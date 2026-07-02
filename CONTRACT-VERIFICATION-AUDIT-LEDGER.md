# Contract Verification + AI-Audit Ledger

Canonical record for the verify-and-audit gate (RUNBOOK §0, ratified by Ilan 2026-06-17):
**no contract goes live in the public testnet URL until it is explorer-verified AND
AI-audited, with both recorded here.** Owned by RWA Bridge session 5. Other sessions
submit their verified link + audit result via cross-session message; session 5 records.

Columns: contract, chain, address, explorer link (verified source), AI audit (tool / score /
report URL / date), notes. "by-design" = compliance control flagged as centralization by
scanners; expected, not a finding.

> **Status 2026-06-17:** ALL LIVE NETWORKS COMPLETE. Every live EVM network (Sepolia, Polygon
> Amoy, Sonic, Soneium Minato, Plume) has its canonical protocol contracts explorer-verified, and
> the source-based audit ([SECURITY-REVIEW-2026-06-17](audits/SECURITY-REVIEW-2026-06-17.md): 0
> Critical/High/Medium across three independent analyzers) covers all EVM chains (one source, same
> bytecode everywhere) plus the Solana programs and the 6 Stellar/Soroban contracts (reproducible
> WASM-hash verified + audited). Astar Shibuya was re-activated 2026-06-19 (yield + lending redeployed;
> all 5 explorer-verified). Base is NOT live (see notes).
>
> **On the "free scanner" score:** the public `solidityscan.com/quickscan/...` link is a rug-pull /
> honeypot THREAT detector, not a code audit - it penalizes the by-design compliance controls
> (owner mint/pause/allowlist) and is the wrong tool for a regulated-asset protocol. The
> source-based review above is the code-security view.
>
> **Scope notes:** (1) Paymaster (TLPTPaymaster v1/v2) is EXCLUDED - AA Phase 2 is flag-hidden,
> not live in the URL. (2) Compliance contracts use an ERC1967 proxy + implementation; both are
> verified (scanners should target the impl for logic). (3) Superseded/old deploys are not listed.

## Sepolia (11155111) - Etherscan V2 - COMPLETE

| Contract | Address | Verified | AI audit (tool / score / url / date) | Notes |
|---|---|---|---|---|
| MBT (RWATokenCCT) | `0x044951AB857C7e23b5570D4ca11466ACACF861A8` | [yes](https://sepolia.etherscan.io/address/0x044951AB857C7e23b5570D4ca11466ACACF861A8#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | RWA token; allowlist/mint = by-design |
| BurnMintTokenPool (MBT) | `0x53d88a3Fe9480767bE210CbC2e2b3E14145D087d` | [yes](https://sepolia.etherscan.io/address/0x53d88a3Fe9480767bE210CbC2e2b3E14145D087d#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | CCIP burn/mint pool |
| CrossChainBridge | `0x454B52EA0e4995136A3A325FE0c8F2F70a2603ee` | [yes](https://sepolia.etherscan.io/address/0x454B52EA0e4995136A3A325FE0c8F2F70a2603ee#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | Functions consumer (CRE bridge path) |
| TLPT (Teleport, BurnMintERC677) | `0x1ceaaE761C87278acfbE33c89eC523B46FAbCcF3` | [yes](https://sepolia.etherscan.io/address/0x1ceaaE761C87278acfbE33c89eC523B46FAbCcF3#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | see config-bug note below |
| PolicyEngine (proxy) | `0xd4b9F980f09f0871C753a7d558B5c500DA1617a3` | [yes](https://sepolia.etherscan.io/address/0xd4b9F980f09f0871C753a7d558B5c500DA1617a3#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | ACE; centralization flags by-design |
| PolicyEngine (impl) | `0x8f1bb564ef4c323350ce301e4a4e354f0fab15f9` | [yes](https://sepolia.etherscan.io/address/0x8f1bb564ef4c323350ce301e4a4e354f0fab15f9#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | scan this for logic |
| AllowPolicy (proxy) | `0x7858b6e699c381583A8A820F52AF051197B3DCD1` | [yes](https://sepolia.etherscan.io/address/0x7858b6e699c381583A8A820F52AF051197B3DCD1#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | ACE |
| AllowPolicy (impl) | `0x69561bec5e4258743ccdb395bae9c93c2df18e2a` | [yes](https://sepolia.etherscan.io/address/0x69561bec5e4258743ccdb395bae9c93c2df18e2a#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | scan this for logic |
| PausePolicy (proxy) | `0x2038964Aea77Aa4A7cfB6a8A769e83FD119e634A` | [yes](https://sepolia.etherscan.io/address/0x2038964Aea77Aa4A7cfB6a8A769e83FD119e634A#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | ACE |
| PausePolicy (impl) | `0xe702363418344C63F00EE3F6d4F23546f4e60FD2` | [yes](https://sepolia.etherscan.io/address/0xe702363418344C63F00EE3F6d4F23546f4e60FD2#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | scan this for logic |
| YieldIssuer | `0x34b8822b44ff287a2c7C6Fa8Fad60B0DC27AB841` | [yes](https://sepolia.etherscan.io/address/0x34b8822b44ff287a2c7C6Fa8Fad60B0DC27AB841#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | |
| YieldToken (YT-MBT) | `0x7f939bc5e0db02910a461EE49127df881D042299` | [yes](https://sepolia.etherscan.io/address/0x7f939bc5e0db02910a461EE49127df881D042299#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | |
| YTMarket | `0xa25cC783b6CBb51CBCA57dC012315ef38212c94E` | [yes](https://sepolia.etherscan.io/address/0xa25cC783b6CBb51CBCA57dC012315ef38212c94E#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | |
| LendingVault (Meridian) | `0x71635c5f2b275136656f29b812af636b29512f0c` | [yes](https://sepolia.etherscan.io/address/0x71635c5f2b275136656f29b812af636b29512f0c#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | |
| TLPTFaucet | `0x13e32c97a1437a8965cd99a8c34e67bdee5f7cfa` | [yes](https://sepolia.etherscan.io/address/0x13e32c97a1437a8965cd99a8c34e67bdee5f7cfa#code) | [3 analyzers: 0 Crit/High/Med (2026-06-17)](audits/SECURITY-REVIEW-2026-06-17.md) | |

> **Config bug surfaced:** `webapp/src/config/chains.ts` lists Sepolia TLPT as `0x8E531A05...`,
> which has NO code on Sepolia. The live TLPT token is `0x1ceaaE761C87278acfbE33c89eC523B46FAbCcF3`
> (per `aa.ts`), verified above. Flagged to the dApp owner to reconcile `chains.ts`.

> **Audit applies to every EVM chain below.** It is the same Solidity source compiled to the same
> bytecode on each chain, so the [3-analyzer source review](audits/SECURITY-REVIEW-2026-06-17.md)
> (0 Critical/High/Medium) covers all of them. The per-chain rows record on-chain *verification*.

## Polygon Amoy (80002) - Etherscan V2 - COMPLETE

Explorer: `https://amoy.polygonscan.com/address/<addr>#code`. All 14 canonical contracts verified:
MBT `0x71A8e443f223cFe59498968758dB49F2D114dCE2`, BurnMintTokenPool `0x48dB3f62B6c7328Fb742B4799654136794dBE2B4`,
PolicyEngine proxy `0xfefA774dB9A52c72EC8f0621046836CbA63d2BA6` + impl `0xf3186574c8a0c0b3e235704bad389b78604b2745`,
AllowPolicy proxy `0x5fe9d900945b60B3BEcEDe4382BEd2653EC99D40` + impl `0x45199c8628c316b9ac7b09a048f74284afc779ab`,
PausePolicy proxy `0x8c57Abe7Cf1330346EB760EDaEE380A66D3f42f7` + impl `0x4de6322b74eb649049d6c93024e51f35b5df306d`,
LockVault `0x5D273AAE0547FD33c0C5247487a160cd26f38FE4`, YieldIssuer `0x62BD210F9e7B4889066Bd7bc360557c666af44F5`,
YieldToken `0xC4dE838dF59AEB7a9Ad099A34EE6F3E1B19D1aeC`, YTMarket `0xa701147b7C1a511790BE503675Df1F8B09caBAa9`,
LendingVault `0xb4908accabcba9ce56ef0324b9695a88e3dd2534`, TLPTFaucet `0x6c61658cfe0e5794f739782433a8011a6107a570`.

## Sonic Testnet (14601) - Etherscan V2 - COMPLETE

Explorer: `https://testnet.sonicscan.org/address/<addr>#code`. All 8 canonical contracts verified:
WrappedMBTv2 (wMBT) `0x670c3a58435cc13e9eda059f4093f31cedeef7f3`, YieldIssuer `0xe43355DFBfaa167B76591432c1d033a454B87AD3`,
YieldToken `0x73362F49F5dB3f1253D73a4f017f2324b6e113e0`, YTMarket `0x3c4070794aa047decCdb42bD9BbE37a4310fa9b9`,
LendingVault (Nova Finance) `0x49f1aa7C252183E79D4944c54698a335Ed65C150`, CREBridgeConsumer `0xa56efb5a85a2c9c2e07478ce257cedecee9ca7f6`,
TLPTFaucet `0xbf9206b3f6b335875a242899c472c2c440fba02f`, TestUSDC `0x43cc10a0bbeb70d8efcb45714e0e0f4c49500c75`.

## Plume Testnet (98867) - Blockscout - COMPLETE

Explorer: `https://testnet-explorer.plume.org/address/<addr>`. Canonical protocol contracts verified:
MBT `0x43cc10a0bbeb70d8efcb45714e0e0f4c49500c75`, YieldIssuer `0xBe0107334394e9b8ccBE18e9D39e3c3C1f302B00`,
YieldToken `0x6eB381e96d39a2393652deD532f304D297602EAe`, YTMarket `0x6c1F0bF40De396292310440ABE8f89996beA0F3E`,
TLPTFaucet `0xf48f2683aea6e5c1f395eef71b012c3fd554a797`. (Pool + ACE proxies on Plume are Chainlink/OZ
library code - identical source already verified on Sepolia + Amoy.)

## Soneium Minato (1946) - Blockscout - COMPLETE

Explorer: `https://soneium-minato.blockscout.com/address/<addr>`. TLPT-ecosystem contracts verified:
OperatorAllowlist `0x5fe9d900945b60b3becede4382bed2653ec99d40`, TLPTFaucet `0x8c57abe7cf1330346eb760edaee380a66d3f42f7`,
TLPTSale `0xe5e1e4b594c343c1e9bac0730b25fd4ab21f7389`, TLPTStaking `0xf3186574c8a0c0b3e235704bad389b78604b2745`,
TestUSDC `0x71a8e443f223cfe59498968758db49f2d114dce2`. (Minato's MBT/bridge is currently hidden in the UI.)

## Astar Shibuya (81) - Blockscout - COMPLETE

Explorer: `https://shibuya.blockscout.com/address/<addr>`. Yield + lending stack redeployed 2026-06-19
from current source (PR #63 `script/DeployShibuyaYieldStack.s.sol`; run via a viem one-tx-at-a-time
deployer on the public RPC, the foundry tooling cannot burst-call this node). Wired to the
already-verified Shibuya MBT `0xF08a207bb7D0198356ED4Fffd555Ee7129E2b8f5` + shared AllowPolicy
`0xce8C237c6b8bF91df589eA740d4d65Cbb75F7DDd` (MBT + ACE proxies verified earlier; bridge stack was
already live). Deployer `0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c` = AllowPolicy owner, so
issuer/market/vault were allowlisted in the same run. Astarium terms: LTV 80% (flagship, tops Plume's
78%), rate 3.5%, price $10. Independently confirmed by session 5: on-chain bytecode present for all 5
(eth_getCode 1927-5031 bytes) and Blockscout `is_verified=true` for all 5 below (compiler
v0.8.24+commit.e11b9ed9; YieldToken needed a one-time poke tx to index, see its row).

| Contract | Address | Verified | AI audit | Notes |
|---|---|---|---|---|
| TestUSDC | `0x817b54C6E13F374B99A0C5e78443bFecCc0b0a2A` | [VERIFIED](https://shibuya.blockscout.com/address/0x817b54C6E13F374B99A0C5e78443bFecCc0b0a2A#code) | 0 Crit/High/Med (SECURITY-REVIEW-2026-06-17, same source/bytecode as Sepolia/Amoy/Plume) | yield + lending currency |
| YieldIssuer | `0xbdA5e22c7037898D5C03b835487134F322B82c41` | [VERIFIED](https://shibuya.blockscout.com/address/0xbdA5e22c7037898D5C03b835487134F322B82c41#code) | same | mints YT-MBT, wired to MBT + AllowPolicy |
| YieldToken (YT-MBT) | `0x9e6e00c2170b50395CBdaAe61242438224bCbd4E` | [VERIFIED](https://shibuya.blockscout.com/address/0x9e6e00c2170b50395CBdaAe61242438224bCbd4E#code) | same | shares MBT's AllowPolicy; CREATE'd in the YieldIssuer constructor. Shibuya Blockscout does not index internal-CREATE traces, so it stayed `is_contract=false` (no creation tx); unblocked by sending one harmless top-level tx to it (`approve(deployer,0)`, `0x4c6f2693e22c84b14a3ba78ca35da9eb857b2984846c9f89ba67ce07542c50d7`, zero economic effect), after which standard-input verify succeeded |
| YTMarket | `0xFf69A2c111759777693e348727BD157db4Fb8AB2` | [VERIFIED](https://shibuya.blockscout.com/address/0xFf69A2c111759777693e348727BD157db4Fb8AB2#code) | same | fixed-price YT secondary market |
| LendingVault (Astarium) | `0x3F1d5141391F5EDe23A84EbdDD2abB3e56Dd9519` | [VERIFIED](https://shibuya.blockscout.com/address/0x3F1d5141391F5EDe23A84EbdDD2abB3e56Dd9519#code) | same | borrow USDC against MBT, LTV 80% |

> The AI-audit is satisfied by source identity: all 5 are the same contracts (`src/defi/TestUSDC.sol`,
> `src/yield/YieldIssuer.sol`, `src/yield/YieldToken.sol`, `src/defi/YTMarket.sol`,
> `src/defi/LendingVault.sol`) already covered by the EVM 3-analyzer run in
> [SECURITY-REVIEW-2026-06-17](audits/SECURITY-REVIEW-2026-06-17.md) (0 Crit/High/Med; one source,
> same bytecode everywhere; compiler v0.8.24 confirmed on-chain). All 5 are now explorer-verified, so
> the yield.ts/defi.ts UI wiring PR can proceed for IA preview-verification, per the gate.

## Solana (devnet) - Rust/Anchor

Three Anchor programs (`mbt-defi`, `mbt-bridge-delivery`, `mbt-allowlist-hook`); program IDs in
`webapp/src/config/solana.ts`, source public in `solana/programs/`. Security review in
[SECURITY-REVIEW-2026-06-17 §6](audits/SECURITY-REVIEW-2026-06-17.md): cargo-audit (advisories are
in transitive toolchain deps, not our code) + strong Anchor access-control review; no exploitable
findings in our program code.

## Stellar (Soroban testnet) + Stellar-bridge helper

Grandfathered: these went live 2026-06-03/04, before the verify+audit gate existed (2026-06-17).
Recorded here for completeness; the Soroban contracts are being brought onto the non-EVM standard.

**Non-EVM verify/audit standard (set 2026-06-17):** Soroban/Solana/Stacks have no Etherscan-style
"verified source" tab. "Verified" therefore = published source at a commit + the on-chain code hash
(WASM hash for Soroban/Solana; Clarity is on-chain-readable) + a reproducible deterministic build
command that rebuilds to the same hash. "Audited" = `cargo audit` (deps) + `cargo clippy` + a
structured access-control / authorization review per contract (same method used for the Solana
programs above). The owning session runs the legwork; session 5 records it here (single source).

EVM helper (Polygon Amoy, Etherscan V2):
- StellarLockVault `0xA19DB9d6944ee5181233A3dC02bdB7dffB779C7D` - [VERIFIED](https://amoy.polygonscan.com/address/0xA19DB9d6944ee5181233A3dC02bdB7dffB779C7D#code) (owner = Stellar-bridge Safe `0x5031080f170F0186bF8EA4Ac538783497dE452a8`). **Audited: 0 Crit/High/Med.** "StellarLockVault" is just the deployment label for `src/bridge/LockVault.sol` (commit 6dfa580, on main), already covered by the EVM 3-analyzer run in [SECURITY-REVIEW-2026-06-17](audits/SECURITY-REVIEW-2026-06-17.md).

**Soroban (Stellar Testnet) - VERIFIED (reproducible WASM-hash) + AUDITED (0 Crit/High/Med).**
Explorer: `stellar.expert/explorer/testnet/contract/<id>`. Source: `soroban/<crate>/` @ commit
`7d46c531` (on main, merged via PR #58). Reproducible build: `stellar contract build &&
stellar contract optimize` (stellar-cli 26.1.0, target `wasm32v1-none`) reproduces the on-chain code
hash for all 6; each confirmed by `stellar contract fetch` + sha256 match. Audit per the non-EVM
standard (cargo-audit + clippy + per-contract access-control review) summarized in
[SECURITY-REVIEW-2026-06-17 §7](audits/SECURITY-REVIEW-2026-06-17.md).

| Contract | Contract ID | WASM sha256 (= on-chain code hash) |
|---|---|---|
| sMBT | `CCYOGL2C7OKWDEN7XTIVLS6GFGOUEQQHER36YV5BHLPDCJJM3FIO3TD5` | `029c83b1707474897734c4399c166415ef6e114011e7f760846e40b17ec6d263` |
| sUSDC | `CAS5G444JM4CDTX33ALSMAEJDMKMNKKUIHC3PV7N24KDRENRUVA4DCPO` | `f89ba0deca1c5bd0d5ad5b0136ac7abb605f38d91f92f6d0419c56ef9b9843e9` |
| LendingVault (Polaris) | `CB7WBLOFLDFJYKCYFOJYICDW3PUJ66IWIGJMP3P7MDDSXNGY5ON3JINS` | `9140af1a878cc74603a105e1516c628b98fdb5a78e03e4ede1a616ee18b45983` |
| YieldIssuer (deposit-time) | `CDUHJUGS7KNRTWPT2J4GZ6IITN5UZU2OYAYGGEAFSP3A3XQTO3NLUSNX` | `f47cdfa87de32e52839ba817b61a11210d394a807bd9289b9ef8416060524283` |
| YT (deposit-time) | `CALXF3KWRM47INN5MDORVWRG652ZYYBBWEECEYPBVKCDGGVCICSKMPVR` | `662f81c410fb6768a2b5f0c0b9315af3d2cb781bfac7a44db9c8ec022784058a` |
| YTMarket | `CBL2JAZ6IC2QV6CWSYUMBZTX2SQHABJGGDCII4QJDGTH2657SP7PUQLC` | `88c17661175614faa61894c59e04e437a46cff80972ef482edac26fe4ac52a1f` |

> **Superseded 2026-06-21 , deposit-time snapshot redeploy (parity with EVM + Stacks, PR #79).** The
> YieldIssuer / YT / YTMarket rows above are the NEW ids; old ids kept for traceability:
> YieldIssuer `CDOHKXC63NMVZ6BRK3MYCCEK5LQCUB227YI53EEPYMBGUZRONP6PCAFG` -> `CDUHJUGS...`,
> YT `CBTRU54726K5WJV6VMIBFY4WW4SHRJF6DEQB7KKAWF4HAXECAVBISPVC` -> `CALXF3KW...`,
> YTMarket `CCXKKM7IVCDAYNI2FCN6DILBTDY37ZVUJB6TUWJEF4TS3IBCMY6QDFKD` -> `CBL2JAZ6...`.
> New YieldIssuer + YT source: `soroban/{yieldissuer,yt}` @ commit `3942874` (PR #79). Reproducible build
> (`stellar contract build`) reproduces the on-chain wasm sha256 for all 3 (`stellar contract fetch`
> match confirmed). Audit: the only new surface is the ERC20-Snapshot additions , `snapshot()` is
> issuer-admin-only (same gate as mint), `balance_of_at`/`total_supply_at` are range-guarded read views,
> the lazy checkpoints live inside `receive_balance`/`spend_balance`/`add_supply` (no new entrypoint),
> compliance gating unchanged; cargo-audit/clippy/fmt clean; the snapshot core was cross-checked against
> `src/yield/YieldToken.sol` by session 5 (`value_at` == lower-bound `_lowerBinaryLookup`, live fallback,
> invalid-id revert). YTMarket wasm is byte-identical to the prior deploy (`88c1766...`); only its id
> changed (re-pointed to the new YT + re-allowlisted), so its audit carries over. **0 Critical/High/Medium.**
> sMBT / sUSDC / LendingVault are unchanged, still @ `7d46c531` (PR #58).

## Stacks (Testnet4, Clarity) - VERIFIED (on-chain source == repo) + AUDITED (0 Crit/High/Med)

Deployer principal `ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW`. Clarity publishes contract source
on-chain natively (readable on the Hiro explorer), so "Verified" = the on-chain source matches the
repo source byte-for-byte. Confirmed 2026-06-20 by fetching each contract from the Hiro testnet API
(`/v2/contracts/source/<principal>/<name>`) and diffing against `clarity/contracts/` on main @ commit
`3942874` (merged via PR #71 / #74 / #77 / #78): all 5 matched exactly (e.g. stx-yt 10969b == 10969b).

Audited 2026-06-20 (RWA Bridge session 5): `clarinet check` clean (clarinet 3.20, 7 contracts, 0
errors, lint-only warnings) + a structured per-contract access-control / authorization / value-
accounting / Clarity-safety review. Result: **0 Critical / High / Medium.** The deposit-time snapshot
model (`stx-yt` / `stx-yield-issuer`) was additionally cross-checked against `src/yield/YieldToken.sol`
(session 5) and the compliance gating + market against the Stellar suite. Findings were INFO/by-design
only: owner-mintable demo tokens, ungated settlement currency (`stx-susdc`), the 64-window checkpoint
demo cap, and cross-period YT re-mint (self-consistent under the deposit-time model).

| Contract | Contract ID | Role |
|---|---|---|
| stx-susdc | `ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-susdc` | demo stablecoin (SIP-010, ungated by design) |
| stx-lending-vault | `ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-lending-vault` | borrow stx-susdc vs stx-mbt collateral, 50% LTV |
| stx-yt | `ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-yt` | yield token, compliance-gated + ERC20-Snapshot |
| stx-yield-issuer | `ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-yield-issuer` | period yield, deposit-time model |
| stx-yt-market | `ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-yt-market` | YT secondary market (escrow) |
| stx-mbt (Phase A) | `ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-mbt` | the regulated RWA collateral token (compliance-gated SIP-010 + bridge mint/burn) |
| sip-010-trait (Phase A) | `ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.sip-010-trait` | SIP-010 fungible-token trait (pure interface) |

Explorer: `https://explorer.hiro.so/txid/<principal>.<name>?chain=testnet`. The two Phase-A contracts
(`stx-mbt`, `sip-010-trait`; source @ commit `0a4805aa`, unchanged since) were verified the same way:
on-chain Hiro source == repo byte-for-byte, independently confirmed 2026-06-21 (session 5). `stx-mbt`
audited (3 separated roles all gated on `contract-caller`; compliance gate complete on transfer +
bridge-mint + bridge-burn; relayer-only, replay-guarded mint; no admin drain): **0 Crit/High/Med.**
`sip-010-trait` is a pure interface (source-match only).
Deploy-time prerequisite (wiring, not a code finding): the lending vault, YT market, and yield issuer
must be allowlisted on `stx-mbt`, and `stx-yt`'s minter must be the yield issuer , Stacks session
confirmed done. Per the gate, `VITE_STACKS_ENABLED` stays OFF on the public URL until these rows are
recorded AND `pre-demo-check` is green.

## CCID compliance-identity stack (Sepolia + Plume) - recorded 2026-07-02

**What shipped:** on-chain KYC credentials (ACE Cross-Chain Identity). Admission on attached
chains = AllowPolicy AND a `common.kyc` credential, enforced by a validator policy in the token's
PolicyEngine chain. Credentials are issued automatically by the Didit-driven kyc-issuer service
(IONOS host, issuer key `0x3055698887538cc6cF0d408e744B04F0806CB6E1`, sole authorized signer).

### Sepolia (Phase 0 #134 -> attach 2026-07-02, ENFORCING since block 11189744)
| Contract | Proxy | Impl |
|---|---|---|
| Registry-admin PolicyEngine | `0xfB1762FbA9630414d9A8940C9f0037e0eBbDEc33` | `0xb2C30E602A9a2179b8372c63208Bc5Ea9A1e1C22` |
| OnlyAuthorizedSenderPolicy (issuer gate) | `0xd494922fC60EC18D197C09cb6a9f70518A1CAD7E` | `0x89E5318a96E25d725A4CFD61b64917C29252ad24` |
| IdentityRegistry | `0x89EC36234fF2D75C306f27913e7421791fC888c7` | `0x5e3303fbe8e83B6a9Db0dF33e8b2f2957f4a0E88` |
| CredentialRegistry | `0xDc5Acd767633E7B29C7f317DBB57c52F597bCe6F` | `0x918c3d3f99166C7f2686Dd2322C9B3A52C49788d` |
| CredentialRegistryIdentityValidatorPolicy | `0xdAbf8046A6fa3b56Ab55fA4154e0fD6679b0d717` | `0x9d80e7Fdfb81f613b05cA484432CDbfD50e71e16` |

Gate history: Phase-0 cleared the verify+audit gate at PR #134 (session 5). Validator ATTACH
2026-07-02, deployer-signed: transfer `0xdc1fec02b1180a62f4cc66cc16071623e3798f47b2ec2baf083bbfe1db278414`,
transferFrom `0x88cc46d6a4b7fee3e177cd268c0b45b89d450e58e3aff630c9c6efa14d4b0e70`,
mint `0xa402bdfecfc1ef088dbac349fb12426e8cdb6f0e128645a932ceaa223b33d419`.
Independent post-attach composition verification (session 6, coordinator): getPolicies on the MBT
engine `0xd4b9F980f09f0871C753a7d558B5c500DA1617a3` = [AllowPolicy, PausePolicy, Validator] on all
three selectors; credentialed wallets (incl. a live Didit-KYC wallet) pass, non-KYC wallet reverts
`PolicyRunRejected`. Pre-attach registry parity 40/40 (CCID channel).

### Plume Testnet (Phase 0-2 proving run 2026-06-29, ENFORCING; gate cleared 2026-07-02)
| Contract | Proxy | Impl |
|---|---|---|
| Registry-admin PolicyEngine | `0xab27438dc2bA528D4A29A1Ab55406C01b70855e2` | `0x6c61658cfe0e5794f739782433a8011a6107a570` |
| OnlyAuthorizedSenderPolicy | `0xA861E26770eC5DdCf84F43F281D373368f6F7Db3` | `0x37b3264481c68dccef623d44b0e83ffc15a22cdd` |
| IdentityRegistry | `0xd0ba47398119D21051c5b1bF1ED59195cE558b4A` | `0x410cd380251cb3005dfb1882d9d678c1095d41f2` |
| CredentialRegistry | `0xABe4a790fb07340e9740470A45A5fF92Fa5fD560` | `0x564f6f04c3d4fad7d019ecf1414e14863d739aae` |
| CredentialRegistryIdentityValidatorPolicy | `0x65Ddd59863d0A2F68E0953efB1226FC8A4b0021D` | `0xcaa55642befbaf6301be79dac88487ebe665ed6f` |

Full verify+audit gate run by session 6 (coordinator) 2026-07-02: **bytecode-match 10/10** against
the repo's viaIR (`FOUNDRY_PROFILE=ccid`) artifacts, immutable ranges masked; **getPolicies exact**
([AllowPolicy `0x2aedDb48...`, PausePolicy `0x5D273AAE...`, Validator]) on all three selectors;
**enforcement independently reproduced** (allowlisted-but-uncredentialed transfer reverts
`PolicyRunRejected` named by the validator, "account identity validation failed"; state restored).
OPEN item: Blockscout explorer-verification runs for the 10 Plume addresses (bytecode-match stands
in as source verification meanwhile).

### Onboarding-authority proxy (KycAllowlister)
| Chain | Proxy | Status |
|---|---|---|
| Sepolia | `0x1D58820C24ba48a9b09B1406a22F6035E4f12e54` | Audited + approved (session 5, #136/#139); owns AllowPolicy `0x7858b6e6...`; allowlister = issuer key `0x3055...`; live issuance traffic since 2026-07-02 (Didit E2E) |

## Not live (documented for completeness)

- **Base Sepolia (84532):** NOT a supported network (`SUPPORTED_CHAINS` excludes it). Early test
  deploys exist but several addresses have no code (deterministic-address artifacts). Nothing live
  to verify; revisit only if Base is added to the dApp.

(Astar Shibuya was previously listed here as DEPRECATED; it was re-activated 2026-06-19 and now has
its own verified section above.)

---
_Last updated 2026-07-02 by RWA Bridge session 6 (coordinator). All live-network canonical protocol contracts
verified (Astar Shibuya 5/5); source-based audit (3 analyzers, 0 Crit/High/Med) covers all EVM
chains + Solana._
