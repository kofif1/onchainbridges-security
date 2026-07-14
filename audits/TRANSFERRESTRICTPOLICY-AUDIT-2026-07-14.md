# TransferRestrictPolicy - Verify + AI-audit Report

**Contract:** `TransferRestrictPolicy` (BUSL-1.1, additive ACE policy; Phase-1 primitive #3)
**Source:** `github.com/kofif1/OnchainBridges-RWA-testnet` @ `78cba7a` (PR #216; deploy from exactly that commit)
**Author lane:** Token Controls (`local_2c9a005c...`) | **Gate:** Session 8, 2026-07-14

## Sepolia (11155111) - single-deploy-driver, no orphan
| Implementation | Proxy |
|---|---|
| `0xC68c3f0beeEeA72e76F4A533d2f3fd731934c3A5` (block 11267728) | `0x01B887BA384f1A51D29CB2BdA9117B2C1f03919d` (block 11267729) |

Gate-confirmed live reads: globalLockupEnd = 0 + empty blocklist (fully no-op), owner `0xFc99...`
(Safe migration gate), codesizes 4103/130, impl slot correct.

## Verification
Etherscan V2, solc 0.8.24, opt 200, viaIR. Impl "Pass - Verified"; proxy bytecode
already-verified ERC1967Proxy, constructor args cross-checked (same initCall shape, impl slot differs).

## AI-audit (Slither, FOUNDRY_PROFILE=ccid)
**0 Critical / 0 High / 0 Medium / 1 Low / 18 Informational.**
The single Low = `timestamp` (block.timestamp lockup comparisons) - **by-design and pre-flagged**:
lockups operate at day/week granularity where miner drift is immaterial (same class as TLPTFaucet,
precedent in SECURITY-REVIEW-2026-06-17). By-design caveats: owner-controlled lockups/blocklist
(compliance-controls token); policy freezes but cannot move funds (pull-out = Phase 2 forcedTransfer).

**Gate verdict: CLEARED for attach** (transfer + transferFrom, `[keccak256("from"), keccak256("to")]`,
append at index 4 -> 5-policy transfer chains; extractor drift-checked at the MaxBalance attach).
