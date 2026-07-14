# MaxBalancePolicy - Verify + AI-audit Report

**Contract:** `MaxBalancePolicy` (BUSL-1.1, additive ACE policy)
**Source:** `github.com/kofif1/OnchainBridges-RWA-testnet` @ `78d86e5`
**Author lane:** Token Controls channel (build session `local_2c9a005c-ca33-4b5e-bc01-65b09c6a3435`)
**PR:** [kofif1/OnchainBridges-RWA-testnet#210](https://github.com/kofif1/OnchainBridges-RWA-testnet/pull/210) (merged 2026-07-11 23:08 UTC)
**Coordinator gate:** RWA Bridge Session 8, `local_69dd2023-bfaf-46ac-a58e-8307a4e78912`, 2026-07-12
**Ledger row:** see `../CONTRACT-VERIFICATION-AUDIT-LEDGER.md` Sepolia section

## Deployed addresses on Sepolia (11155111)

Two deploys landed inadvertently (single-deploy-driver process not followed for this first
primitive; captured for future primitives). Both bytecode-identical, both explorer-verified.
Only the second pair goes forward.

| Role | Implementation | Proxy | Status |
|---|---|---|---|
| Authoritative (nonce 611/612, blocks 11254823/824) | `0x7E1ac7C5F58308d22bAe944B0061A932085B9914` | `0xe2e7Dce33380EfD675FEc42B4202612F29923167` | verified + audited; slated for attach on MBT |
| Orphan / superseded (nonce 609/610, blocks 11254703/704) | `0x789aA37bf2ef02946e722bdBC1300AcB07B1594b` | `0xe221AeB60647e84947a7c193698afe8384716370` | verified for audit trail; NEVER attached, NEVER wired |

**Impl codehash (identical across both):** `0xdda351ae092a3a5b28456d04bc4867e7542b8256ce327c88772a8f58b2d460da` (Token Controls reported, independently confirmed).

Live-state reads (both proxies):
- `cap()` = `2^256 minus 1` (empty configure default, no-op mode)
- `owner()` = `0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c` (deployer; Safe migration under `policy_mainnet_secure_signing_gate`)
- `typeAndVersion()` = `MaxBalancePolicy 1.0.0`

## Explorer verification

Verified via Etherscan V2 unified API (`api.etherscan.io/v2/api?chainid=11155111`), solc 0.8.24, optimizer 200, viaIR (matches the `FOUNDRY_PROFILE=ccid` posture the deploy used).

- Impl (authoritative): [sepolia.etherscan.io/address/0x7E1ac7C5F58308d22bAe944B0061A932085B9914#code](https://sepolia.etherscan.io/address/0x7E1ac7C5F58308d22bAe944B0061A932085B9914#code) : Pass, Verified 2026-07-12.
- Impl (orphan): [sepolia.etherscan.io/address/0x789aA37bf2ef02946e722bdBC1300AcB07B1594b#code](https://sepolia.etherscan.io/address/0x789aA37bf2ef02946e722bdBC1300AcB07B1594b#code) : auto-matched via identical bytecode.
- Proxy (authoritative): [sepolia.etherscan.io/address/0xe2e7Dce33380EfD675FEc42B4202612F29923167#code](https://sepolia.etherscan.io/address/0xe2e7Dce33380EfD675FEc42B4202612F29923167#code) : auto-matched as OpenZeppelin ERC1967Proxy.
- Proxy (orphan): [sepolia.etherscan.io/address/0xe221AeB60647e84947a7c193698afe8384716370#code](https://sepolia.etherscan.io/address/0xe221AeB60647e84947a7c193698afe8384716370#code) : auto-matched as OpenZeppelin ERC1967Proxy.

## Slither static-audit (2026-07-12)

Ran Slither 0.11.4 against the impl source with explicit remappings (`@openzeppelin/contracts/` to `lib/openzeppelin-contracts/contracts/`, `@chainlink/policy-management/` to `lib/chainlink-ace/packages/policy-management/src/`, `@chainlink/contracts/` to `lib/ccip/contracts/`). 10 contracts analyzed via 100 detectors.

**Result: 0 High, 0 Medium, 0 Low, 18 Informational.**

Category breakdown:
- 11 naming-convention (in transitive deps + one cosmetic in MaxBalancePolicy: `MaxBalancePolicyStorageLocation` constant follows the OpenZeppelin `OwnableStorageLocation` naming convention rather than `UPPER_CASE_WITH_UNDERSCORES`; deliberate for consistency with the ERC-7201 ecosystem pattern).
- 4 assembly (deliberate, standard ERC-7201 storage-pointer helper: MaxBalancePolicy's `_s()` uses `assembly { s.slot := MaxBalancePolicyStorageLocation }`, identical to the OpenZeppelin Ownable/Initializable pattern).
- 2 solc-version (OpenZeppelin's `IERC20` and `IERC165` interfaces pin `>=0.4.16`; unavoidable given upstream).
- 1 pragma (multiple `^0.8.20`/`^0.8.24` across the dep graph; not a defect).

Full JSON report retained locally at `scratch/token-controls/maxbalance-slither.json` (not committed, transient scratch class per `policy_file_hygiene_three_classes`).

## Coordinator source review (2026-07-12)

Read the verified source on Sepolia Etherscan (matches the PR #210 head commit). Notes:

- **Owner-only admin surface**: `setCap(uint256)`, `setExempt(address,bool)`, `setExemptBatch(address[],bool)`. **By-design centralization** per the RUNBOOK section 0 caveat: MaxBalancePolicy is a compliance control, owner-controlled cap adjustment is the point. Owner = deployer `0xFc99...` on testnet, migration to Safe/timelock under `policy_mainnet_secure_signing_gate` before mainnet, same posture as AllowPolicy / PausePolicy / ComplianceCheck.
- **Overflow discipline**: `run()` checks `amount > cap` first, then `balanceOf(to) > cap minus amount` (safe subtraction because `amount <= cap`); never computes `balanceOf(to) + amount` directly. Confirmed on the verified source.
- **Reentrancy**: `run()` is `view`; admin setters are pure state writes with no external calls after mutation. Not applicable.
- **Access control**: `onlyOwner` on all setters, inherited from OpenZeppelin `OwnableUpgradeable` via the `Policy` base. Confirmed.
- **Bypass surface**: attaches via `PolicyEngine.addPolicy` at the token selector level. Cannot be bypassed on `transfer`/`transferFrom`/`mint` once attached because ACE runs it inline; the `run()` view function returns a `PolicyResult` that the engine acts on. Attach step is a follow-up PR (Token Controls owns).
- **Cross-references**: extractor dependency (needs `keccak256("to")` + `keccak256("amount")` from ERC20TransferExtractor + ERC20MintExtractor) confirmed present on the deployed PolicyEngine already; no new extractor needed.
- **Test coverage**: 28/28 unit tests pass under `FOUNDRY_PROFILE=ccid` (PR body). Tests cover cap enforcement, exemption logic, admin authorization, reversibility via detach.
- **Reversibility**: initial `cap = type(uint256).max` is a no-op; `PolicyEngine.removePolicy` unwinds cleanly. Attach with no-op cap first, then `setCap` live only after coordinated review.

## Findings summary

| Severity | Count | Notes |
|---|---|---|
| Critical | 0 | |
| High | 0 | |
| Medium | 0 | |
| Low | 0 | |
| Informational | 18 (Slither) | All in transitive deps or cosmetic (documented above) |

## By-design caveats to carry into ledger

1. Owner-controlled `setCap`, `setExempt`, `setExemptBatch` = compliance-control centralization, expected, not a finding.
2. Owner = deployer `0xFc99...` = testnet posture; Safe/timelock migration before mainnet per `policy_mainnet_secure_signing_gate`.

## Follow-ups (not in this audit scope)

- Token Controls stages three `PolicyEngine.addPolicy(mbt, selector, proxy, [keccak256("to"), keccak256("amount")])` calls (transfer / transferFrom / mint) against the authoritative proxy after this ledger row lands; Ilan executes.
- Post-attach `getPolicies` reads to be independently re-verified by the CCID channel (Validator `0xdAbf8046` should remain at index 2, MaxBalance at index 3).
- `setCap` live for a real number is a separate coordination window (must exceed largest single onboarding mint per wallet, circulate to Agents / CCID / demo before setting).

## Sign-off

Verified + AI-audited by RWA Bridge Session 8 (coordinator), 2026-07-12 EDT. Both deployed pairs on Sepolia are explorer-verified; authoritative pair is cleared for Token Controls' follow-up attach step; orphan pair recorded for audit trail and will never be attached.

