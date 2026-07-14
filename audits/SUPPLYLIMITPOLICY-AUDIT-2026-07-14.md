# SupplyLimitPolicy - Verify + AI-audit Report

**Contract:** `SupplyLimitPolicy` (BUSL-1.1, additive ACE policy; clean-room from ERC-3643 SupplyLimit semantics)
**Source:** `github.com/kofif1/OnchainBridges-RWA-testnet` @ `d69ef7e` (PR #215, merged 2026-07-14; deploy from main `7bfe684`)
**Author lane:** Token Controls channel (`local_2c9a005c-ca33-4b5e-bc01-65b09c6a3435`)
**Coordinator gate:** RWA Bridge Session 8 (`local_69dd2023-bfaf-46ac-a58e-8307a4e78912`), 2026-07-14
**Ledger row:** `../CONTRACT-VERIFICATION-AUDIT-LEDGER.md` Sepolia section

## Deployed addresses on Sepolia (11155111) - single-deploy-driver followed, no orphan

| Role | Implementation | Proxy |
|---|---|---|
| Authoritative | `0x33Bad648E9F8274aaeD074D9A5033c7893Ac78bA` (block 11267602) | `0x0582AeDF2CD32716BCB67EdD2352287fFC11FBa5` (block 11267603) |

Live-state reads (independently confirmed by the gate): `maxSupply()` = 2^256-1 (no-op default),
`owner()` = `0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c` (deployer; Safe migration under
`policy_mainnet_secure_signing_gate`), `typeAndVersion` = "SupplyLimitPolicy 1.0.0", impl slot correct.

## Verification
Etherscan V2 (`chainid=11155111`), solc 0.8.24, optimizer 200, viaIR. Impl verified
("Pass - Verified"); proxy = standard OZ ERC1967Proxy, bytecode already-verified, constructor
args cross-checked against `run-latest.json` (initCall = same engine `0xd4b9F980...` + owner
`0xFc99...` shape as MaxBalancePolicy, impl slot differs).

## AI-audit (Slither 0.11.x, FOUNDRY_PROFILE=ccid)
**0 Critical / 0 High / 0 Medium / 0 Low; 18 Informational** (dependency/naming conventions,
same profile as the MaxBalancePolicy run). Manual review notes:
- Mint-selector-only attach with `[keccak256("amount")]` is correct: mint is the only
  supply-increasing policy-checked op (burn is not policy-checked; transfers do not change supply).
- Pre-mint `totalSupply()` read documented and correct (`runPolicy` executes before
  `_totalSupply` increments), so `totalSupply() + amount > maxSupply` is the right check.
- ERC-7201 namespaced storage; deploys at `maxSupply = uint256.max` -> attach is behavior-neutral.
- Complementary to (not replacing) the RWATokenCCT PoR reserve check; either can reject a mint.

**By-design centralization caveat:** owner-controlled `setMaxSupply` (single admin function;
smaller surface than MaxBalancePolicy - no exemption list). Owner is the deployer EOA pending
the Safe migration gate.

**Gate verdict: CLEARED for attach** (mint selector only, append at index 4 ->
`[AllowPolicy, PausePolicy, Validator, MaxBalance, SupplyLimit]`).
