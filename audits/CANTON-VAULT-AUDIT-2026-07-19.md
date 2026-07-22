# Canton EVM-side Phase-2 Gated Vault, Security Review (2026-07-19)

**Contracts (Sepolia, chain 11155111):**
- `CantonLockVault` = `0xFE4a573C780DA0F895530ED609FDa7e3b833E15f`
- `LockDepositorExtractor` = `0xbEa509a63035322D3117BcdDaa5A969ab4F53203`
- `CantonBridgeOutbox` = `0x0d21fb470c8577Df126db7470c9F843cA378B360` (FRESH, deployed 2026-07-20 for the gated vault; identical #259 source, Etherscan-verified, getForwarderAddress()=0xF8344CFd, owner()=0xFc99, isAttested all-false = clean nonce space). Replaces the prior `0x89f38a88` to avoid a nonce collision: that outbox had attested[1..7] from the old vault's locks, and CantonBridgeOutbox keys on the raw lockNonce + reverts duplicates, so the new vault (restarts at nonce 0) needed a clean outbox. Caught by Canton pre-E2E.

**Source:** PR #259 (merged to `main` at `634fb73`), `src/bridge/canton/`. SPDX MIT (original OB
authorship; clean-room vs any T-REX / ERC-3643 source per `policy_no_copying_copyrighted_code`).

**Method:** manual source review (pre-merge on #259, all files) plus Etherscan-V2 verified source
(public bytecode match) plus on-chain wiring reads plus on-chain behavioral enforcement proof via
`eth_call` simulation. Not a substitute for a professional audit (pre-mainnet, testnet-only).

**Auditor:** RWA Bridge Session 8 (coordinator).

---

## Findings: 0 Critical / 0 High / 0 Medium

### Design strengths confirmed
- **Compliance gate precedes value movement.** `lock()` calls `policyEngine.run(...)` on the
  depositor (`payload.sender`, surfaced by `LockDepositorExtractor`) BEFORE `token.safeTransferFrom`.
  A non-allowlisted caller reverts before any GGBR moves. The vault-level gate (owner-independent) is
  the correct pattern for GGBR (a faithful third-party clone with a deny-list, no positive allowlist).
- **The number-one audit item is now satisfied.** The in-source WARNING, that if no policy is bound to
  (vault, lock selector) then `run()` returns silently with NO enforcement, is closed: `setExtractor`
  plus `addPolicy` are on-chain (see proof below) and `getPolicies(vault, 0xf643509c)` = `[AllowPolicy]`.
- **Return leg is CEI, replay-guarded, forwarder-gated.** `_processReport` (ReceiverTemplate,
  forwarder-only) checks `processedBurns[burnNonce]` plus per-tx / daily caps, sets state before the
  external `safeTransfer` (checks-effects-interactions), emits `Released`.
- **Deterministic recipient commitment.** `destRecipient = keccak256(bytes(cantonParty))` keeps the
  DON report shape identical to Stacks / CCT; `encodeCantonRecipient` is a pure mirror for off-chain.

### Low / follow-up (documented, not blocking the one-way MVP)
- **Return-leg caps = `type(uint256).max` and recipient not ACE-gated.** Deployed unlimited; the
  in-source NOTE already flags that `_processReport` must gate `recipient` through `policyEngine.run`
  (mirroring `lock`) and caps must be set to real values BEFORE the Canton to EVM return path is ever
  enabled. Dormant plus forwarder-gated in the one-way MVP, so acceptable today. **Hard prereq before
  enabling return.** Tracked in `todo_onchain_credential_enforcement_gap`.

## On-chain wiring (verified, Sepolia)
- `vault.token()` = GGBR `0x9902D67Ac10a065e43Da17Bed31D3b99eBD1887F`
- `vault.policyEngine()` = ACE PolicyEngine `0xd4b9F980f09f0871C753a7d558B5c500DA1617a3`
- `vault.lockNonce()` = 0 (fresh) **at time of audit, 2026-07-19. SNAPSHOT: reads 1 as of 2026-07-22 after one legitimate outbound lock; the outbound leg is in normal use. Neither this nor `isAttested` affects the findings or the two return-leg preconditions, which were re-confirmed on 2026-07-22 (`vault.policyEngine()` still `0xd4b9F980...`)**; return caps = uint256.max (dormant)
- `extractor.LOCK_SELECTOR` = `0xf643509c` = `bytes4(keccak256("lock(uint256,string)"))`
- `extractor.PARAM_DEPOSITOR` = `keccak256("depositor")` = `0x5bf3f581aca7878c78a6920df3b2a5e73280e8f24fe596cd1cf839a75d3d786d`
- Deployed by `0xFc99...Fd33c`, block 11309903 (extractor tx `0xbefff00e...`, vault tx `0xdaae440c...`)

## Gate activation (0xFc99, ADMIN_ROLE on the engine, verified)
- `setExtractor(0xf643509c, extractor)`, tx `0x6d22bd80fcece0a6b7a4bb61fa559b12edea56696b96f4b2fbbbc9fe2d3f3792` (block 11310007)
- `addPolicy(vault, 0xf643509c, AllowPolicy 0x7858b6e6..., [keccak256("depositor")])`, tx `0x4a4fd22be76a85c6168fd8492ec50e3f67d89fc0fe302e2d6bafe63f2cd681f4` (block 11310025)
- Post-activation: `getExtractor(0xf643509c)` = extractor; `getPolicies(vault, 0xf643509c)` = `[0x7858b6e699c381583A8A820F52AF051197B3DCD1]`

## Behavioral enforcement proof (eth_call lock() simulation)
- **Non-allowlisted sender** (`0x...dEaD`): REVERTS, error data decodes to ASCII `"address is not on
  allow list"` (ACE AllowPolicy rejection). Gate BLOCKS.
- **Allowlisted sender** (`0x5164...`): passes the ACE policy, reverts only at
  `ERC20InsufficientAllowance(vault, 0, 1e18)` (fresh vault, no GGBR approval yet). Gate PASSES.

## Verdict
**PASS** for the one-way (Sepolia to Canton) MVP: the outbound `lock` compliance gate is deployed,
verified, bound, and proven enforcing. The return (Canton to EVM) path stays gated behind the
documented prereqs (recipient ACE-gating plus real caps) before it may be enabled.
