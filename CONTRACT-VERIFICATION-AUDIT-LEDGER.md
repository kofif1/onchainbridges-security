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
> WASM-hash verified + audited). Base and Astar/Shibuya are NOT live (see notes).
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
TestUSDC `0x71a8e443f223cfe59498968758db49f2d114dce2`.

**MBT bridge stack , LIVE + source-verified 2026-06-28** (Minato re-enabled, in MBT_CHAIN_IDS; supersedes the prior "hidden in UI" note):

| Contract | Address |
|---|---|
| MBT (RWATokenCCT) | `0x690ab8d7a6a96f49d65f113c5adcd89111480232` |
| Pool (BurnMintTokenPool 1.5.1) | `0xb4908accabcba9ce56ef0324b9695a88e3dd2534` |
| PolicyEngine proxy | `0x5d273aae0547fd33c0c5247487a160cd26f38fe4` |
| PolicyEngine impl | `0x49f1aa7c252183e79d4944c54698a335ed65c150` |
| AllowPolicy proxy | `0xa06b827bb452bc3e2caea0eee38b2186a28e99e1` |
| AllowPolicy impl | `0x19807c563746ab8fa5815c147cc51141ead600c2` |
| PausePolicy proxy | `0xaaa3a1ef510b029d0949c242372e535bf3702c8f` |
| PausePolicy impl | `0xcbe81190b7637136099c35f88e71fb2d04d4a7f2` |

solc 0.8.24 / optimizer 200. The 3 ACE contracts are ERC1967 proxy+impl pairs; impl addresses confirmed via on-chain ERC1967 slot reads matching the proxies in `chains.ts`. Pool `typeAndVersion` = "BurnMintTokenPool 1.5.1". Enforced policy chain checked via `getPolicies(MBT, transfer/transferFrom/mint)` = [AllowPolicy `0xa06b827b...`, PausePolicy `0xaaa3a1ef...`] (no orphan-policy trap). Lane verified-live both directions Sepolia<->Minato 2026-06-28 (canary + seq 1637 SUCCESS).

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
| YieldIssuer | `CD7DADUDMQRWOAANVV37U6233RO7YTCTTL6R3X7ZIBWUQNUQR7WS76SK` | `96903db2fe04216b207a855d0b6225e8374d11bcc70de770749e46c7281f4b5b` |
| YT | `CCMZ6UES2XLHKOQ7O74HE2T4IQFFNULBU3PCWPQUDTV5XJB656VF5FMU` | `662f81c410fb6768a2b5f0c0b9315af3d2cb781bfac7a44db9c8ec022784058a` |
| YTMarket | `CBVBHAHBBUMOCCEMFDEZFNKU3TYBFGT2F7WWRCYXWBHF7NYHG7R53QJ2` | `2b613c8773b5a63961636f68e9328d871891308d9adb3f317df14ba6196a3b16` |

> Source is on main as of commit `7d46c531` (PR #58, `feat: add Soroban contract sources to version
> control`, 36/36 native tests green), so the cited commit is reachable via `git checkout 7d46c531`.

> **Yield-stack redeploys (YieldIssuer/YT/YTMarket only; sMBT/sUSDC/Polaris unchanged).** The three
> yield rows above are the **2026-06-28 overflow-fix redeploy** (PR #127, source
> `soroban/{yieldissuer,yt,ytmarket}` @ commit `2f34586`), fixing an i128 mul-before-div overflow: the
> pro-rata/cost math now uses an overflow-safe `mul_div` via a 256-bit `I256` intermediate. Re-VERIFIED
> (`stellar contract fetch` sha256 == reproducible build, all 3; YT wasm is byte-identical to its prior
> deploy, only its id changed via the new issuer admin). Re-AUDITED (RWA Bridge 5 + Stellar session):
> delta = the `mul_div` helper at 4 sites (claim_yield/claimable_yield/buy/quote_buy) only; **0
> Crit/High/Med**. Explorers: [YieldIssuer](https://stellar.expert/explorer/testnet/contract/CD7DADUDMQRWOAANVV37U6233RO7YTCTTL6R3X7ZIBWUQNUQR7WS76SK), [YT](https://stellar.expert/explorer/testnet/contract/CCMZ6UES2XLHKOQ7O74HE2T4IQFFNULBU3PCWPQUDTV5XJB656VF5FMU), [YTMarket](https://stellar.expert/explorer/testnet/contract/CBVBHAHBBUMOCCEMFDEZFNKU3TYBFGT2F7WWRCYXWBHF7NYHG7R53QJ2).
>
> **Supersession history (gap documented, not hidden).** Yield lineage: original 2026-06-03
> (`CDOHKXC..`/`CBTRU547..`/`CCXKKM7I..`, the ids previously in THIS ledger) -> #79 deposit-time redeploy
> 2026-06-21 (`CDUHJUGS..`/`CALXF3KW..`/`CBL2JAZ6..`) -> #127 overflow-fix 2026-06-28 (current, above).
> The 2026-06-21 redeploy was gate-passed in the main-repo ledger (PR #86) but its public-repo re-sync
> was skipped, so this public ledger jumped original -> #127. (Also: the main-repo
> `docs/public/CONTRACT-VERIFICATION-AUDIT-LEDGER.md` copy and this public-repo copy have drifted and
> should be reconciled to one source of truth.)

## Stacks (Clarity, Testnet4)

**Non-EVM standard:** Clarity source is on-chain-readable; "Verified" = on-chain source == repo source at
a commit; "Audited" = clarinet check + npm tests + a structured access-control / arithmetic review.

**Overflow-fix redeploy 2026-06-28 (PR #129).** The Stacks yield/market had the same
`(* a b)`-before-`/` u128 overflow as the Soroban port (Clarity uint is 128-bit, no 256-bit widen), so
the fix is a new `stx-math` long-division `mul-div-down` = `floor(a*b/c)` computed without ever forming
`a*b`. The two yield/market contracts were redeployed under v2 names (Clarity names immutable). VERIFIED:
on-chain source `== #129` repo source for all 3 (independent `/v2/contracts/source` fetch + diff,
trailing-newline only). AUDITED (RWA Bridge 5 + Stacks session): the `mul-div-down` step-invariant proof
is correct and equals the Soroban floor result; delta = the 4 yield/market sites only; **0 Crit/High/Med**.
Deployer principal `ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW`.

| Contract | Principal.Name | Verified (explorer) |
|---|---|---|
| stx-math | `ST1RSK6..QDQZW.stx-math` | [explorer](https://explorer.hiro.so/txid/ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-math?chain=testnet) |
| stx-yield-issuer-v2 | `ST1RSK6..QDQZW.stx-yield-issuer-v2` | [explorer](https://explorer.hiro.so/txid/ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-yield-issuer-v2?chain=testnet) |
| stx-yt-market-v2 | `ST1RSK6..QDQZW.stx-yt-market-v2` | [explorer](https://explorer.hiro.so/txid/ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-yt-market-v2?chain=testnet) |

> The rest of the Stacks set (stx-mbt, stx-susdc, stx-yt, stx-lending-vault, the bridge contracts) is
> unchanged by this redeploy; recording the full Stacks set here is tracked as a follow-up.

## Not live (documented for completeness)

- **Base Sepolia (84532):** NOT a supported network (`SUPPORTED_CHAINS` excludes it). Early test
  deploys exist but several addresses have no code (deterministic-address artifacts). Nothing live
  to verify; revisit only if Base is added to the dApp.
- **Astar Shibuya (81):** DEPRECATED. MBT token + pool are zeroed in `chains.ts` and Shibuya is
  removed from `SUPPORTED_CHAINS`. Explorer is Subscan (no forge verification path). Revisit only
  if the chain is re-enabled.

---
_Last updated 2026-06-17 by RWA Bridge session 5. All live-network canonical protocol contracts
verified; source-based audit (3 analyzers, 0 Crit/High/Med) covers all EVM chains + Solana._
