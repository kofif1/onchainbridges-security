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
| MaxBalancePolicy (proxy, authoritative) | `0xe2e7Dce33380EfD675FEc42B4202612F29923167` | [yes](https://sepolia.etherscan.io/address/0xe2e7Dce33380EfD675FEc42B4202612F29923167#code) | [Slither 0.11.4: 0 Crit/High/Med/Low, 18 Info (2026-07-12)](audits/MAXBALANCEPOLICY-AUDIT-2026-07-12.md) | ACE additive Phase 1 primitive (PR #210); ERC1967 proxy, cap = uint256.max no-op, owner = 0xFc99 (Safe migration under `policy_mainnet_secure_signing_gate`). **ATTACHED on Sepolia MBT `0x044951AB` 2026-07-12** on transfer (tx `0x5e6be1aff891b7d6ae22fe571caad872b65905b5483ff1d45dca05153f9af079`) / transferFrom (`0xa42b2ff9b4f14250d7349afdec2713c8a3d410718b8ae569d7617689ed684b20`) / mint (`0x55761f91eee1a7a80d5548559dc1eade5dbe4594071859cfdbe2fbf452233707`), params `[keccak256("to"), keccak256("amount")]`, appended at index 3. Post-attach chain `[AllowPolicy, PausePolicy, Validator, MaxBalance]`; cap=max so behavior-neutral until a coordinated `setCap`. Extractor drift-check pre-attach: transfer/transferFrom via ERC20TransferExtractor `0x53dFCE21` (emits from+to+amount), mint via ERC20MintExtractor `0xb689499e` (emits to+amount) |
| MaxBalancePolicy (impl, authoritative) | `0x7E1ac7C5F58308d22bAe944B0061A932085B9914` | [yes](https://sepolia.etherscan.io/address/0x7E1ac7C5F58308d22bAe944B0061A932085B9914#code) | [Slither 0.11.4: 0 Crit/High/Med/Low, 18 Info (2026-07-12)](audits/MAXBALANCEPOLICY-AUDIT-2026-07-12.md) | scan this for logic; setCap/setExempt/setExemptBatch = by-design centralization |
| MaxBalancePolicy (proxy, ORPHAN / superseded) | `0xe221AeB60647e84947a7c193698afe8384716370` | [yes](https://sepolia.etherscan.io/address/0xe221AeB60647e84947a7c193698afe8384716370#code) | [Slither 0.11.4: 0 Crit/High/Med/Low, 18 Info (2026-07-12)](audits/MAXBALANCEPOLICY-AUDIT-2026-07-12.md) | duplicate deploy at nonce 609/610 before Token Controls' authoritative nonce 611/612; NEVER attached, NEVER wired; on-chain for audit trail only |
| MaxBalancePolicy (impl, ORPHAN / superseded) | `0x789aA37bf2ef02946e722bdBC1300AcB07B1594b` | [yes](https://sepolia.etherscan.io/address/0x789aA37bf2ef02946e722bdBC1300AcB07B1594b#code) | [Slither 0.11.4: 0 Crit/High/Med/Low, 18 Info (2026-07-12)](audits/MAXBALANCEPOLICY-AUDIT-2026-07-12.md) | bytecode-identical to authoritative impl (codehash `0xdda351ae092a3a5b28456d04bc4867e7542b8256ce327c88772a8f58b2d460da`); orphan, not wired |
| SupplyLimitPolicy (proxy) | `0x0582AeDF2CD32716BCB67EdD2352287fFC11FBa5` | [yes](https://sepolia.etherscan.io/address/0x0582AeDF2CD32716BCB67EdD2352287fFC11FBa5#code) | [Slither: 0 Crit/High/Med/Low, 18 Info (2026-07-14)](audits/SUPPLYLIMITPOLICY-AUDIT-2026-07-14.md) | ACE additive Phase 1 primitive #2 (PR #215, merged d69ef7e); ERC1967 proxy, maxSupply = uint256.max no-op, owner = 0xFc99 (Safe migration gate). **ATTACHED on Sepolia MBT 2026-07-14** mint selector 0x40c10f19 ONLY, params [keccak256("amount")], index 4 (tx `0x9b0a19288d5efa2d72b1876be60c434bba346e14831b1490eac3025098842d55`, block 11267709). Mint chain [AllowPolicy, PausePolicy, Validator, MaxBalance, SupplyLimit]; cap=max so behavior-neutral until setMaxSupply; CCIP-pool static mint passed clean post-attach; CCID independent re-read pinged. Single-deploy-driver followed, no orphan |
| SupplyLimitPolicy (impl) | `0x33Bad648E9F8274aaeD074D9A5033c7893Ac78bA` | [yes](https://sepolia.etherscan.io/address/0x33Bad648E9F8274aaeD074D9A5033c7893Ac78bA#code) | [Slither: 0 Crit/High/Med/Low, 18 Info (2026-07-14)](audits/SUPPLYLIMITPOLICY-AUDIT-2026-07-14.md) | scan this for logic; setMaxSupply = by-design centralization; pre-mint totalSupply read verified correct |
| TransferRestrictPolicy (proxy) | `0x01B887BA384f1A51D29CB2BdA9117B2C1f03919d` | [yes](https://sepolia.etherscan.io/address/0x01B887BA384f1A51D29CB2BdA9117B2C1f03919d#code) | [Slither: 0 Crit/High/Med, 1 Low (by-design timestamp), 18 Info (2026-07-14)](audits/TRANSFERRESTRICTPOLICY-AUDIT-2026-07-14.md) | ACE Phase-1 primitive #3 (PR #216, 78cba7a); global+per-wallet lockups (outgoing, max-rule) + blocklist + lockupExempt; deploys fully no-op; owner 0xFc99 (Safe gate). **ATTACHED on Sepolia MBT 2026-07-14**: transfer tx `0xda795172cce6d174740d0d3158cd753a332bf41d8918d8ca3e6c154776d44d39` (block 11267779) + transferFrom tx `0x1b6673d9439a3afa88f6472f7430e0e48099ec7950f0cb77bc0c405295223766` (block 11267782), both index 4, params [keccak256("from"),keccak256("to")]. Final chains: transfer/transferFrom [Allow,Pause,Validator,MaxBalance,TransferRestrict], mint [Allow,Pause,Validator,MaxBalance,SupplyLimit]; behavioral proof clean, all knobs no-op; CCID re-read pinged. Rejection strings "address is blocked"/"transfer locked" -> IA decode map |
| TransferRestrictPolicy (impl) | `0xC68c3f0beeEeA72e76F4A533d2f3fd731934c3A5` | [yes](https://sepolia.etherscan.io/address/0xC68c3f0beeEeA72e76F4A533d2f3fd731934c3A5#code) | [same report](audits/TRANSFERRESTRICTPOLICY-AUDIT-2026-07-14.md) | scan this for logic; owner lockups/blocklist + block.timestamp = by-design; freezes only, cannot move funds |
| CantonLockVault (ACE-gated) | `0xFE4a573C780DA0F895530ED609FDa7e3b833E15f` | [yes](https://sepolia.etherscan.io/address/0xFE4a573C780DA0F895530ED609FDa7e3b833E15f#code) | [manual review + on-chain behavioral proof: 0 Crit/High/Med (2026-07-19)](audits/CANTON-VAULT-AUDIT-2026-07-19.md) | Canton Phase-2 EVM-side lock vault (PR #259, main 634fb73), deployed by 0xFc99 block 11309903. `lock()` runs the ACE PolicyEngine on the depositor (via LockDepositorExtractor) BEFORE value moves. **Gate ACTIVATED 2026-07-19**: setExtractor tx `0x6d22bd80..`, addPolicy(AllowPolicy 0x7858) tx `0x4a4fd22b..`; `getPolicies(vault,0xf643509c)=[0x7858..]`. Behavioral proof: non-allowlisted `lock()` reverts "address is not on allow list", allowlisted passes to ERC20 allowance. Return leg `_processReport` forwarder-gated + dormant; caps=uint256.max + recipient NOT ACE-gated = HARD PREREQ before enabling Canton->EVM return. Supersedes ungated vault `0x92B8263e..` (live lane cuts over separately, Canton-owned) |
| LockDepositorExtractor | `0xbEa509a63035322D3117BcdDaa5A969ab4F53203` | [yes](https://sepolia.etherscan.io/address/0xbEa509a63035322D3117BcdDaa5A969ab4F53203#code) | [same report](audits/CANTON-VAULT-AUDIT-2026-07-19.md) | ACE extractor for `CantonLockVault.lock(uint256,string)`; surfaces `payload.sender` as `keccak256("depositor")=0x5bf3f581..` so AllowPolicy checks the DEPOSITOR (not a calldata arg). LOCK_SELECTOR `0xf643509c`. Bound on engine via setExtractor 2026-07-19 (tx `0x6d22bd80..`) |
| CantonBridgeOutbox (fresh, for gated vault) | `0x0d21fb470c8577Df126db7470c9F843cA378B360` | [yes](https://sepolia.etherscan.io/address/0x0d21fb470c8577Df126db7470c9F843cA378B360#code) | [same report](audits/CANTON-VAULT-AUDIT-2026-07-19.md) | DON-attestation outbox for the gated vault 0xFE4a (PR #259 source, identical to the prior outbox). Deployed by 0xFc99 tx `0xaa5e3e16..` block 11310194; getForwarderAddress()=0xF8344CFd, owner()=0xFc99, isAttested(1..7)=FALSE (clean nonce space). REPLACES reused outbox `0x89f38a88` for the new lane: the outbox keys attestations on raw lockNonce + reverts AlreadyAttested, and 0x89f38a88 already had nonces 1-7 attested from the OLD vault's locks, so the fresh-nonce gated vault (starts at 0) needed a clean outbox or its first-lock attestation would revert -> no mint. Nonce-keyed outbox = single-vault. |

> **Config bug surfaced:** `webapp/src/config/chains.ts` lists Sepolia TLPT as `0x8E531A05...`,
> which has NO code on Sepolia. The live TLPT token is `0x1ceaaE761C87278acfbE33c89eC523B46FAbCcF3`
> (per `aa.ts`), verified above. Flagged to the dApp owner to reconcile `chains.ts`.

> **Audit applies to every EVM chain below.** It is the same Solidity source compiled to the same
> bytecode on each chain, so the [3-analyzer source review](audits/SECURITY-REVIEW-2026-06-17.md)
> (0 Critical/High/Medium) covers all of them. The per-chain rows record on-chain *verification*.

## Plume Testnet (98867) - Phase-1 policies - Blockscout - COMPLETE (2026-07-14)

Propagation of the three Sepolia-audited Phase-1 policies (identical source, main `2fcd6e3` containing
`78cba7a`; per the standing rule the source audits cover all EVM chains - these rows record on-chain
verification). All six Blockscout-verified (3 impls fresh, 3 ERC1967 proxies bytecode-matched, constructor
args cross-checked against archived run-latest.json at `broadcast-archive/plume-98867/`). Gate-confirmed
live reads: owner `0xFc99...`, correct typeAndVersion, no-op knobs. Same by-design caveats as Sepolia rows.

| Policy | Proxy | Impl | Audit |
|---|---|---|---|
| MaxBalancePolicy | `0x16cb43d4eFF1727080352f2a14A68c5cc22A13a1` | `0xF7431a563de33840d0e350779765FdDBc38aE4D4` | [Sepolia report](audits/MAXBALANCEPOLICY-AUDIT-2026-07-12.md) |
| SupplyLimitPolicy | `0x7967517F323b2294e19A0A92fc2fAE7522617270` | `0x22C799187e8166953aA0748ea846DcBE9d7667c1` | [Sepolia report](audits/SUPPLYLIMITPOLICY-AUDIT-2026-07-14.md) |
| TransferRestrictPolicy | `0xd761C6ef8f1c29CA2cC4a64bAc84149c13204fc8` | `0x5E5256A9bAc9682de28db49F85B4891c82185234` | [Sepolia report](audits/TRANSFERRESTRICTPOLICY-AUDIT-2026-07-14.md) |

**ATTACHED on Plume MBT 2026-07-16** (Ilan's 6 casts, all status 1; validator `0x65Ddd598` holds at index 2):
- MaxBalance `0x16cb43d4` at index 3: transfer `0x0af3ecbac27da754cae0c1d517d076ea0e125f6c4b2254bc1e83044aa48242ee` (block 23805629), transferFrom `0x3d525ec6738fa75ab19856266759ccfb5526d43377f3a4cc04f715dc0be7cdab` (23805630), mint `0x6478bbdef4cc63e4eaacc69a4a2d485c02fee3e21f0ce7fed90e9eda304e1487` (23805631).
- SupplyLimit `0x7967517F` at index 4: mint `0x2226bda2f1907eb2d1efa6dc7d3427c0c5f71e39562e5cdd52fcb8112b488bb9` (23805634).
- TransferRestrict `0xd761C6ef` at index 4: transfer `0xb1ee95d5dc2aefae9744ea8e1041c35fac6e2aa50ff2b11596f34f8eaba6d44d` (23805635), transferFrom `0xaed1319339fe97b7900dca185158f86afb0a42f90001d0d0c6dc27b4e9d56f1b` (23805636).

Final live chains: transfer/transferFrom `[Allow, Pause, Validator@2, MaxBalance@3, TransferRestrict@4]`; mint `[Allow, Pause, Validator@2, MaxBalance@3, SupplyLimit@4]`. Behavioral proof clean (allowlisted passes, non-allowlisted rejects on AllowPolicy, pool mint clean); CCID independent re-read pinged. Plume = second chain live after Sepolia; Amoy + Minato next on Ilan's window, same pipeline.

## Polygon Amoy (80002) - Etherscan V2 - COMPLETE

Explorer: `https://amoy.polygonscan.com/address/<addr>#code`. All 14 canonical contracts verified:
MBT `0x71A8e443f223cFe59498968758dB49F2D114dCE2`, BurnMintTokenPool `0x48dB3f62B6c7328Fb742B4799654136794dBE2B4`,
PolicyEngine proxy `0xfefA774dB9A52c72EC8f0621046836CbA63d2BA6` + impl `0xf3186574c8a0c0b3e235704bad389b78604b2745`,
AllowPolicy proxy `0x5fe9d900945b60B3BEcEDe4382BEd2653EC99D40` + impl `0x45199c8628c316b9ac7b09a048f74284afc779ab`,
PausePolicy proxy `0x8c57Abe7Cf1330346EB760EDaEE380A66D3f42f7` + impl `0x4de6322b74eb649049d6c93024e51f35b5df306d`,
LockVault `0x5D273AAE0547FD33c0C5247487a160cd26f38FE4`, YieldIssuer `0x62BD210F9e7B4889066Bd7bc360557c666af44F5`,
YieldToken `0xC4dE838dF59AEB7a9Ad099A34EE6F3E1B19D1aeC`, YTMarket `0xa701147b7C1a511790BE503675Df1F8B09caBAa9`,
LendingVault `0xb4908accabcba9ce56ef0324b9695a88e3dd2534`, TLPTFaucet `0x6c61658cfe0e5794f739782433a8011a6107a570`.

### Phase-1 policies (Amoy) - Etherscan V2 - COMPLETE (2026-07-16)

The three Sepolia-audited Phase-1 policies propagated to Amoy (identical source, main `2fcd6e3`; per the
standing rule the Sepolia source audits cover all EVM chains, these rows record on-chain verification).
All 6 Etherscan-V2-verified (viaIR / `FOUNDRY_PROFILE=ccid`; 3 impls no-args + 3 ERC1967 proxies, proxy
constructor args = `(impl, initCall)` with engine slot `0xfefA774d`, cross-checked against archived
receipts at `broadcast-archive/amoy-80002/`). Single-deploy-driver, addresses matched predictions,
live reads verified (typeAndVersion, no-op knobs, owner `0xFc99`, impl slots). Same by-design caveats as Sepolia.

| Policy | Proxy | Impl | Audit |
|---|---|---|---|
| MaxBalancePolicy | `0x92152fc12141257f9CB59E4b48BD5d03d39FFC17` | `0x0AF4dA1E7D7223636f68b0Fece064a08c823a0b2` | [Sepolia report](audits/MAXBALANCEPOLICY-AUDIT-2026-07-12.md) |
| SupplyLimitPolicy | `0xd9425Ee38168071378a567E8969DDD8Ad31935eC` | `0x241a31e68E994036B027eBa11FF4A2F9Bf2c305C` | [Sepolia report](audits/SUPPLYLIMITPOLICY-AUDIT-2026-07-14.md) |
| TransferRestrictPolicy | `0x68e7A9514953750b4bc4ED1b715fC8a8759AB611` | `0xC5DD43513D013B422FA522cf2f6E5B57ec038F69` | [Sepolia report](audits/TRANSFERRESTRICTPOLICY-AUDIT-2026-07-14.md) |

**ATTACHED on Amoy MBT 2026-07-16** (Ilan's 6 casts, all status 1; validator `0xCAEe7962` holds at index 2):
- MaxBalance at index 3: transfer `0x3526fa2b54fd97f33ff150e3051c649d71731e3f2452cd708a922ae56e02b39e` (block 42417186), transferFrom `0xedc576d2a5a4dcd6a49af6a12db2d0dfd038cf7a252d49ecb9713b822fe39475` (42417189), mint `0x10777761779a66a89cfda6938299a2d513b0924ca0b27549afb072f0ada4cd96` (42417191).
- SupplyLimit at index 4: mint `0x376cb74d526414a0826bacb9d5aeacf2d3b3b2044380e731a1d457401f544c74` (42417192).
- TransferRestrict at index 4: transfer `0xa2f778a49a55a8883decd862b12069205d60f0c283d253294fc671183feb1454` (42417193), transferFrom `0xcde7a1c4e0b7055de12497180930c50fc6281c389bb1a5a11088cfe7ee99fda0` (42417196).

Final live chains: transfer/transferFrom `[Allow, Pause, Validator@2, MaxBalance@3, TransferRestrict@4]`; mint `[Allow, Pause, Validator@2, MaxBalance@3, SupplyLimit@4]`. Behavioral proof clean; CCID re-read pinged. Amoy = third chain live (after Sepolia + Plume).

## Soneium Minato (1946) - Phase-1 policies - Blockscout - COMPLETE (2026-07-16)

The three Sepolia-audited Phase-1 policies propagated to Minato (identical source, main `2fcd6e3`; the
Sepolia source audits cover all EVM chains per the standing rule, these rows record on-chain verification).
All 6 Blockscout-verified (`soneium-minato.blockscout.com`, viaIR / `FOUNDRY_PROFILE=ccid`; 3 impls no-args +
3 ERC1967 proxies, proxy constructor args = `(impl, initCall)` with engine slot `0x5d273aae`, receipts at
`broadcast-archive/minato-1946/`). Single-deploy-driver, addresses matched predictions, live reads verified
(typeAndVersion, no-op knobs, owner `0xFc99`, impl slots). Same by-design caveats as Sepolia. Deterministic-collision
note: Minato MaxBalance impl/proxy addresses collide with Shibuya YTMarket/Astarium-vault addresses on other chains,
chain-qualify everything.

| Policy | Proxy | Impl | Audit |
|---|---|---|---|
| MaxBalancePolicy | `0x3F1d5141391F5EDe23A84EbdDD2abB3e56Dd9519` | `0xFf69A2c111759777693e348727BD157db4Fb8AB2` | [Sepolia report](audits/MAXBALANCEPOLICY-AUDIT-2026-07-12.md) |
| SupplyLimitPolicy | `0x7c36D0605a53D006792444F4346F65d2f5600285` | `0xAa78852A39204B4C761cFaC12B5c930357D541c9` | [Sepolia report](audits/SUPPLYLIMITPOLICY-AUDIT-2026-07-14.md) |
| TransferRestrictPolicy | `0x9D7770951589fd40aC2816AE60172063aE0EeFD2` | `0x91AaC905d4946e18311e4Eea267B01830e451437` | [Sepolia report](audits/TRANSFERRESTRICTPOLICY-AUDIT-2026-07-14.md) |

**ATTACHED on Minato MBT 2026-07-16** (Ilan's 6 casts, all status 1; validator holds at index 2, uniform shape vs the other three chains):
- MaxBalance at index 3: transfer `0x0524f238638fa91c556a1b9af557f90e37a32c5ce7d2d37724ab69bc300a225a` (block 30555404), transferFrom `0xbb0c0edea2c08e0946028682fefebf219654d7f8704e915856d5efd608692ab9` (30555406), mint `0x77e4ff0e22fd7ac499f4854c1ef7923342265049ade134bbe95257cfdb97047e` (30555408).
- SupplyLimit at index 4: mint `0x8fc9350d6519b1d88185642158e0a1494cc663c9eb65e85aae33cb358edeb143` (30555409).
- TransferRestrict at index 4: transfer `0x5dc18e20af69cbf22ed1e16d80755cde28b9a7330238091050c161dd27592f48` (30555411), transferFrom `0xada96f8b96e959cfe748e2c91d251604d573c9d3ddb6128a71124494e9876bcc` (30555413).

Behavioral proof clean; CCID pinged for the final all-chains sweep. **Minato = fourth EVM chain live. FULL EVM PHASE-1 PROPAGATION COMPLETE: 4 chains (Sepolia + Plume + Amoy + Minato) x 3 policies, all deployed, gated (verified + audited), attached, and enforcing.** Shibuya deferred on Astar recovery; Base pending its going-live.

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

### Stacks Tier-2 identity (deployed + rotated 2026-07-05, coordinator-gated)

Cross-chain "verify once, everywhere" Tier-2 for Stacks: a native credential registry + a
split-operator allowlister proxy that becomes `stx-mbt`'s compliance-admin. Gate-reviewed PASS on
PR #170 (Clarity-only, no webapp cut). Deployed by the Stacks channel (single-signer), each step
independently verified on-chain by the coordinator (Hiro reads + falsified reader): GATE 1
(post-publish: all operators = deployer, admin NOT yet rotated) PASS, GATE 2 (post-rotation:
`stx-mbt.get-compliance-admin` == the proxy principal, operators intact, nothing pre-issued) PASS,
SMOKE (proxy `allow`->`is-allowed=true`->`revoke`->`false`, both `(ok true)`, admin undisturbed) PASS.

| Contract | Contract ID | Role | Deploy txid |
|---|---|---|---|
| stx-credential-registry | `ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-credential-registry` | native KYC credential registry (`is-credentialed(who,cred-type)`; owner + issuer-operator = deployer) | `0x1a7c065f152a01fdb2c756a170e5bc6d8fbca23a8b15c21fcc8b4bc6069aba67` |
| stx-kyc-allowlister | `ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-kyc-allowlister` | split-operator compliance-admin proxy for `stx-mbt` (allowlister-operator + pause-operator + owner = deployer); rotated to `stx-mbt` compliance-admin | `0x5adc674b459e7dc661e4a33dd165760a25f57682a6a385ce1bcf96c72120c243` |

Rotation tx (`set-compliance-admin(stx-mbt)` -> proxy): `0x0ed7bbafb269cb4004eb87aa4c495363724406d4095122b0832a156e22c24e6c`.
Smoke: allow `0x42223184eeb95e13c5272728b335e31135167c93b18c8cbcb07c0244abcb8189`, revoke `0x4ef1cfbc91c13dd76bad8c361ac8eccae514ea27b31677826d50339ac6757fad`.

### Production verification badge (deployed + gated 2026-07-05) - PERMANENT

`ST1RSK6GEJ4GP8QC1QNN91J1KW014FBZCMTAQDQZW.stx-verification-badge` (SIP-009 soulbound, reuses the existing
`.nft-trait`). The wallet-visible production badge (replaces the throwaway spike). Deployed single-signer (Ilan),
gated by coordinator. **VERIFIED** (deployed Hiro source, authoritative) + **AUDITED** (0 Crit/High/Med):
73-line source; `transfer` body = unconditional `ERR-SOULBOUND` (u901), NO `nft-transfer?` path = HARD soulbound
(can never move); mint/burn gated `contract-caller == issuer`, `set-token-uri`/`set-issuer`/`set-contract-owner`
gated `contract-caller == contract-owner` (both init tx-sender = deployer; NO tx-sender-based gate = correct
posture); mint idempotent via `badge-of` (no dupes); burn issuer-only + clears `badge-of`; MIRROR-ONLY (never read
for admission - enforcement stays at the registry/compliance). On-chain verified: get-owner(u1)=`ST1KQ0H7CZEN0SQ5ANGRK8Y676H2MKGRZY4VGXQB9`,
last-token-id=u1, get-token-uri(u1)=`https://demo.onchainbridges.com/badge/ob-verified.json` (font-baked PNG +
CORS-served, SIP-016), has-badge(ST1KQ0H7)=true. txids: publish `0xbece9da251ac8c535b641b8e661814f2c6f73b5aaed5a619594fc4514f58389d`,
set-token-uri `0xc6b26cc13d772fa8786366370fa582ac06b52e9482a0a82863d7c5fe2ad1aa91`, mint `0x20ae98415b19577be1d4fe9cdf02e8be28df0099fe0a92268b80b4e6ba906634`.
Xverse ST3WWMG9 NOT minted (is-credentialed=false, deferred). In-wallet render = QA empirical check post-Hiro-index
(the spike showed Hiro-caching alone does not guarantee wallet render). Formal PR link to be added when finalized (byte-identical to deployed).

**THROWAWAY (NOT a permanent registry contract):** `stx-badge-spike` (SIP-009 soulbound badge spike)
+ `nft-trait`, deployed to demonstrate the wallet-visible badge; minted token u1 to `ST1KQ0H7CZEN0SQ5ANGRK8Y676H2MKGRZY4VGXQB9`
(Leather) and u2 to `ST3WWMG93NPTFB16V7MYZ0YE7HS24YSMP75E70XZD` (Xverse); last-token-id=2, both owners verified on-chain.
Wallet render finding (2026-07-05, EMPIRICAL - Ilan's live wallet check; supersedes prior API-layer inferences):
Badge is DETECTED as a soulbound collectible in BOTH Leather (ST1KQ0H7, "Amount 1") AND Xverse (ST3WWMG9, "1 item")
- the mint + soulbound mechanism works in both wallets. BUT the IMAGE DOES NOT RENDER in either (Leather: "Image
currently unavailable"; Xverse: blank placeholder). Hiro having a valid cached PNG did NOT translate to wallet
display. Real-badge fix = a FRESH production soulbound mint bound to the Tier-2 credential registry with a proper
PNG SIP-016 image (font-independent art, CORS-served), NOT the spike - fresh mint required because Hiro has no
manual re-index endpoint (POST .../refresh = 404), so editing the spike metadata won't update wallets. QA verifies
render IN-WALLET before sign-off. Mint/compliance path clean (owner/soulbound/token-uri all verified on-chain).
Soulbound confirmed on-chain: deployed `transfer` body is unconditional `ERR-SOULBOUND` (u901), no
`nft-transfer?` path. token-uri `https://demo.onchainbridges.com/badge/badge-spike.json` (HTTP 200).
Publish nft-trait `0xa3019fe5016a81d73e5a8aeee45be44bd3028db871b1768e0142aba7365908d4`, spike
`0x1f5dfc0a569d3e6bb6fa89cb0c39b02f36195374861c0b079de98dce5acf6912`, mint
`0x1944b28d50b036764c7cfa28cc593fb67ad97c8db78b72f99e723c886bc227a4`. Stacks channel to strip the
Clarinet.toml entries + spike `.clar` at wind-down; do NOT promote to production without a fresh gate.

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
| Sonic Testnet | `0x277Ca924d2f6d1B0e5D7D195d880f75E1e3e78da` | **WrappedMBTAllowlister** (split-operator owner-proxy for wMBT v2). Gate run by session 6 (coordinator) 2026-07-04: PR #162 review clean (no compliance bypass / reentrancy / fund custody; renounceOwnership disabled), 20/20 foundry tests independently reproduced, **source-verified on Sonicscan** (Etherscan V2 API), deploy tx `0xd83122c6...ec2bb9`. OWNS wMBT v2 `0x670c3a58...` since transferOwnership tx `0x0718b553...2af53` (pre-transfer live check: `minters[CRE consumer 0xa56efb5a]=true`, mint path independent of ownership). Hot allowlister = issuer key `0x3055...` (allow/disallow only); cold admin = `0xFc9933...` (pause/minter/rotate/escape). First Tier-1 write path for cross-chain verification; live smoke test = 5-wallet backfill via the proxy, tx `0xf1b077a4...c79b37` |

### Verification badge SBT (component C - soulbound wallet badge, MIRROR-ONLY)

Deployed 2026-07-04 (Ilan signing sitting, CCID channel build). Gate run by session 6 (coordinator)
PRE-deploy on branch `ccid-soulbound-badge` @ e3ae73b: line-by-line review clean (soulbound `_update`
chokepoint closes every movement path; approvals hard-revert; one-per-wallet tokenId=uint160(wallet);
two-step ownership, no renounce; renderer swap can never touch token semantics; renderer fails to
UNKNOWN never VALID), 8/8 foundry tests independently reproduced. **MIRROR-ONLY invariant:** never
read for admission by anything; compliance enforcement stays exclusively with the ACE validators.
All 10 contracts **source-verified** (Etherscan V2: Sepolia/Amoy; Blockscout: Plume/Minato/Shibuya;
`FOUNDRY_PROFILE=ccid`). Owner = `0xFc9933...` (two-step), issuer = `0x3055...` on every chain.

| Chain | BadgeRendererV1 | VerificationBadgeSBT |
|---|---|---|
| Sepolia | `0x0dad8806B7768A8E6bca3f9DbA8419F27C08cA9d` | `0x11D0c9bC018F3B3D22Fc1AbFEc138a278AE3f30D` |
| Polygon Amoy | `0x1714F8334728268fCd667f8228E62DA105AAA5A8` | `0x54f1Eaf13038364214D1E75F6bD143Cc80BBfffB` |
| Plume Testnet | `0x62BD210F9e7B4889066Bd7bc360557c666af44F5` | `0xa701147b7C1a511790BE503675Df1F8B09caBAa9` |
| Soneium Minato | `0xE258a260a7A0a917a1cacc58281420A445Aa0730` | `0x3a044d883F8277de07fC315E9dB5f2ECB0eB2969` |
| Astar Shibuya | `0xa701147b7C1a511790BE503675Df1F8B09caBAa9` | `0x7798F2F58C25186F03DF1a40Eed08A4905C3b5F7` |

> **Badge cohort rule + 2026-07-04 stray-mint incident (recorded for accountability):** the badge
> cohort is PER-CHAIN (the chain's credentialed EOAs; protocol contracts are credentialed for
> admission parity but NEVER badged - badge = human KYC marker). During the Shibuya mint run the
> coordinator minted 2 badges to SEPOLIA-cohort wallets (0x044367F8, 0x45d67462) inferred to be in
> the Shibuya cohort without the authoritative list - both wallets read credentialed=false on
> Shibuya, so the live-truth renderer showed REVOKED (never a false VALID; the mirror-only invariant
> meant zero compliance impact). Both burned by the owner same sitting. STANDING RULE: no signed
> writes in the badge lane without the CCID channel's authoritative cohort enumeration. Final badge
> matrix: Sepolia 23/23, Amoy 19/19, Plume 21/21, Minato 4/4, Shibuya 5/5 credentialed EOAs badged.
>
> **Deterministic-address collision warnings (chain-qualify ALWAYS):** Shibuya's RENDERER address
> equals PLUME'S SBT address (`0xa701147b...` - maximally confusable); Amoy's SBT `0x54f1Eaf1...`
> and both Plume badge addresses also collide with backfill-cohort wallet addresses on other chains.
> Same deployer+nonce coincidence class as the Base/Plume/Sonic collisions noted above.

## Multi-Asset Signing Session 1 (AVR + TGLP) - Sepolia + Amoy - Etherscan V2 - COMPLETE (2026-07-16)

Signing Session 1 of the multi-asset marketplace deployed two RWA assets, **AVR** (Aurum Vault Reserve)
and **TGLP** (Thames Gateway Logistics Park), across **Sepolia (hub)** and **Polygon Amoy**. Each asset
has its own PolicyEngine + LinkedComplianceCheck (linked to the per-chain master AllowPolicy allowlist),
a BurnMintTokenPool for CCIP burn/mint, and mint/transfer extractors. Per the every-asset-PoR catalog rule,
each asset carries a **hub** Chainlink Proof-of-Reserve feed (MockV3Aggregator on testnet; a real Chainlink
PoR feed at mainnet). Deployed by the Multi-Asset Marketplace channel; verify/audit gate by Session 8.
Owner/issuer-admin = `0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c` (deployer; Safe migration under `policy_mainnet_secure_signing_gate`).

**Source audit:** [MULTIASSET-SS1-AUDIT-2026-07-16](audits/MULTIASSET-SS1-AUDIT-2026-07-16.md), Slither 0.11.4
on the two OB-authored contracts (LinkedComplianceCheck, RWATokenCCT): **0 Critical/High/Medium in OB code**
(the 2 Medium reentrancy reports are Chainlink ACE library `PolicyProtected`; the 1 OB Medium `unused-return`
on the PoR read is non-exploitable given the staleness + negative-reserve guards). Chainlink ACE library
contracts (PolicyEngine, AllowPolicy, extractors) are vendored under the BUSL additional-use grant; CCIP
BurnMintTokenPool + MockV3Aggregator are Chainlink source. Constructor args taken from on-chain creation input, not guessed.

> **PoR labeling:** the MockV3Aggregator rows are reserve FEEDS, not tokens. In public docs / dApp they appear
> under the token they back (e.g. "AVR Reserve Feed, Chainlink Proof of Reserve (testnet mock)"), never as a standalone asset.

> **Operational risk (flagged, not a contract finding):** AVR + TGLP PoR feeds have `maxStaleness = 24h`. The
> por-refresh keeper currently refreshes only the MBT feed; it must be extended to the AVR (`0x77f09Bc9...`)
> and TGLP (`0x27C931...`) feeds, or hub mints/mint-backs revert with `StaleReserveData` after 24h.

### Sepolia (11155111)

**Shared master allowlist**

| Contract | Address | Verified |
|---|---|---|
| AllowPolicy (master impl) | `0x20BCACc849092A575738693830815b04d3e07E3C` | [yes](https://sepolia.etherscan.io/address/0x20BCACc849092A575738693830815b04d3e07E3C#code) |
| ERC1967 proxy | `0xd276471610692F3B5BECF433C867900E0291FD86` | [yes](https://sepolia.etherscan.io/address/0xd276471610692F3B5BECF433C867900E0291FD86#code) |

**AVR - Aurum Vault Reserve (token, pool, engine, linked-compliance, extractors, PoR feed)**

| Contract | Address | Verified |
|---|---|---|
| PolicyEngine (engine impl) | `0x305A50edADE43b4F07cAe29D948407ba39C4C549` | [yes](https://sepolia.etherscan.io/address/0x305A50edADE43b4F07cAe29D948407ba39C4C549#code) |
| ERC1967 proxy | `0x6B603E425C95f6a91daAA0f6cF079b4E235a702f` | [yes](https://sepolia.etherscan.io/address/0x6B603E425C95f6a91daAA0f6cF079b4E235a702f#code) |
| LinkedComplianceCheck (impl) | `0xE6F5026B933875Da5Ebb258ec4446Ffa2D2e340F` | [yes](https://sepolia.etherscan.io/address/0xE6F5026B933875Da5Ebb258ec4446Ffa2D2e340F#code) |
| ERC1967 proxy | `0xB37576b86201fe92C23Df6E1dc0EDD6c9aE15140` | [yes](https://sepolia.etherscan.io/address/0xB37576b86201fe92C23Df6E1dc0EDD6c9aE15140#code) |
| ERC20TransferExtractor | `0xebA1e8385323fce20377A77d80E3284fB67B63e5` | [yes](https://sepolia.etherscan.io/address/0xebA1e8385323fce20377A77d80E3284fB67B63e5#code) |
| ERC20MintExtractor | `0xAD335fd374139Bef8c4384A798857A5E762400F7` | [yes](https://sepolia.etherscan.io/address/0xAD335fd374139Bef8c4384A798857A5E762400F7#code) |
| MockV3Aggregator (PoR reserve feed) | `0x77f09Bc9Bd4B9a2FFeF802dbae1F9Af724A333b8` | [yes](https://sepolia.etherscan.io/address/0x77f09Bc9Bd4B9a2FFeF802dbae1F9Af724A333b8#code) |
| RWATokenCCT (token) | `0x8CaDD1Dd8841FC8dC88f7f27dd0a12F2ABb5Ac2D` | [yes](https://sepolia.etherscan.io/address/0x8CaDD1Dd8841FC8dC88f7f27dd0a12F2ABb5Ac2D#code) |
| BurnMintTokenPool | `0x6ECD13c09a59F198aE51D2B159a7F8B082f47d49` | [yes](https://sepolia.etherscan.io/address/0x6ECD13c09a59F198aE51D2B159a7F8B082f47d49#code) |

**TGLP - Thames Gateway Logistics Park (token, pool, engine, linked-compliance, extractors)**

| Contract | Address | Verified |
|---|---|---|
| PolicyEngine (engine impl) | `0xFC52536Cd234Eb2F940DdE4972083D52D47eaAF4` | [yes](https://sepolia.etherscan.io/address/0xFC52536Cd234Eb2F940DdE4972083D52D47eaAF4#code) |
| ERC1967 proxy | `0xB5E576E407a8b4d9311357274Bc7fc1b712e47D2` | [yes](https://sepolia.etherscan.io/address/0xB5E576E407a8b4d9311357274Bc7fc1b712e47D2#code) |
| LinkedComplianceCheck (impl) | `0xC368191502DBE71d9a1575e62eB0e9C24B040405` | [yes](https://sepolia.etherscan.io/address/0xC368191502DBE71d9a1575e62eB0e9C24B040405#code) |
| ERC1967 proxy | `0x5e348Ac40b42aB28FfD131E5d52e88173c5431F9` | [yes](https://sepolia.etherscan.io/address/0x5e348Ac40b42aB28FfD131E5d52e88173c5431F9#code) |
| ERC20TransferExtractor | `0xF3e0d152feD7347806A90e49d5F06C8B9085c9Bc` | [yes](https://sepolia.etherscan.io/address/0xF3e0d152feD7347806A90e49d5F06C8B9085c9Bc#code) |
| ERC20MintExtractor | `0x832f044AbDc18b0Af6EfaBdB88869Cb2a45f7Eb3` | [yes](https://sepolia.etherscan.io/address/0x832f044AbDc18b0Af6EfaBdB88869Cb2a45f7Eb3#code) |
| RWATokenCCT (token) | `0xa2951ee0A94aA6a37646B3112561f1b4eCd22e36` | [yes](https://sepolia.etherscan.io/address/0xa2951ee0A94aA6a37646B3112561f1b4eCd22e36#code) |
| BurnMintTokenPool | `0x0B3a9dBF5B10520775ed3b8d3F38c2B3754A0d42` | [yes](https://sepolia.etherscan.io/address/0x0B3a9dBF5B10520775ed3b8d3F38c2B3754A0d42#code) |

**TGLP hub reserve feed (retrofit, 2026-07-16):**

| Contract | Address | Verified |
|---|---|---|
| MockV3Aggregator (TGLP PoR reserve feed) | `0x27C931499bDcdF0C81F5a853358BC99216Bf75C9` | [yes](https://sepolia.etherscan.io/address/0x27C931499bDcdF0C81F5a853358BC99216Bf75C9#code) |

### Polygon Amoy (80002)

**Shared master allowlist**

| Contract | Address | Verified |
|---|---|---|
| AllowPolicy (master impl) | `0x8BC2987abf06E5CE6fb4a4B170FBd1f2b5dB6108` | [yes](https://amoy.polygonscan.com/address/0x8BC2987abf06E5CE6fb4a4B170FBd1f2b5dB6108#code) |
| ERC1967 proxy | `0x7A6bd37D4367dc0F02cEd912b1070f877000E7be` | [yes](https://amoy.polygonscan.com/address/0x7A6bd37D4367dc0F02cEd912b1070f877000E7be#code) |

**AVR - Aurum Vault Reserve (token, pool, engine, linked-compliance, extractors; no PoR, hub-only)**

| Contract | Address | Verified |
|---|---|---|
| PolicyEngine (engine impl) | `0x6E0a14Ef8C684a6f1c3220626DCFd32DFb25D540` | [yes](https://amoy.polygonscan.com/address/0x6E0a14Ef8C684a6f1c3220626DCFd32DFb25D540#code) |
| ERC1967 proxy | `0xa1b8C77E085f9Ad7623aC7FBa7335D4b29500E00` | [yes](https://amoy.polygonscan.com/address/0xa1b8C77E085f9Ad7623aC7FBa7335D4b29500E00#code) |
| LinkedComplianceCheck (impl) | `0x9902D67Ac10a065e43Da17Bed31D3b99eBD1887F` | [yes](https://amoy.polygonscan.com/address/0x9902D67Ac10a065e43Da17Bed31D3b99eBD1887F#code) |
| ERC1967 proxy | `0xAC30A1Dc1F39d0cF3758baEa893356A89dAD5847` | [yes](https://amoy.polygonscan.com/address/0xAC30A1Dc1F39d0cF3758baEa893356A89dAD5847#code) |
| ERC20TransferExtractor | `0x7650203244AB71072798b2a922E63e6159a8dF10` | [yes](https://amoy.polygonscan.com/address/0x7650203244AB71072798b2a922E63e6159a8dF10#code) |
| ERC20MintExtractor | `0x92B8263ead8712f2962D84a7319acD4C7D01Bf35` | [yes](https://amoy.polygonscan.com/address/0x92B8263ead8712f2962D84a7319acD4C7D01Bf35#code) |
| RWATokenCCT (token) | `0x23FFAE0C81d1FeDFE96F7C23dd79132EBD38Cfa6` | [yes](https://amoy.polygonscan.com/address/0x23FFAE0C81d1FeDFE96F7C23dd79132EBD38Cfa6#code) |
| BurnMintTokenPool | `0x732C0D5792F0e9194BEa09EC73F2d8E84dF17fF7` | [yes](https://amoy.polygonscan.com/address/0x732C0D5792F0e9194BEa09EC73F2d8E84dF17fF7#code) |

**TGLP - Thames Gateway Logistics Park (token, pool, engine, linked-compliance, extractors; no PoR, hub-only)**

| Contract | Address | Verified |
|---|---|---|
| PolicyEngine (engine impl) | `0x000dF9158FAeB7F1Edc7f0957C68BfA34d5e7F76` | [yes](https://amoy.polygonscan.com/address/0x000dF9158FAeB7F1Edc7f0957C68BfA34d5e7F76#code) |
| ERC1967 proxy | `0xbebbA83b8623aAfB9Ec802304B4ED0Bff261BB16` | [yes](https://amoy.polygonscan.com/address/0xbebbA83b8623aAfB9Ec802304B4ED0Bff261BB16#code) |
| LinkedComplianceCheck (impl) | `0x5e353a7094F2f2C57EA4e74652e976cd2a313039` | [yes](https://amoy.polygonscan.com/address/0x5e353a7094F2f2C57EA4e74652e976cd2a313039#code) |
| ERC1967 proxy | `0xca32f347A3A474349A15e30584440c97966d3b44` | [yes](https://amoy.polygonscan.com/address/0xca32f347A3A474349A15e30584440c97966d3b44#code) |
| ERC20TransferExtractor | `0x88c6B3C9744C7Fd33338ba5876CeA4A96Baa601e` | [yes](https://amoy.polygonscan.com/address/0x88c6B3C9744C7Fd33338ba5876CeA4A96Baa601e#code) |
| ERC20MintExtractor | `0xF7a66681a995bF4fCBAb93f47dfd52d846c2367B` | [yes](https://amoy.polygonscan.com/address/0xF7a66681a995bF4fCBAb93f47dfd52d846c2367B#code) |
| RWATokenCCT (token) | `0xeAE7190C10Aad2c677a575319A6511A5047A7550` | [yes](https://amoy.polygonscan.com/address/0xeAE7190C10Aad2c677a575319A6511A5047A7550#code) |
| BurnMintTokenPool | `0x4b8d147f9b7985D338E53eC42A2b0571e2C326D3` | [yes](https://amoy.polygonscan.com/address/0x4b8d147f9b7985D338E53eC42A2b0571e2C326D3#code) |

## Multi-Asset Signing Session 2 (SS2: AVR/TGLP lending vaults + TGLP yield stack) - Polygon Amoy - Etherscan V2 - COMPLETE (2026-07-20)

Signing Session 2 deployed the DeFi layer on top of the SS1 assets, Amoy-only per Phase A: one lending
vault per asset (provider "Atlas Capital") plus a full yield stack for TGLP. AVR intentionally has NO
yield stack (decision D5). Deployed by Ilan 2026-07-18 (staged by the Multi-Asset Marketplace channel);
verify + audit + ledger gate by Session 8, 2026-07-20.

**Source audit: no new contract code.** `LendingVault`, `YieldIssuer`, `YieldToken` and `YTMarket` are
the already-audited sources, confirmed UNCHANGED vs `origin/main`, so the existing source review covers
them. SS2 created new INSTANCES with new parameters; those parameters were each verified on-chain
(below). Compiler solc 0.8.24 / optimizer 200 / via_ir FALSE.

**On-chain state verified (Session 8, 2026-07-20):** vault terms match the approved runbook exactly
(AVR 6500 bps LTV / 500 bps APR / $250 per token; TGLP 7500 bps / 420 bps / $25); each vault pre-funded
100,000 TestUSDC; collateral tokens resolve to the SS1 assets (AVR `0x23FFAE0C` "Aurum Vault Reserve",
TGLP `0xeAE7190C` "Thames Gateway Logistics Park"); the TGLP yield stack is internally consistent
(issuer.rwaToken == the TGLP vault's collateral; issuer.yieldToken == market.ytToken == YT-TGLP;
YT.owner == the issuer; one shared settlement USDC `0xce8C237c`); `currentPeriodId = 0` (no period
opened yet); and all five are admitted on the Amoy MASTER allowlist `0x7A6bd37D`, which is also the
YT's `allowPolicy`.

| Contract | Address | Verified | Notes |
|---|---|---|---|
| AVR LendingVault (Atlas Capital) | `0x34aE4bc3850651ad92036de01e530be943309218` | [yes](https://amoy.polygonscan.com/address/0x34aE4bc3850651ad92036de01e530be943309218#code) | collateral AVR; 65% LTV, 5.0% APR, $250/token; 100k USDC |
| TGLP LendingVault (Atlas Capital) | `0x21389D0c8ABAA1C555516a495d3558CE48a5221A` | [yes](https://amoy.polygonscan.com/address/0x21389D0c8ABAA1C555516a495d3558CE48a5221A#code) | collateral TGLP; 75% LTV, 4.2% APR, $25/token; 100k USDC |
| TGLP YieldIssuer | `0x52966F83C3a6D68104aa285e01cAbFf944c9a236` | [yes](https://amoy.polygonscan.com/address/0x52966F83C3a6D68104aa285e01cAbFf944c9a236#code) | rwaToken TGLP, yieldCurrency USDC (6dp); deploys the YT |
| YT-TGLP (YieldToken) | `0xbA3198097fd29d8DEA1CB7ac7Ac6896DB4A24DE3` | [yes](https://amoy.polygonscan.com/address/0xbA3198097fd29d8DEA1CB7ac7Ac6896DB4A24DE3#code) | name/symbol "YT-TGLP"; owner = the issuer; allowPolicy = MASTER |
| TGLP YTMarket | `0xf0BAeD5FF09731C1278fd0b843681E906985c1d0` | [yes](https://amoy.polygonscan.com/address/0xf0BAeD5FF09731C1278fd0b843681E906985c1d0#code) | fixed-price YT market, settles in USDC |

> **Verification gotcha (recorded for the next signing session):** these must be compiled from the
> Multi-Asset worktree; its `lib/` submodule revisions are what reproduce the deployed bytecode, while
> the main-repo checkout yields a different metadata hash and will NOT verify. Additionally,
> `forge verify-contract` failed on BOTH LendingVaults with "deployment bytecode does NOT match" until
> `--compiler-version v0.8.24+commit.e11b9ed9` and `--num-of-optimizations 200` were passed EXPLICITLY
> (foundry was not transmitting them for those two; the other three verified without it). Independent
> local proof was obtained first: the on-chain creation input equals the locally compiled creation
> bytecode plus the documented constructor args, with lengths reconciling exactly.

> **Carried forward (mixed-version state, for the YT-v2 migration):** TGLP's yield stack is v1
> `YieldToken`. PR #264 wires `YieldTokenV2` into `YieldIssuer`, so stacks deployed after that merge
> mint v2 while TGLP stays v1 and lacks v2's issuer primitives. TGLP must ride the SAME coordinated,
> Ilan-gated YT-v2 migration wave rather than a bespoke later redeploy.

## Base Sepolia (84532) - Phase-0 stack - Etherscan V2 - COMPLETE (2026-07-17)

The MBT + full ACE stack deployed for the (pending) Base go-live. All 11 Etherscan-V2-verified.
**Compiler: solc 0.8.24, optimizer 200, `via_ir=FALSE`** - the Base stack deployed at repo HEAD
`cad3021` (2026-06-28) whose default profile had via_ir off; current main flipped the default to
via_ir=true (post-#134), which changes ONLY the complex RWATokenCCT/pool bytecode (small ACE contracts
are byte-identical either way). RWATokenCCT source unchanged since 0c92652; libs since 42ecbbf.
Audit basis: RWATokenCCT + ERC20MintExtractor covered by the Multi-Asset SS1 Slither audit (same source);
PolicyEngine/AllowPolicy/PausePolicy/TransferExtractor = vendored Chainlink ACE (BUSL grant); pool = CCIP; feed = Chainlink mock.

> **PROVENANCE-GAP / mainnet-readiness lesson (Lane-Wiring, 2026-07-17):** the live Base deploy's broadcast is
> NOT committed (the 84532 run-latest.json is an unrelated April deploy; the 06-28 artifact is a dry-run only),
> so there is no committed deploy SHA - reconstructed as `cad3021` from source+lib+metadata. Deploying on the
> default profile then later flipping that profile's `via_ir` breaks reproducibility of already-live contracts.
> For mainnet: pin the exact deploy profile + commit the broadcast.

| Contract | Address | Verified |
|---|---|---|
| RWATokenCCT (MBT) | `0x2aedDb48a3b6028244ed31C0fdDEB2B72Fce3bE1` | [yes](https://sepolia.basescan.org/address/0x2aedDb48a3b6028244ed31C0fdDEB2B72Fce3bE1#code) |
| BurnMintTokenPool | `0x49f1aa7C252183E79D4944c54698a335Ed65C150` | [yes](https://sepolia.basescan.org/address/0x49f1aa7C252183E79D4944c54698a335Ed65C150#code) |
| PolicyEngine (impl) | `0x52FBB6bD6522eF2EcF7c05b6be42F460ec7518bf` | [yes](https://sepolia.basescan.org/address/0x52FBB6bD6522eF2EcF7c05b6be42F460ec7518bf#code) |
| PolicyEngine (proxy) | `0xa1a58d40490CeB9a36bA2c8e0Bf457aba591452c` | [yes](https://sepolia.basescan.org/address/0xa1a58d40490CeB9a36bA2c8e0Bf457aba591452c#code) |
| AllowPolicy (impl) | `0x752145eE04594EA218473C79F0dAFefAD4a1765C` | [yes](https://sepolia.basescan.org/address/0x752145eE04594EA218473C79F0dAFefAD4a1765C#code) |
| AllowPolicy (proxy) | `0x2b7B6f835D9C416e84ce85F982D37b5a78fbe6D8` | [yes](https://sepolia.basescan.org/address/0x2b7B6f835D9C416e84ce85F982D37b5a78fbe6D8#code) |
| PausePolicy (impl) | `0xfB1aA3d4A41DF3d858CCde72B94acCc0F2AeD9b1` | [yes](https://sepolia.basescan.org/address/0xfB1aA3d4A41DF3d858CCde72B94acCc0F2AeD9b1#code) |
| PausePolicy (proxy) | `0x55d0Ed9c1232646ECeabAaA95bc74892B6A2DEEC` | [yes](https://sepolia.basescan.org/address/0x55d0Ed9c1232646ECeabAaA95bc74892B6A2DEEC#code) |
| ERC20TransferExtractor | `0xa6Bd2AD27Ffa124237411c0877D48b5496BaF6d7` | [yes](https://sepolia.basescan.org/address/0xa6Bd2AD27Ffa124237411c0877D48b5496BaF6d7#code) |
| ERC20MintExtractor | `0xAB062D019021134D0dcf4b6bA9FBb9FddB07Cd38` | [yes](https://sepolia.basescan.org/address/0xAB062D019021134D0dcf4b6bA9FBb9FddB07Cd38#code) |
| MockV3Aggregator (PoR feed) | `0x3520f45AfBbEc76484bD146Cb260C03D0Faec5F5` | [yes](https://sepolia.basescan.org/address/0x3520f45AfBbEc76484bD146Cb260C03D0Faec5F5#code) |

Status: verified + audited (this section). Base is NOT yet in the dApp - pending the PoR keeper running,
the config/LIVE_DIRECTED PR, and pre-demo-check green. When those land, Base flips live.

## CCID Mainnet Agent TESTBED (Base 8453 + Ethereum 1) - Etherscan V2 - COMPLETE (2026-07-20)

> ### READ THIS FIRST: this is a MAINNET TESTBED, NOT the Onchain Bridges mainnet launch.
> These contracts are deployed under `policy_mainnet_testbed_1usd_cap`: a sanctioned, $1-per-tx-capped
> agent testbed, deployed from a FRESH BURNER key A2 `0xEd3434017182ae8bFcd33cDbaEE593eA1c453A4A`.
> The protocol has NOT launched on mainnet. `policy_mainnet_secure_signing_gate` remains INTACT and
> uncrossed: the protocol admin key `0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c` is **nonce 0 on BOTH
> Base and Ethereum** (independently verified by the Verify & Ledger gate, 2026-07-20; Ethereum balance
> 0 wei). MBT `totalSupply() == 0` on both chains: no value has been minted. Do not cite these addresses
> as evidence that OB is live on mainnet.
>
> ### Recorded deliberately: a wrong-key near-miss, and the control that caught it
> **The control worked.** A MANDATORY pre-broadcast signer check is codified at RUNBOOK section 0.5 and in
> CLAUDE.md's Hard Rules (merged as PR #268), with the procedure at
> `SESSION-COORDINATION/MAINNET-KEY-SAFETY.md`: before ANY mainnet `--broadcast`, run
> `cast wallet address --private-key $PRIVATE_KEY` and confirm it prints the intended deployer; anything
> else is a STOP condition. That rule exists because of an earlier wrong-key near-miss (2026-07-18), and it
> is why every mainnet transaction in this sequence was preceded by a signer verification.
>
> **What happened.** A repo `.env` sets `PRIVATE_KEY=0xFc99...` (the protocol admin key) and silently
> clobbers a manually-loaded key in the same shell. During the Ethereum vault step this caused a deploy to
> be attempted from `0xFc99` instead of the A2 burner. One run reached forge and FAILED TO SEND on
> insufficient funds (`0xFc99` holds 0 ETH on Ethereum), producing 0 receipts; a later recurrence was
> stopped by the signer check before any broadcast. The step was reset to A2 and re-run successfully.
>
> **Verified outcome (independently confirmed by this gate, not accepted on report):** `0xFc99` is
> **nonce 0 on BOTH Base and Ethereum**, with 0 wei on Ethereum. NO transaction was signed by the protocol
> admin key on mainnet, NO state changed, NO funds were at risk, and `policy_mainnet_secure_signing_gate`
> was never crossed.
>
> **No key was exposed or compromised.** The failure mode was a wrong key LOADED into a shell, not a key
> leaked. `0xFc99`'s address is already public from testnet deployments, so this entry discloses nothing
> that was not already on-chain.
>
> This is recorded because an audit ledger that lists only successes is not an audit ledger. The evidence
> that a control works is that it catches something.

**Deploy provenance.** Built from branch `ccid-base-testbed-deploy` at commit `850cf43` (scripts), with
committed broadcast evidence (`broadcast/{DeployWithACE,DeployCCIDRegistries,DeployKycAllowlister,DeployTestbedVault,WireLane}.s.sol/{8453,1}/run-latest.json`).
Compiler for ALL contracts: **solc v0.8.24+commit.e11b9ed9, optimizer runs 200, `FOUNDRY_PROFILE=ccid`
(via_ir=true)** - every deploy script ran under that profile, and all 48 verifications matched it with no
default-profile fallback needed. Local `ccid`-profile build reproduced each deployed creation bytecode as
an exact prefix, i.e. including the metadata hash.

**Twin-address property (chain-qualify everything).** Every contract below has the IDENTICAL address on
Base and Ethereum (A2 was nonce-0 on both and the deploy sequence is identical, so CREATE addresses twin).
Identity was therefore resolved PER CHAIN via `typeAndVersion`, never assumed. The discriminator proving
two genuinely distinct chains is the CCIP router on the pool: Base `0x881e3A65B4d4a04dD529061dd0071cf975F58bCD`
vs Ethereum `0x80226fc0Ee2b096224EeAc085Bb9a8cba1146f7D` (both "Router 1.2.0"). All 24 addresses also have
identical bytecode lengths on both chains.

**On-chain identity + wiring (verified independently on each chain, keyed RPC).** All contracts code-present
with correct `typeAndVersion`; `pool.getToken() == MBT`; `KycAllowlister.allowPolicy() == AllowPolicy`;
`AllowPolicy.owner() == KycAllowlister`; `KycAllowlister.owner() == KycAllowlister.allowlister() == A2`;
MBT reads "Miami Beach Tower"/MBT/18; `LendingVault.collateralToken() == MBT`.

**Policy chain / VALIDATOR ATTACH.** The attach was deliberately deferred to last (after the credential
backfill and parity proof, since attaching into a zero-credential registry would revert every mint/bridge),
and was authorized directly by Ilan.

**BASE 8453: ATTACHED and ENFORCING, verified by this gate 2026-07-20.**
`getPolicies(MBT, transfer|transferFrom|mint) == [AllowPolicy 0xf5BC0a32, PausePolicy 0x859864b8,
CredentialRegistryIdentityValidatorPolicy 0x7488f4cE]` on ALL THREE selectors, validator at index 2.
Attach txs all receipt status 1: `0xf83f79a06d1078f7d2b3cb5f0a8c1be53187dc5a2a030623dddb48fd6a111a24`,
`0xfda0c1615d44b7b15def6a56bf87742cc6b6738636cf313d69b6cdd8a9af5721`,
`0xa60a70d7e78675674b44731a6ba46e6b9d94598e5df67cc223a75b38dffac904` (blocks 48907018-48907020).
`validate()`: A2 = true, Pool = true; negative controls `0x...dEaD`, the MBT token, and a synthetic address
all = false.

**END-TO-END ENFORCEMENT PROOF (not merely "installed").** Because the credentialed set EQUALS the
allowlisted set, any uncredentialed sender is also non-allowlisted, so AllowPolicy at index 0 rejects first
and an ordinary revert proves NOTHING about the validator. The validator was therefore isolated with an
`eth_call` STATE OVERRIDE (no transaction, no state change, no address stranded), gated on a positive
control proving the endpoint honours overrides (`addressAllowed(synthetic)` = false without, true with):
- A) no override, synthetic sender not allowlisted: rejecting policy = `0xf5BC0a32...` (AllowPolicy)
- B) override on, synthetic sender ALLOWLISTED but UNCREDENTIALED: rejecting policy = `0x7488f4cE...`
  (the CREDENTIAL VALIDATOR)
The rejection MOVES to the validator the moment the allowlist stops being the binding constraint, which is
end-to-end evidence the ENGINE consults it on the real transfer path. Attribution is taken from
`PolicyRunRejected(address policy, ...)` BY ADDRESS, not by reason string. Slot for the override was derived
independently (`keccak256(abi.encode(account, 0xfe3a2b7f...7800))`) and matched. Reproduced independently by
both the deploying channel and this gate.

**ETHEREUM 1: ATTACHED and ENFORCING, verified by this gate 2026-07-20** (verified independently on
Ethereum's own endpoint, NOT inferred from the Base result despite the twin addresses). Composition
identical: `[AllowPolicy, PausePolicy, Validator]` at index 2 on all three selectors. Attach txs all
receipt status 1: `0xe3bf38a471f0b6a176d9167161790620b6f0175f471035da773bade939ceec2f`,
`0xcc03528c3ba959d1aadedd3a0b14ca097e57f8e4c43e08f27ad7aaceaaaaf8e7`,
`0xd7dd40727118ddbeb4303f94cff1173001e11b0159ca3a77b096a878795052c9` (blocks 25578374-25578377).
`validate()`: A2 = true, Pool = true, MBT-token control = false, synthetic control = false. Ethereum's
endpoint was separately confirmed to honour state overrides (its own positive control, not assumed from
Base), and the A/B reproduces exactly: no override rejects at `0xf5BC0a32` (AllowPolicy), override-on
rejects at `0x7488f4cE` (the validator).

**PERMIT-PROOF (the gate PERMITS, not merely BLOCKS) - BOTH CHAINS.** credentialed A2 -> credentialed Pool,
`transfer(Pool, 0)` via `eth_call`: **NO REVERT on Base AND Ethereum.** Legitimate parties are not stranded.
> **STANDING GATE ITEM, added 2026-07-20 (credit: the CCID channel).** Every compliance attach needs a
> PERMIT-proof alongside the REJECT-proof. All of composition / `validate()` / the A/B prove only that the
> gate BLOCKS. An over-restrictive or misconfigured validator passes every one of those and still bricks the
> asset. When the token has zero holders, **"it blocks everyone" and "it works" are indistinguishable** until
> the first real transfer fails in front of an audience. Always simulate a fully-legitimate transfer too.

State at verification: enforcement LIVE on both chains, MBT `totalSupply` still 0 (nothing has moved),
allowlisted set == credentialed set == {A2, Pool}, invariant intact and unchanged by the attach.
This supersedes the earlier "validator NOT attached" carve-out on the Batch #1 PASS.

**CCIP lane wiring: PASS.** Each pool records ONLY the remote selector, which is correct. Base pool
`isSupportedChain(5009297550715157269)` = true; Ethereum pool `isSupportedChain(15971525489660198786)` = true;
own-selector = false. WireLane txs both receipt status 1: Base `0x04d1b8cae9c1e23b52224fcc2ec89a5b5ab9026e8cd1d477bdd6d693da81f47a`,
Ethereum `0xe75bdf0ade3721fe1fd2d013ab7f16bc6b43e984a8244946ad072f57e8b9454a`.

**AllowPolicy allowlist ENUMERATION (not just spot reads), 2026-07-20 point-in-time.** NET allowlist =
exactly **{A2, Pool `0xAd01B8A9C7497d7c560a67eEf40183064bec3A1f`}** on BOTH chains, zero disallow events.
Confirmed by TWO INDEPENDENT enumerations (Verify & Ledger and RWA Bridge Session 8), which agree exactly.

Why event-enumeration is a complete record of state for this implementation: in the deployed AllowPolicy,
`allowAddress` and `disallowAddress` are the ONLY two functions that write `allowList`, both are `onlyOwner`,
and both emit. There is no batch variant and no initializer that seeds the mapping. If a silent write path
existed, all event-based enumeration would collapse.

Ownership history matters here and corrects a natural but WRONG assumption (the current owner did not always
own it). On both chains: `OwnershipTransferred 0x0 -> A2` at the creation block (Base 48889068, Ethereum
25576114), then `A2 -> KycAllowlister 0xAD45f7B0` later (Base 48889762, Ethereum 25576489). **Both allowlist
entries were written in between, while A2 was the DIRECT owner** (Base blocks 48889087 / 48889088; Ethereum
25576135 / 25576136). So the load-bearing fact is NOT "the KycAllowlister owns it" but rather:
**A2 is the only actor in BOTH regimes** (direct owner then, sole `allowlister` via the KycAllowlister now),
and every A2 transaction is accounted for (Base nonce 50 == exactly the 50 committed broadcast receipts, and
the scanned windows contain the entry blocks above).

TWO CONDITIONS this enumeration depends on, stated because they can expire:
1. **AllowPolicy is UPGRADEABLE** (ERC-7201 storage behind an ERC1967 proxy). An implementation swap could
   introduce a write path that sets `allowList` WITHOUT emitting, silently invalidating every event-based
   enumeration. Verified: exactly ONE `Upgraded` event on each chain, at the creation block (impl
   `0x64FAe191F68e29E447Aa484A4d16648CABdE7CFc`), no mid-life upgrade, and the live impl slot still reads
   that same impl.
2. The result is POINT-IN-TIME. It expires if the `allowlister` is rotated, the proxy is upgraded, or any
   new address is allowlisted. **Re-enumerate immediately before the validator attach.**

**Audit.** OB-authored contracts (RWATokenCCT, ERC20MintExtractor, KycAllowlister, TestUSDC, LendingVault)
are BYTE-IDENTICAL to `origin/main` (0 diff vs the deploy commit), so prior reviews carry:
[MULTIASSET-SS1-AUDIT-2026-07-16](audits/MULTIASSET-SS1-AUDIT-2026-07-16.md) (RWATokenCCT, ERC20MintExtractor),
[SECURITY-REVIEW-2026-06-17](audits/SECURITY-REVIEW-2026-06-17.md) (LendingVault, TestUSDC), and the CCID
gate review at PR #136 (KycAllowlister). Vendored: Chainlink ACE (PolicyEngine, AllowPolicy, PausePolicy,
ERC20TransferExtractor, IdentityRegistry, CredentialRegistry, CredentialRegistryIdentityValidatorPolicy,
OnlyAuthorizedSenderPolicy) under the **BUSL** additional-use grant, flagged for the commercial-license gate;
BurnMintTokenPool = Chainlink CCIP; MockV3Aggregator = Chainlink mock; ERC1967Proxy = OpenZeppelin.
**No copied non-permissive code.**

All 48 targets (24 addresses x 2 chains) are explorer-verified, each **as the expected contract name**, and
that status was independently re-confirmed by this gate via `getsourcecode` rather than taken from the
verification tooling's own output.

| Contract | Address (same on both chains) | Base 8453 | Ethereum 1 | Provenance |
|---|---|---|---|---|
| Token PolicyEngine (proxy) | `0xeB2215E7abA5B5D98BA28eEAc7A0b5F96e550EAF` | [yes](https://basescan.org/address/0xeB2215E7abA5B5D98BA28eEAc7A0b5F96e550EAF#code) | [yes](https://etherscan.io/address/0xeB2215E7abA5B5D98BA28eEAc7A0b5F96e550EAF#code) | OZ ERC1967Proxy |
| Token PolicyEngine (impl) | `0xDa7b6C95dfb74a992c8B26A3B5067B8ae5215b2e` | [yes](https://basescan.org/address/0xDa7b6C95dfb74a992c8B26A3B5067B8ae5215b2e#code) | [yes](https://etherscan.io/address/0xDa7b6C95dfb74a992c8B26A3B5067B8ae5215b2e#code) | ACE (BUSL) |
| AllowPolicy (proxy) | `0xf5BC0a322BB5D56324e1a2B95142596E1382Cefe` | [yes](https://basescan.org/address/0xf5BC0a322BB5D56324e1a2B95142596E1382Cefe#code) | [yes](https://etherscan.io/address/0xf5BC0a322BB5D56324e1a2B95142596E1382Cefe#code) | OZ ERC1967Proxy |
| AllowPolicy (impl) | `0x64FAe191F68e29E447Aa484A4d16648CABdE7CFc` | [yes](https://basescan.org/address/0x64FAe191F68e29E447Aa484A4d16648CABdE7CFc#code) | [yes](https://etherscan.io/address/0x64FAe191F68e29E447Aa484A4d16648CABdE7CFc#code) | ACE (BUSL) |
| PausePolicy (proxy) | `0x859864b8F4f1be248D62A45aB8051059500B65c9` | [yes](https://basescan.org/address/0x859864b8F4f1be248D62A45aB8051059500B65c9#code) | [yes](https://etherscan.io/address/0x859864b8F4f1be248D62A45aB8051059500B65c9#code) | OZ ERC1967Proxy |
| PausePolicy (impl) | `0x6Bd50d29343805D8E1F3041436e93B6E2Cc4870f` | [yes](https://basescan.org/address/0x6Bd50d29343805D8E1F3041436e93B6E2Cc4870f#code) | [yes](https://etherscan.io/address/0x6Bd50d29343805D8E1F3041436e93B6E2Cc4870f#code) | ACE (BUSL) |
| ERC20TransferExtractor | `0x2d86a9f6Fed041C4eE126FaE3C220542F0c55Cd8` | [yes](https://basescan.org/address/0x2d86a9f6Fed041C4eE126FaE3C220542F0c55Cd8#code) | [yes](https://etherscan.io/address/0x2d86a9f6Fed041C4eE126FaE3C220542F0c55Cd8#code) | ACE (BUSL) |
| ERC20MintExtractor | `0xfd8880CdCA20Dc9BD00810B6f66Abff5073CC4bd` | [yes](https://basescan.org/address/0xfd8880CdCA20Dc9BD00810B6f66Abff5073CC4bd#code) | [yes](https://etherscan.io/address/0xfd8880CdCA20Dc9BD00810B6f66Abff5073CC4bd#code) | OB (SS1 audit) |
| MockV3Aggregator (PoR) | `0x0Aa476554284A058E65cCa6a8E469e26A4467aeE` | [yes](https://basescan.org/address/0x0Aa476554284A058E65cCa6a8E469e26A4467aeE#code) | [yes](https://etherscan.io/address/0x0Aa476554284A058E65cCa6a8E469e26A4467aeE#code) | Chainlink mock |
| RWATokenCCT (MBT) | `0xB40195A6ED439ca3AB7f6db6EafF56e7c5d26853` | [yes](https://basescan.org/address/0xB40195A6ED439ca3AB7f6db6EafF56e7c5d26853#code) | [yes](https://etherscan.io/address/0xB40195A6ED439ca3AB7f6db6EafF56e7c5d26853#code) | OB (SS1 audit) |
| BurnMintTokenPool | `0xAd01B8A9C7497d7c560a67eEf40183064bec3A1f` | [yes](https://basescan.org/address/0xAd01B8A9C7497d7c560a67eEf40183064bec3A1f#code) | [yes](https://etherscan.io/address/0xAd01B8A9C7497d7c560a67eEf40183064bec3A1f#code) | Chainlink CCIP |
| CCID RegistryAdminEngine (proxy) | `0x68bd4472298fCC5feDDdd0e2e718Ec2e66C83f0e` | [yes](https://basescan.org/address/0x68bd4472298fCC5feDDdd0e2e718Ec2e66C83f0e#code) | [yes](https://etherscan.io/address/0x68bd4472298fCC5feDDdd0e2e718Ec2e66C83f0e#code) | OZ ERC1967Proxy |
| CCID RegistryAdminEngine (impl) | `0x469dEb78304b300e8fB7b4fbc688DB99c5ff36fc` | [yes](https://basescan.org/address/0x469dEb78304b300e8fB7b4fbc688DB99c5ff36fc#code) | [yes](https://etherscan.io/address/0x469dEb78304b300e8fB7b4fbc688DB99c5ff36fc#code) | ACE (BUSL) |
| CCID IssuerGatePolicy (proxy) | `0x009CE93076ed4Eb9A3c79D83ad82421e9839bCf4` | [yes](https://basescan.org/address/0x009CE93076ed4Eb9A3c79D83ad82421e9839bCf4#code) | [yes](https://etherscan.io/address/0x009CE93076ed4Eb9A3c79D83ad82421e9839bCf4#code) | OZ ERC1967Proxy |
| CCID IssuerGatePolicy (impl) | `0xdF8Cc9E0dB4569B884Ea3d6D2532B2a8a16C52D2` | [yes](https://basescan.org/address/0xdF8Cc9E0dB4569B884Ea3d6D2532B2a8a16C52D2#code) | [yes](https://etherscan.io/address/0xdF8Cc9E0dB4569B884Ea3d6D2532B2a8a16C52D2#code) | ACE (BUSL) |
| CCID IdentityRegistry (proxy) | `0x49eE2710b173590262bb70d697363a2B0B0d9D7d` | [yes](https://basescan.org/address/0x49eE2710b173590262bb70d697363a2B0B0d9D7d#code) | [yes](https://etherscan.io/address/0x49eE2710b173590262bb70d697363a2B0B0d9D7d#code) | OZ ERC1967Proxy |
| CCID IdentityRegistry (impl) | `0x2aD2000cc082DFdbBcb0a3a729497e52dd5b1D1d` | [yes](https://basescan.org/address/0x2aD2000cc082DFdbBcb0a3a729497e52dd5b1D1d#code) | [yes](https://etherscan.io/address/0x2aD2000cc082DFdbBcb0a3a729497e52dd5b1D1d#code) | ACE (BUSL) |
| CCID CredentialRegistry (proxy) | `0x8934085baA17E322465925E17d1EF5EC48602322` | [yes](https://basescan.org/address/0x8934085baA17E322465925E17d1EF5EC48602322#code) | [yes](https://etherscan.io/address/0x8934085baA17E322465925E17d1EF5EC48602322#code) | OZ ERC1967Proxy |
| CCID CredentialRegistry (impl) | `0x8531ED4dF95c8b86014a06bAf3bFD7a76f6F1d10` | [yes](https://basescan.org/address/0x8531ED4dF95c8b86014a06bAf3bFD7a76f6F1d10#code) | [yes](https://etherscan.io/address/0x8531ED4dF95c8b86014a06bAf3bFD7a76f6F1d10#code) | ACE (BUSL) |
| CCID ValidatorPolicy (proxy) | `0x7488f4cE569af8B1e6FcC67Fd5fDFad210bC0599` | [yes](https://basescan.org/address/0x7488f4cE569af8B1e6FcC67Fd5fDFad210bC0599#code) | [yes](https://etherscan.io/address/0x7488f4cE569af8B1e6FcC67Fd5fDFad210bC0599#code) | OZ ERC1967Proxy; ATTACHED on Base (enforcing), Ethereum pending |
| CCID ValidatorPolicy (impl) | `0x85e4E3793e1BeA928AfF7C9EB229409FEd343EC4` | [yes](https://basescan.org/address/0x85e4E3793e1BeA928AfF7C9EB229409FEd343EC4#code) | [yes](https://etherscan.io/address/0x85e4E3793e1BeA928AfF7C9EB229409FEd343EC4#code) | ACE (BUSL); ATTACHED on Base (enforcing), Ethereum pending |
| KycAllowlister | `0xAD45f7B00a7fB7c3a72Bd4f84969058f6910428A` | [yes](https://basescan.org/address/0xAD45f7B00a7fB7c3a72Bd4f84969058f6910428A#code) | [yes](https://etherscan.io/address/0xAD45f7B00a7fB7c3a72Bd4f84969058f6910428A#code) | OB (#136) |
| TestUSDC (mock) | `0x35cb83515bc206d753cd97db08c741a2A3906ebf` | [yes](https://basescan.org/address/0x35cb83515bc206d753cd97db08c741a2A3906ebf#code) | [yes](https://etherscan.io/address/0x35cb83515bc206d753cd97db08c741a2A3906ebf#code) | OB (2026-06-17) |
| LendingVault ("Meridian") | `0xD95ff9F192C078985d24cb070d9e647aC59F6D69` | [yes](https://basescan.org/address/0xD95ff9F192C078985d24cb070d9e647aC59F6D69#code) | [yes](https://etherscan.io/address/0xD95ff9F192C078985d24cb070d9e647aC59F6D69#code) | OB (2026-06-17) |

> ### STANDING INVARIANT for EVERY policy attach on EVERY chain (found 2026-07-20, not a defect today)
> `PolicyEngine.run` does NOT unconditionally run every attached policy. It iterates the chain and
> **RETURNS IMMEDIATELY on the first policy that returns `PolicyResult.Allowed`** (enum is
> {None, Allowed, Continue}). A policy that reverts rejects the whole call; a policy returning `Continue`
> passes control to the next one.
> **Therefore any policy that returns `Allowed` short-circuits every policy AFTER it in that chain**, and
> `getPolicies` would still show a perfect-looking composition while the downstream policies never run.
> The ACE package ships exactly such a contract (`BypassPolicy`, same `policies/` directory).
> **THE RULE (general form): before ANY policy attach on ANY chain, read the `run()` return value of
> EVERY policy already at a LOWER INDEX than the one being attached, and confirm each returns `Continue`.
> Checking only the policy you are adding is INSUFFICIENT, because it is the policies AHEAD of yours that
> can skip you.** Index order is load-bearing, not cosmetic.
>
> Verified for the current MBT chain, all three links (an earlier version of this note checked only index 0
> and index 2 and missed PausePolicy at index 1, which sits directly ahead of the credential validator):
> `AllowPolicy.run` returns `Continue` on pass (AllowPolicy.sol:107); `PausePolicy.run` returns `Continue`
> on pass and reverts `PolicyRejected("contract is paused")` when paused (PausePolicy.sol:78-80);
> `CredentialRegistryIdentityValidatorPolicy.run` returns `Continue` on pass (line 68). The full chain
> executes and there is NO bypass in any deployed chain today.
>
> **Exhaustive library audit (2026-07-20):** across every policy in
> `lib/chainlink-ace/packages/policy-management/src/policies/`, **`BypassPolicy` is the ONLY contract that
> returns `PolicyResult.Allowed`** and therefore the only one that can short-circuit a chain. All others
> (AllowPolicy, PausePolicy, IntervalPolicy, MaxPolicy, OnlyAuthorizedSenderPolicy, OnlyOwnerPolicy,
> RejectPolicy, RoleBasedAccessControlPolicy, SecureMintPolicy, VolumePolicy, VolumeRatePolicy,
> CertifiedActionValidatorPolicy) return `Continue`; the two CertifiedAction* subclasses declare no return
> of their own and inherit the base. So the concrete check is: **is `BypassPolicy` (or any custom
> `Allowed`-returning policy) present at a lower index?**
>
> **Corollary for verifying an attach:** `getPolicies` proves a policy is INSTALLED, `validate()` proves it
> DISCRIMINATES in isolation, and only an address-attributed revert proves the ENGINE consulted it. The
> engine reverts `PolicyRunRejected(address policy, string rejectReason, Payload)` naming the REJECTING
> POLICY BY ADDRESS, so attribute on that address, not on the reason string. Note that when the credentialed
> set EQUALS the allowlisted set, an uncredentialed sender is also non-allowlisted, so `AllowPolicy` (index 0)
> rejects first and such a test proves NOTHING about the validator. Isolating the validator requires an
> allowlisted-but-uncredentialed sender, which must NOT be created on mainnet (it would break the
> credentialed-superset-of-allowlist invariant and strand an address); use an `eth_call` STATE OVERRIDE of
> the AllowPolicy slot (ERC-7201 base `0xfe3a2b7f46cd21f6575f413edd13b679ddeba1bf1b08400ac236f2528fe97800`,
> slot = `keccak256(abi.encode(account, base))`) or a testnet rehearsal.
>
> **State-override tests REQUIRE a positive control.** Many endpoints silently IGNORE the override object
> rather than erroring, and you then get an ordinary AllowPolicy rejection that reads like a clean negative
> result. Before trusting the outcome, override the slot and simulate a call you expect to PASS the
> allowlist: if it still reverts with "address is not on allow list", the override was dropped and the test
> is VOID, not negative. A silently-ignored override is a bug signal, never a data point.

**CCID issuer authorization (verified by this gate, 2026-07-20).** The fresh mainnet CCID issuer
`0xa50BDbb59b3916BEf55aeaC87fa268B0A190e9cC` is authorized on the IssuerGate
`0x009CE93076ed4Eb9A3c79D83ad82421e9839bCf4` on BOTH chains: `senderAuthorized(issuer) == true` on Base and
Ethereum; gate `owner() == A2` on both; authorization txs both receipt status 1 with `to` = the IssuerGate
(Base `0x8c89b76a12c9fcddb1e32571ceee96795d9b57668840d9061002db28a3fb2f9d` block 48906027, Ethereum
`0x1c45f21b5b241fbc26641ccbad64733552767bcebc63bc3d30a3f11e37803505` block 25578157).
**BY DESIGN, NOT AN OVERSIGHT:** `senderAuthorized(A2) == true` is intentionally RETAINED on both chains so a
fault in the fresh issuer key cannot lock out issuance. That means TWO keys can issue credentials today.
Moving to a single-issuer posture (revoking A2) is a separate later step, gated on the backfill being proven.

> **Gate-tooling gotchas recorded for the next mainnet batch:** (1) the Etherscan V2 key's free plan does
> NOT cover Base for the `proxy` module (`eth_getCode`) or for `logs`, but the `contract` module
> (`getsourcecode` / `verifysourcecode`) DOES work on Base. (2) Alchemy's free tier caps `eth_getLogs` to a
> **10-block range**, which is what defeats naive allowlist enumeration. Do NOT conclude the chain is
> unreadable: the PUBLIC Base endpoint `https://mainnet.base.org` serves roughly **9000-block** log ranges
> and is the better endpoint for Base log scans (CCID scanned 48889000..48905848 in one pass, zero errors).
> Pick the endpoint per capability rather than assuming the keyed one is always superior. (3) `forge verify-contract` needs `--watch`; without it submissions silently do not land. (4) Under
> the 3/sec rate limit, forge reported FAILED for 20 contracts whose verification had actually SUCCEEDED, so
> forge stdout is not ground truth: always re-check with `getsourcecode`.

## Token Controls Phase 2 v2 stack (Sepolia 11155111) - Etherscan V2 - COMPLETE (2026-07-21)

The ERC-3643 issuer-primitive stack (AgentRole, freeze/unfreeze, forcedTransfer, recoveryAddress, batch ops).
Deployed by Ilan, signer `0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c` (correct for Sepolia TESTNET; the
never-0xFc99 rule is MAINNET-scoped). Gate run by Verify & Ledger; address subset independently re-verified
by RWA Bridge Session 8.

**Audit:** [TOKEN-CONTROLS-V2-AUDIT-2026-07-20](audits/TOKEN-CONTROLS-V2-AUDIT-2026-07-20.md). FRESH review,
not cite-prior: `RWATokenV2` and `YieldTokenV2` are NEW sources and the earlier reviews cover only v1
`RWATokenCCT` / `YieldToken`. Result: 0 Critical, 0 High, **1 Medium (F-1)**, remainder informational, plus
a recorded POSITIVE finding that the batch operations correctly synthesise a per-item single-op policy run
(the naive implementation would be a total compliance bypass on a default-allow engine).

**F-1 drove a full redeploy.** The Medium finding was that `_requireAdmitted` failed CLOSED on the allowlist
but OPEN on the credential validator, so the agent bypass paths could be configured allowlist-only while
ordinary transfers stayed credential-gated. Fixed at the CONTRACT level (PR #277, merge `2505dc4`):
`setAdmissionCheckers` now reverts `AdmissionValidatorRequired()` on a zero validator, and a deliberate
waiver requires `setAdmissionCheckersUnsafe`, which emits `AdmissionValidatorWaived` so the choice is
auditable ON-CHAIN rather than inferred from a zero field. Because `YieldIssuer.rwaToken`/`yieldToken`,
`YTMarket.ytToken`, `LendingVault.collateralToken` and the pool's token are all `immutable` (verified: no
setter exists to re-point any of them), the token redeploy forced the ENTIRE 6-contract stack.

**F-1 closure PROVEN IN THE DEPLOYED BYTECODE, not merely built from fixed source.** Simulated FROM THE
OWNER, because `setAdmissionCheckers` is `onlyOwner` and the modifier runs BEFORE the guard, so a call from
any other sender reverts for an unrelated reason and would be a FALSE GREEN:

| Call | Result |
|---|---|
| NEW `setAdmissionCheckers(allow, 0)` from owner | REVERT `0xa2bdd2c2` = `AdmissionValidatorRequired()` |
| RETIRED token, same call | **SUCCESS** (the hazard was real, and is preserved on the dead token) |
| NEW `setAdmissionCheckers(allow, VALIDATOR)` from owner | **SUCCESS** (positive control: the setter is not bricked) |
| NEW `setAdmissionCheckersUnsafe` from owner / from NON-owner | SUCCESS / REVERT (hatch present AND owner-gated) |
| RETIRED token `setAdmissionCheckersUnsafe` | REVERT (function absent pre-fix) |

The retired-token rows are what make this a proof rather than a positive-only assertion; the positive-control
row is what rules out an over-restrictive fix that would satisfy every reject-test while being unusable.

### Canonical addresses (all 6 explorer-verified; status independently re-confirmed via `getsourcecode`, NOT taken from the verification tooling)

| Contract | Address | Verified |
|---|---|---|
| RWATokenV2 (MBT v2) | `0x661d63bE70A1eb9A904636F5dDcaDEeFdF6337B5` | [yes](https://sepolia.etherscan.io/address/0x661d63bE70A1eb9A904636F5dDcaDEeFdF6337B5#code) |
| BurnMintTokenPool v2 | `0xA2eB25A10043C64096da1D97C1Fcc6259Bc909AC` | [yes](https://sepolia.etherscan.io/address/0xA2eB25A10043C64096da1D97C1Fcc6259Bc909AC#code) |
| YieldIssuer (v2 refs) | `0xE3B2de4218C0Ab1C9149AAAe5135BF3D54d367c8` | [yes](https://sepolia.etherscan.io/address/0xE3B2de4218C0Ab1C9149AAAe5135BF3D54d367c8#code) |
| YieldTokenV2 (YT v2) | `0x3043A478196b8c1F39561eB336A10823ea5b652F` | [yes](https://sepolia.etherscan.io/address/0x3043A478196b8c1F39561eB336A10823ea5b652F#code) |
| YTMarket v2 | `0x2401F3Bde7Bc6c0381B3ca030a4769DaA383938D` | [yes](https://sepolia.etherscan.io/address/0x2401F3Bde7Bc6c0381B3ca030a4769DaA383938D#code) |
| LendingVault v2 ("Meridian Lending") | `0xdeA110fDC0bE42d6c0832A12e5aE9ff075e92732` | [yes](https://sepolia.etherscan.io/address/0xdeA110fDC0bE42d6c0832A12e5aE9ff075e92732#code) |

Compiler for all 6: **`FOUNDRY_PROFILE=ccid` (via_ir=true), solc v0.8.24+commit.e11b9ed9, optimizer runs 200,
EVM cancun, bytecodeHash=ipfs.** The DEFAULT profile does not even compile the tree (stack-too-deep in the
vendored CCID validator). Pre-submission proof for each: on-chain creation input EQUALS the locally built
creation bytecode plus ABI-encoded constructor args, exact prefix match. Constructor args were DERIVED from
the on-chain creation input (the broadcast artifact is not on `origin/main`) and then cross-checked against
the deploying channel's stated values; all cross-checks passed.

> **REPRODUCIBILITY GOTCHA, load-bearing for anyone re-verifying these.** The build must have **14
> remappings, not 15**. An auto-detected `ds-test/=lib/openzeppelin-contracts/lib/forge-std/lib/ds-test/src/`
> remapping, present in the shared checkout (forge fetches it as an untracked directory) but ABSENT in the
> deploying environment, changes the solc metadata and therefore the trailing IPFS hash. The code bytes match
> either way; only the metadata hash differs, so it presents as a near-miss rather than as drift. The
> directory must be absent when rebuilding. This is the same class as the Multi-Asset SS2 lesson that the
> `lib/` submodule state sets the metadata hash.

### RETIRED, superseded by the above (recorded, NOT deleted)

Pre-F-1 deploy of 2026-07-20: `0xE09c01A6e308563fCd0357EC859A07CFB6ECEAdF` (RWATokenV2),
`0xC3F2C961eBC5526A9c7795E34FAD02F37c805079` (pool), `0x4AC18a2B5264fdC1De79088d96552Abf9e777095` (issuer),
`0x70d56FEA4bDD7E140e85687A1fa29Dae81Ba7d16` (YT), `0x85cdE2C63d11b37387AcD46f31e5F32D60454CA3` (market),
`0xE1f80f47c5128D68Dce355Ed58f97efC29EC553e` (vault). **F-1 is both the reason they exist and the reason they
were replaced.** They are retained because the retired token is the control that proves the fix works. None of
the 6 new addresses collides with a retired one or with any address previously recorded in this ledger
(checked explicitly; a redeploy landing on a previously-ledgered address would otherwise read as
"already verified").

### State at gate time, and the standing conditions

v1 MBT `0x044951AB...` is UNTOUCHED throughout: its chain still reads `[AllowPolicy, PausePolicy, Validator,
MaxBalance, TransferRestrict]` on transfer. Wiring is internally consistent and points ONLY at the new set.
The engine `0xd4b9F980...` is REUSED, not redeployed.

### ATTACH COMPLETE: v2 is COMPLIANCE-ENFORCING (2026-07-21, verified by this gate)

Ilan signed the attach (19 txs, all success, blocks 11322496-11322518). Policy-attach preceded any mint,
config or exposure, so the standing condition above was SATISFIED. `totalSupply` remains 0 and v2 stays
outside CCIP until Phase B.

**Composition: v2 is IDENTICAL to LIVE v1 on all three selectors** (compared against the live v1 chain, not
against an expected array, since expected-vs-file is circular):
`[AllowPolicy 0x7858b6e6, PausePolicy 0x2038964A, Validator 0xdAbf8046, MaxBalance 0xe2e7Dce3,
TransferRestrict 0x01B887BA]` on transfer/transferFrom, and `[..., SupplyLimit 0x0582AeDF]` on mint.
Validator at index 2, CCID convention preserved. Config landed: `admissionValidator == 0xdAbf8046` (so the
F-1 invariant HOLDS on the live config), one agent, mint authority on pool + issuer only.

**PERMIT-PROOF: PASSES.** An admitted + credentialed sender is NOT blocked. Required because every
reject-test below would pass identically on a token that rejects EVERYBODY, which is bricked rather than
compliant.

**REJECT-PROOFS, both legs, attributed BY POLICY ADDRESS with a v1 control:**

| Sender state | v2 rejects at | v1 control |
|---|---|---|
| neither allowlisted nor credentialed | AllowPolicy `0x7858b6e6` | same |
| **allowlisted but UNCREDENTIALED** | **Validator `0xdAbf8046`** | same |

The second row is the CREDENTIAL leg proven BEHAVIOURALLY, not merely by composition. No such wallet exists
on Sepolia (and creating one would break the credentialed-superset invariant), so it was produced with an
`eth_call` STATE OVERRIDE of the AllowPolicy slot: zero transactions, zero state change, no address stranded.
Gated on its own positive control first (`addressAllowed(synthetic)` false without the override, true with),
because an endpoint that silently ignores overrides returns an ordinary AllowPolicy rejection that reads like
a clean negative. The deploying channel had honestly declared this leg as unproven on v2 after its intended
test wallet turned out to have been credentialed since the label was written; the override closes it without
needing any wallet at all.

> **The unwired window is safe PROCEDURALLY, not MECHANICALLY.** `onlyMintBurn` and `onlyAgent` both grant the
> OWNER an implicit bypass of their mappings, so the owner can mint and can call the agent paths even though
> `mintBurnAuthorities` and `isAgent` read false for every address. **Attach-before-mint is a rule to be
> FOLLOWED, not a property that is enforced.** See the audit's precision box for the bound: the bypass is on
> the AUTHORITY check, not on compliance, so for mint/transfer/transferFrom the full ACE chain still applies
> to the owner.
>
> **This is exactly WHY the ordering invariant is load-bearing rather than tidy.** The owner can always mint,
> so nothing mechanical stops a pre-attach mint into a token with an EMPTY policy chain, which would be an
> ungated issuance. Once the chain IS attached, even an owner mint runs the mint-selector policies and is
> gated like anyone else's (measured on mainnet: an owner mint to a non-allowlisted recipient reverts, named
> to AllowPolicy). The attach is therefore the step that converts a procedural promise into an enforced
> property. **Policy-attach MUST precede any mint, config or exposure, and the attach must be verified with a
> PERMIT-proof as well as reject-proofs.**

**Provenance: DURABLE.** The deploy broadcasts are committed to `origin/main` via **PR #280, MERGED at
`608759e9892b331f5a07a14c6eff9b889e8db33d`** (2026-07-21). Independently verified by this gate: that commit
is an ancestor of `origin/main`, and both archive files are present there, so the evidence no longer lives
only in a worktree. Verified byte-identical to the originals by SHA-256 before the merge: RETIRED
`run-1784597569821.json`, CANONICAL `run-1784614635493.json`. Provenance is carried in the FILENAMES
(`RETIRED-preF1fix` / `CANONICAL-postF1fix`) so the retired set is explained by the record itself rather than
only by audit prose. `run-latest.json` was deliberately EXCLUDED: a committed file whose meaning silently
changes on the next deploy is worse than no file, because a later reader would cite it believing it fixed.
The canonical archive parses to exactly the 5 top-level CREATEs above, all receipts `0x1`, with YieldTokenV2
correctly absent as a top-level CREATE (it is an internal CREATE from the YieldIssuer constructor, which is
also why it has no creation transaction of its own; this did NOT block its verification).

The ATTACH broadcast is archived in **PR #283 (OPEN at the time of writing)** as
`broadcast-archive/sepolia/ConfigureSepoliaV2Stack-ATTACH-run-1784672029577.json`, 19 txs / 19 receipts all
`0x1`. SHA-256 `d8d29fd067090fc9ad0b9f8b7cb845ca3680f13d4e92293e2363c8dfa3bc8ebb`, which this gate computed
independently against both the archived copy and the original and found identical. Recorded as OPEN rather
than implying durability; replace with the merge SHA when it lands.

## Not live (documented for completeness)

- **Base Sepolia (84532):** NOT a supported network (`SUPPORTED_CHAINS` excludes it). Early test
  deploys exist but several addresses have no code (deterministic-address artifacts). Nothing live
  to verify; revisit only if Base is added to the dApp.

(Astar Shibuya was previously listed here as DEPRECATED; it was re-activated 2026-06-19 and now has
its own verified section above.)

---
_Last updated 2026-07-16 (Multi-Asset Signing Session 1: AVR + TGLP, 38 contracts Sepolia+Amoy, Etherscan V2 verified + Slither audited, 0 Crit/High/Med in OB code) by RWA Bridge Session 8 (coordinator). All live-network canonical protocol contracts
verified (Astar Shibuya 5/5); source-based audit (3 analyzers, 0 Crit/High/Med) covers all EVM
chains + Solana._
