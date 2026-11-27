# Contract Verification + AI-Audit Ledger

> **CANONICAL FILE: `/Users/ilan/Claude Code/onchainbridges-security/CONTRACT-VERIFICATION-AUDIT-LEDGER.md`.**
> This is the single live ledger. SAME-NAME HAZARD: as of 2026-10-12 there are 38 files named
> `CONTRACT-VERIFICATION-AUDIT-LEDGER.md` under `/Users/ilan/Claude Code` (worktree + docs/public snapshots). They
> are STALE COPIES, not the record - e.g. `OnchainBridges-RWA-testnet/docs/public/CONTRACT-VERIFICATION-AUDIT-LEDGER.md`
> was last touched 2026-09-04 and predates the entire LendingVault block and all BNB entries below. Resolve "the
> ledger" by THIS absolute path only; never by filename or grep across the tree. If you read a copy, state which
> file. (Verify & Ledger owns this header; the canonical docs carry the matching pointer.)

Canonical record for the verify-and-audit gate (RUNBOOK §0, ratified by Ilan 2026-06-17):
**no contract goes live in the public testnet URL until it is explorer-verified AND
AI-audited, with both recorded here.** Owned by RWA Bridge session 5. Other sessions
submit their verified link + audit result via cross-session message; session 5 records.

Columns: contract, chain, address, explorer link (verified source), AI audit (tool / score /
report URL / date), notes. "by-design" = compliance control flagged as centralization by
scanners; expected, not a finding.

> ### STANDING PUBLIC-EXPOSURE BLOCK - LendingVault repay() permissionless collateral drain (opened 2026-10-12)
> **GATE STANCE: no V&L PASS for ANY LendingVault address for public exposure, and NO collateral-safety,
> position-safety or "pausable" language through this gate, while this defect is live on origin/main.**
> Defect (per S8, found by reading origin/main): `repay()` ends in `collateralToken.safeTransfer(msg.sender,
> collateralReturn)` with no borrower check and no access modifier - ANY caller can repay ANY active position and
> receive that position's collateral. Profitable at every LTV < 100%. The Amoy position-96 recovery
> (repay 0xb8c1eadc...c12c22a, signed by Ilan, position now active=false/collateral=0, recipient 0xCf3099 MBT
> 61->64) is a LIVE POSITIVE CONTROL that the behaviour works on a deployed vault. NOT a V12 finding; must not be
> filed as one.
> Three facts that bear on the gate (per S8): (1) NO PAUSE EXISTS - no Pausable, no pause fn in LendingVault.sol
> on origin/main, so any "we can pause it" claim is FALSE. (2) setTokenPrice/setLTV ARE access-controlled, but via
> an inline `if (msg.sender != owner) revert Unauthorized();`, NOT an onlyOwner modifier - a modifier-grep reads
> them as permissionless and they are not; read the body before any finding on them. (3) ATTRIBUTION UPDATE
> 2026-10-12 (per S8/S2, not independently verified by me): the ~$560 formerly "pending attribution" is now
> attributed to OB-ORIGINATED addresses, which LOWERS LOSS, NOT EXPOSURE - the block is UNCHANGED. 0x6Ab39C090...
> 46A6 (2 AVR, Amoy Atlas pos 0) = CLAIMED OB-originated but NOT VERIFIED BY V&L, and the claim is DISPUTED as of
> 2026-10-23 (see the FLEET CENSUS provenance note at the end of this file - the ONE HOME for this question; treat
> 0x6Ab39C09 as NON-ours / to-classify, NOT as settled-ours, until Ilan confirms by name). The attribution rests on
> a transcript claim of a fresh BIP-39 wallet onboarded via allowAddress tx 0xc27971f5, which is a 4-byte fragment
> that cannot be looked up; CUSTODY UNRESOLVED - S8 corrected 2026-10-12 that "S2 holds the key" came
> from a PRIOR-session transcript and is NOT present-tense custody; an in-session BIP-39 mnemonic never persisted
> to durable store may be UNRECOVERABLE (S2 searched the tree with -a, found no accessible key file). Ilan being
> asked. 0x91F5F78d80...9107 (6 MBT, Sepolia Meridian pos 9/10/11) = probable internal
> developer/owner key (appears as DEVELOPER= in an env line; owner() of TLPT on Base Sepolia) but CUSTODY
> UNCONFIRMED - S8 explicitly does NOT assert custody from a transcript; Ilan to confirm before anyone signs from
> it. So no verified third-party collateral at risk, but do not record either 0x6Ab39C or 0x91F5F7 as settled/
> closeable until Ilan confirms custody. SEPARATE FACT, own line (does NOT change exposure): ~$560 (0x6Ab39C ~$500
> + 0x91F5F7 ~$60) may be UNRECOVERABLE BY US, not merely unattributed - if those keys are not in hand we cannot
> close those positions ourselves. This lowers our remediation-by-CLOSE-OUT ability, not the exposure, and
> STRENGTHENS the case for the CODE FIX over a close-out-and-wait approach. Closeable-by-us custody state (per S8):
> 0xCf30 Ilan-confirmed; 0xFc99 protocol admin repo-resident; 0x08Cf2b ES server-wallet (presumed, unconfirmed);
> 0x950b dust ~0.0004 MBT (not worth a tx); 0x6Ab39C + 0x91F5F7 unresolved per above. S8 also verified NO
> automated opener on Amoy Atlas (newest pos 123 opened 2026-10-07; nextPositionId stable at 124 across ~7400
> blocks) - no setLTV(0)/offline treadmill to stop before a close pass. EXPOSURE IDENTICAL: the defect is still
> permissionless, still live on origin/main, still on the public fundraising demo surface with the public repo
> showing how to use it; the ten empty hub vaults run the same code.
> Blast radius (S2 swept, reconciled vs each vault's totalCollateral(); S8-verified items marked): ~$1,800 across
> 6 live vaults / 5 chains - Amoy Atlas MBT 0xf48f2683...a797 (55 MBT), Amoy Atlas AVR 0x34aE4bc3...9218 (4 AVR),
> Plume Plumage MBT 0x8f1bb564...15f9 (12 MBT), Sepolia Meridian MBT 0x71635c5f...2f0c (6 MBT; S8-VERIFIED
> nextPositionId 17 / totalCollateral 6000009166666875003 / chainId 11155111 @ block 11901428), Shibuya Astarium
> MBT 0x3F1d5141...9519 (6 MBT), Sonic Nova wMBT 0x49f1aa7C...C150 (1 wMBT). The ten Sepolia multi-asset HUB
> vaults (§6.20) are S8-VERIFIED nextPositionId=0 / totalCollateral=0 @ block 11901428 = ZERO exposure, but they
> run the SAME vulnerable code - their zeros are "empty", NOT a PASS.
> CORRECTIONS 2026-10-12 (LOSS reductions only; EXPOSURE LINE UNCHANGED - defect still permissionless+live on
> origin/main, ten empty hub vaults run the same code, public repo shows how):
>  - SONIC ROW REMOVED from drainable: NOT exploitable. Verified by me on Sonic (chain 14601 block 19146271):
>    wMBT v2 0x670c3a58 paused()=TRUE (tAV "WrappedMBT 2.0.0"); Nova vault 0x49f1aa7C collateralToken()==0x670c3a58
>    (immutable bind to the paused token); totalCollateral 1.000005285714428575 wMBT. repay()'s
>    collateralToken.safeTransfer reverts while paused, and the USDC safeTransferFrom precedes the payout with no
>    try/catch so the WHOLE tx reverts (attacker's USDC rolled back) - not drainable, not even griefing. That ~1
>    wMBT (~$10) is PERMANENTLY FROZEN (migration pause, immutable v2 binding, off-surface since 2026-10-06 per
>    origin/main defi.ts), neither exposed nor recoverable without reversing the v2->v3 migration. (This address
>    pair is also the chain-97 CCID RegistryAdminEngine impl / IdentityRegistry impl - twins, resolved by chain.)
>  - REVISED DRAINABLE ~$1,790 (was ~$1,800) after removing Sonic. CLOSE PASS (per S2 report + S2 re-read, NOT my
>    independent sweep, NOT S8's): S2 closed 20 of 21 0xCf30 positions = 60 MBT (~$600) removed from exposure -
>    Atlas/Amoy 15 (54,58,88,92,106,110,113-118,120,122,123)=45 MBT, Plumage/Plume 3 (39,40,41)=9 MBT,
>    Astarium/Shibuya 2 (1,2)=6 MBT; Atlas nextPositionId 124 before==after (no demo borrow during the window).
>    21st = the Sonic pause-frozen one above. Remaining drainable: Amoy AVR 0x34aE4bc3 (~$1,000, incl 0x6Ab39C $500
>    + 0x08Cf2b $500) + 0xFc99 Atlas/Plume + 0x91F5F7 $60 Sepolia; S2's 0xFc99/0x6Ab39C legs still running. Treat
>    the 60 MBT as reported-and-self-verified-by-S2 until I or S8 sweep it.
>  - #516 QUARANTINE EXCEPTION (granted 2026-10-12): PR #516 adds 0x49f1aa7C to 06-contracts as the chain-97
>    IdentityRegistry IMPL inside the twin-disambiguation caveat ("...superseded Sonic Nova LendingVault v2.
>    Resolve every one by chain id 97 and typeAndVersion."). Scanned the full diff with controls: NO collateral-
>    safety / position-safety / pausable / vault-claim language. Outside the block's intent (which guards a
>    LendingVault presented AS a functioning public vault with safety language); it reduces vault-confusion.
>    CLEARED for #516 only; block otherwise UNCHANGED for every other surface.
> My own independent code-read of repay() DONE 2026-10-12 (working-tree src/defi/LendingVault.sol @ checkout
> e49c660, file last touched by 61ef58f1): fn repay(uint256,uint256) external has NO access modifier; its only
> gate is `if (!pos.active) revert PositionNotActive` (line 158) - position-active, NOT borrower; line 178
> collateralReturn = pos.collateralAmount*principalPortion/pos.borrowedUSDC; line ~200 collateralToken.safeTransfer
> (MSG.SENDER, collateralReturn) - to the caller, not pos.borrower; no msg.sender==borrower check anywhere (the
> sole `borrower` token in the body is the comment "Return collateral tokens to the borrower", which the code
> contradicts). Mechanic: caller pays the position's USDC debt (safeTransferFrom msg.sender, line 181) and receives
> its collateral; net profit whenever collateral value > debt+interest, i.e. any loan opened below 100% LTV.
> Defect shape CONFIRMED first-hand. STRONGER FORM STILL PENDING: diff this src vs origin/main HEAD + confirm the
> deployed-vault runtime bytecode matches (reboot interrupted network sweeps). The BLOCK stands regardless.
> Position 96 (Amoy Atlas) INDEPENDENTLY CONFIRMED CLOSED by me 2026-10-12: positions(96) on 0xf48f2683 @ Amoy
> chain 80002 block 50017892 = borrower 0x903b8667..., collateralAmount 0, borrowedUSDC 0, active=FALSE; same read
> gave nextPositionId 124 and totalCollateral 55.000372 MBT (corroborates S8's Amoy exposure figure first-hand).
> So the memory precondition "fix only AFTER S2's position-96 recovery" is SATISFIED - the fix is unblocked to
> proceed. The quarantine still stands, but NO LONGER because of position-96 (it is closed); it stands only
> because the fix is not yet shipped. Clears when: fix merged to origin/main + redeployed to every listed vault +
> V&L re-verified live. Owner of enumeration: S8.

> ### TOOLING TRAP that produced FOUR false findings in one day, across four independent sessions
> **zsh does not word-split an unquoted variable.** `set -- $pair` / `cast call $addr` inside a loop passes
> EMPTY arguments, and `cast` then returns empty output that reads as a real result. Observed: "NO-CODE"
> reported for 16 live contracts; "REVERT" reported for 8 healthy PoR feeds; `getPolicies` reported empty for
> a populated chain; and one session initially blamed a public RPC for it and had to retract that.
> **Its polarity is the opposite of the usual false-green: it manufactures FALSE ALARM, not false calm.** It
> mimics a genuine finding (dead contracts, mass reverts, a stripped policy chain) rather than mimicking
> health. Both are corrosive: a false green erodes safety, a false alarm erodes trust in the instrument, and
> a monitor nobody believes is already switched off.
> **Knowing about it does not protect you** - one session hit it in the very next command after reading a
> warning about it, because it yields a plausible answer rather than an error. Only the structural fix works:
> quote every expansion, and parse tables with `while IFS='|' read -r`, never by word-splitting.
> Corollary: an empty or all-negative result from a batch loop is a BUG SIGNAL until proven otherwise;
> reproduce a suspected mass-failure as a single isolated call before recording any conclusion from it.
>
> ### AN AMBIGUOUS PASS MESSAGE IS A DEFECT IN A MONITOR, even when the logic is perfect
> The Canton permit-probe's negative-control mode printed "ALL HEALTHY" on a run that proves the DETECTOR
> works and proves nothing about health. The counter logic was correctly wired (a blind matcher does set the
> failure flag and exit non-zero, verified), but the wording conflated two claims, and the evidence it
> mattered is that it sent a reviewer hunting for a counter bug that did not exist. **The output IS the
> product**: nobody reads the counter logic at 3am, they read one line and decide whether to act, so a line
> claiming health on a run that never tested health is how a real alert gets dismissed later. Fixed by making
> the summary mode-aware, with an in-file comment recording WHY so it is not "simplified" back into one
> string. General rule: **a monitor's message must name which claim it is making.** "Passed" is not a claim.
>
> ### SHIP CHECKS AS RUNNABLE MODES, NOT AS DESCRIBED RESULTS
> Every verification handed between channels should be reproducible by the recipient. A described test can
> only be trusted; a runnable one can be verified. The permit-probe's negative control was built in as a
> re-runnable flag, which is why this gate could independently reproduce both suites (11/11 permit, 11/11
> detect) rather than accept a report of them. Evidence, not testimony.
>
> ### READING CONVENTION: every on-chain STATE value here is a SNAPSHOT, not a standing claim
> Verification facts are durable: source verified, bytecode matched, audit result, the composition of a
> policy chain at attach. **Mutable STATE readings are true only at the date on their entry** - balances,
> `totalSupply`, nonces, `lockNonce`, `isAttested`, `isAgent`, allowlist membership, deployer key nonces.
> They drift through normal healthy use, and a later reader must NOT treat them as current. Where a state
> value is load-bearing for a decision, RE-READ IT at decision time rather than citing this document.
> This convention exists because two entries drifted within a day of publication (the Canton `lockNonce`
> and `isAttested` rows, from a single legitimate outbound lock). The drift was harmless; publishing a
> state claim with no snapshot marker is what makes it silently become a false claim.

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
| TestTLPTSale (testnet native->TLPT ramp) | `0x239c188df0525B8410F253f71c5De6C69325d7F6` | [yes](https://sepolia.etherscan.io/address/0x239c188df0525b8410f253f71c5de6c69325d7f6#code) | [S8 adversarial source audit PASS, no changes (2026-08-30)](https://github.com/kofif1/OnchainBridges-RWA-testnet/pull/346#issuecomment-5470876893) | prices read LIVE from the v2.1 paymaster (no-arbitrage by construction); treasury-funded, no mint authority; boundary-probe buy delivered exactly 1 TLPT at $0.05; testnet-only |
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
| CantonBridgeOutbox (fresh, for gated vault) | `0x0d21fb470c8577Df126db7470c9F843cA378B360` | [yes](https://sepolia.etherscan.io/address/0x0d21fb470c8577Df126db7470c9F843cA378B360#code) | [same report](audits/CANTON-VAULT-AUDIT-2026-07-19.md) | DON-attestation outbox for the gated vault 0xFE4a (PR #259 source, identical to the prior outbox). Deployed by 0xFc99 tx `0xaa5e3e16..` block 11310194; getForwarderAddress()=0xF8344CFd, owner()=0xFc99, isAttested(1..7)=FALSE (clean nonce space) **at deploy, 2026-07-19; SNAPSHOT: isAttested(1) is TRUE as of 2026-07-22 after one legitimate outbound lock, lockNonce now 1. Expected use, not drift in the finding**. REPLACES reused outbox `0x89f38a88` for the new lane: the outbox keys attestations on raw lockNonce + reverts AlreadyAttested, and 0x89f38a88 already had nonces 1-7 attested from the OLD vault's locks, so the fresh-nonce gated vault (starts at 0) needed a clean outbox or its first-lock attestation would revert -> no mint. Nonce-keyed outbox = single-vault. |

> **Config bug surfaced:** `webapp/src/config/chains.ts` lists Sepolia TLPT as `0x8E531A05...`,
> which has NO code on Sepolia. The live TLPT token is `0x1ceaaE761C87278acfbE33c89eC523B46FAbCcF3`
> (per `aa.ts`), verified above. Flagged to the dApp owner to reconcile `chains.ts`.

> **Audit applies to every EVM chain below.** It is the same Solidity source compiled to the same
> bytecode on each chain, so the [3-analyzer source review](audits/SECURITY-REVIEW-2026-06-17.md)
> (0 Critical/High/Medium) covers all of them. The per-chain rows record on-chain *verification*.


**TestTLPTSale (native->TLPT ramp) added 2026-08-30:** `0x10313A481c7b41aa251D58Ec347Dcf4Ac96357ff` verified (Etherscan V2: ContractName=TestTLPTSale); S8 adversarial source audit PASS, no changes ([PR #346 comment](https://github.com/kofif1/OnchainBridges-RWA-testnet/pull/346#issuecomment-5470876893)); probe buy delivered exactly 1 TLPT at 0.25 S parity.

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


**TestTLPTSale (native->TLPT ramp) added 2026-08-30:** `0xabfD1d08dBA9649AAb3599Db24A6d6D4247113F4` verified - AUTO-verified via Blockscout's shared bytecode DB (from the Minato verification), CONFIRMED by direct getsourcecode read on Plume's own Blockscout (name=TestTLPTSale); a duplicate manual submission returned "Fail - Unable to verify" against the already-verified contract (instrument quirk, recorded); S8 adversarial source audit PASS, no changes ([PR #346 comment](https://github.com/kofif1/OnchainBridges-RWA-testnet/pull/346#issuecomment-5470876893)); probe buy delivered exactly 1 TLPT at 5 PLUME.

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


**TestTLPTSale (native->TLPT ramp) added 2026-08-30:** `0x27C931499bDcdF0C81F5a853358BC99216Bf75C9` verified (Etherscan V2: ContractName=TestTLPTSale); S8 adversarial source audit PASS, no changes ([PR #346 comment](https://github.com/kofif1/OnchainBridges-RWA-testnet/pull/346#issuecomment-5470876893)); probe buy delivered exactly 1 TLPT at 0.25 POL parity. TWIN: same address is Sepolia's TGLP Reserve Feed - chain-qualify.

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

> **TWIN TRAP - CHAIN-QUALIFY `0xf48f2683...554a797` (do NOT cite this entry as the Amoy vault's audit).**
> This address is a cross-chain twin (same deployer+nonce). On PLUME (98867) it is the TLPTFaucet above
> (codesize 2505, codehash `0x37d754c2db68a41b102a7e1064a9f1b3b6601563102eb3e4402748b4dd104cea`, `tlpt()`
> getter, no LendingVault interface) - THAT is what this "COMPLETE" verifies. On AMOY (80002) the SAME
> address is a DIFFERENT contract: the "Atlas Capital" LendingVault VARIANT the OB agent borrows from
> (codesize 4534, codehash `0x91d836cc874a7e0ccfd0ab78183f3cfa3e74d66535b12bd671e49908f8f2c1d5`,
> `borrow(uint256)`/`ltvBps()`; webapp/src/config/defi.ts + agent-config.json). Verified live per-chain
> 2026-09-08 (both owner 0xFc99). The Amoy LendingVault is NOT byte-identical to the audited OB
> LendingVault@default (metadata ipfs + 211 masked-body bytes differ) - its source/audit provenance is
> UNESTABLISHED (Rail 2B's tree) and it is NOT covered by this Plume entry. The AGENTS-13 borrow reverted
> against this Amoy contract (VaultCallFailed, empty returndata; see the borrow-verify notes). Chain-qualify
> before citing. (Config drift also noted: litepaper current-testnet-deployment.md lists Atlas-Amoy =
> `0xB4908aCc...` = an OLDER re-deployed vault; the LIVE agent vault is `0xf48f2683...`.)

## Soneium Minato (1946) - Blockscout - COMPLETE

Explorer: `https://soneium-minato.blockscout.com/address/<addr>`. TLPT-ecosystem contracts verified:
OperatorAllowlist `0x5fe9d900945b60b3becede4382bed2653ec99d40`, TLPTFaucet `0x8c57abe7cf1330346eb760edaee380a66d3f42f7`,
TLPTSale `0xe5e1e4b594c343c1e9bac0730b25fd4ab21f7389`, TLPTStaking `0xf3186574c8a0c0b3e235704bad389b78604b2745`,
TestUSDC `0x71a8e443f223cfe59498968758db49f2d114dce2`. (Minato's MBT/bridge is currently hidden in the UI.)


**TestTLPTSale (native->TLPT ramp) added 2026-08-30:** `0xc22B8aAc6D00E86D4811af41786B6Bb97242E525` verified (Blockscout: fully_verified=true); S8 adversarial source audit PASS, no changes ([PR #346 comment](https://github.com/kofif1/OnchainBridges-RWA-testnet/pull/346#issuecomment-5470876893)); probe buy delivered exactly 1 TLPT at 0.000025 ETH. NOTE: distinct from the DORMANT USDC-rail `TLPTSale 0xe5e1e4b5...` also on Minato - two contracts by design.

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

### Base Sepolia (84532) - CCID credential gate, ATTACHED + ENFORCING - Etherscan V2 - COMPLETE (2026-07-22)

Ilan directed closing the Base Sepolia credential gap (it had CCIP but no CCID validator). CCID deployed
the full stack + KycAllowlister, authorized the issuer, and backfilled credentials; the Verify & Ledger
gate ran verify/audit/enforcement/ledger, and the attach was fired only on Ilan's direct in-chat
authorization. Deployer/issuer `0xFc9933C8...` (correct for this TESTNET chain).

> **TWIN TRAP (chain-qualify everything).** Three of these addresses are cross-chain twins of LIVE Sepolia
> contracts; each was resolved on 84532's OWN chain via typeAndVersion, never inferred from the twin:
> `0x044951AB` = IdentityRegistry IMPL here / "Miami Beach Tower" MBT on Sepolia;
> `0x53d88a3F` = IdentityRegistry PROXY here / BurnMintTokenPool on Sepolia;
> `0xf8888292` = IssuerGate PROXY here / a PoR feed on Sepolia. Same address, different contract per chain.

**THE HALT THIS GATE EXISTS FOR (recorded because it worked).** CCID's pre-attach ask was a full allowlist
ENUMERATION, because their bounded public-RPC `getLogs` scan hit the range cap and returned false-empty
(a direct read of {0xFc99, pool} is not an enumeration). The independent full-range scan on a keyed RPC
found a THIRD allowlisted address, `0xCf309940276b1e1396AED22aC4e1E7D2E3dd27d6` (block 44312699), that was
`addressAllowed = TRUE` but `validate() = FALSE` - allowlisted-but-UNCREDENTIALED. The credentialed set was
NOT a superset of the allowlisted set, so attaching would have stranded it (admitted by the allowlist, yet
reverting on the credential leg the moment it was used). **ATTACH HALTED.** CCID chose to credential it (it
is the recurring Didit KYC test wallet, deliberately allowlisted, not an error). After the backfill
(registerIdentity `0x4d02c145...`, registerCredential `0x70bfea9f...`, both status 1, to the
Identity/Credential registries NOT the AllowPolicy), re-verify confirmed `validate(0xCf309940) = TRUE`, the
allowlist net UNCHANGED at exactly {0xFc99, pool, 0xCf309940} (credential added, not an allowlist entry), and
the superset restored. A bounded-scan attach would have shipped the latent break.

Enumeration completeness (same standard as the mainnet section): full-range Etherscan scan; exactly ONE
`Upgraded` at the AllowPolicy creation block (impl unchanged, no silent write path); ownership `0x0 -> 0xFc99`
at creation then `0xFc99 -> KycAllowlister 0x854870d8` at block 45455532, with all allow events predating the
transfer, so all were 0xFc99's direct calls.

**ATTACH + ENFORCEMENT, verified 2026-07-22 (attach txs `0xf0a0ea9c` / `0xe8c5806a` / `0x1a8a597f`, all
status 1):** `getPolicies(MBT 0x2aedDb48, transfer|transferFrom|mint) == [AllowPolicy 0x2b7B6f83,
PausePolicy 0x55d0Ed9c, Validator 0x6dea0c23]`, validator at index 2 on all three. PERMIT-proof: a real
transfer credentialed->credentialed does NOT revert. Credential-leg ISOLATION via `eth_call` state override
(positive-control-gated on the endpoint honouring overrides): no override rejects at AllowPolicy, an
allowlisted-but-uncredentialed synthetic rejects at the VALIDATOR - the rejection MOVES to the validator when
the allowlist stops binding. Mandatory here, not optional: because the credentialed and allowlisted sets are
EQUAL, a reject-only test cannot distinguish a correctly-gated chain from a bricked one.

**Contracts - identity chain-qualified, audit cite-prior by PROVEN codehash-identity, all 7 explorer-verified
on Basescan (independently re-confirmed via `getsourcecode`).** Each of the 5 ACE impls is codehash-IDENTICAL
to its already-audited Sepolia CCID counterpart (so the prior ACE/CCID review carries byte-for-byte, not by
assertion); KycAllowlister is OB's MIT `src/kyc/KycAllowlister.sol`, unchanged since the #136 review. Built
from the CCID worktree at `1250671` (branch `ccid-base-testbed-deploy`), profile MEASURED per contract =
`FOUNDRY_PROFILE=ccid` / via_ir / solc v0.8.24+commit.e11b9ed9 / runs 200 (default profile did NOT match).

| Contract | Address | Impl | Verified |
|---|---|---|---|
| RegistryAdminEngine (proxy) | `0x90250A98538159fA7A2306FAeA9D84e9deC24C77` | `0xb689499ef68846ce3d6FD8BB5C92F9EC88F45073` | [yes](https://sepolia.basescan.org/address/0x90250A98538159fA7A2306FAeA9D84e9deC24C77#code) |
| PolicyEngine (impl) | - | `0xb689499ef68846ce3d6FD8BB5C92F9EC88F45073` | [yes](https://sepolia.basescan.org/address/0xb689499ef68846ce3d6FD8BB5C92F9EC88F45073#code) |
| IssuerGatePolicy (proxy) | `0xf88882929AA3ABcDf6A9B976AA33B4158C918c1C` | `0xB917335db8ff4e5ab36E4E0f3b643e54134f86Cc` | [yes](https://sepolia.basescan.org/address/0xf88882929AA3ABcDf6A9B976AA33B4158C918c1C#code) |
| OnlyAuthorizedSenderPolicy (impl) | - | `0xB917335db8ff4e5ab36E4E0f3b643e54134f86Cc` | [yes](https://sepolia.basescan.org/address/0xB917335db8ff4e5ab36E4E0f3b643e54134f86Cc#code) |
| IdentityRegistry (proxy) | `0x53d88a3Fe9480767bE210CbC2e2b3E14145D087d` | `0x044951AB857C7e23b5570D4ca11466ACACF861A8` | [yes](https://sepolia.basescan.org/address/0x53d88a3Fe9480767bE210CbC2e2b3E14145D087d#code) |
| IdentityRegistry (impl) | - | `0x044951AB857C7e23b5570D4ca11466ACACF861A8` | [yes](https://sepolia.basescan.org/address/0x044951AB857C7e23b5570D4ca11466ACACF861A8#code) |
| CredentialRegistry (proxy) | `0xbf9206B3F6B335875A242899C472C2C440FBa02F` | `0x388078c36aa58f8dD591e3A07Deef59a080C8aec` | [yes](https://sepolia.basescan.org/address/0xbf9206B3F6B335875A242899C472C2C440FBa02F#code) |
| CredentialRegistry (impl) | - | `0x388078c36aa58f8dD591e3A07Deef59a080C8aec` | [yes](https://sepolia.basescan.org/address/0x388078c36aa58f8dD591e3A07Deef59a080C8aec#code) |
| ValidatorPolicy (proxy, ENFORCING) | `0x6dea0c2323b90784b143E6011D2F003aF61BD67F` | `0x2925B83018F616d8efc44A78faE743dbd95707ef` | [yes](https://sepolia.basescan.org/address/0x6dea0c2323b90784b143E6011D2F003aF61BD67F#code) |
| ValidatorPolicy (impl) | - | `0x2925B83018F616d8efc44A78faE743dbd95707ef` | [yes](https://sepolia.basescan.org/address/0x2925B83018F616d8efc44A78faE743dbd95707ef#code) |
| KycAllowlister (owns AllowPolicy) | `0x854870d876B35b4b5da6ea39d6C3093a9F34F733` | - (direct, MIT) | [yes](https://sepolia.basescan.org/address/0x854870d876B35b4b5da6ea39d6C3093a9F34F733#code) |

**Publish governance:** the 5 ACE contracts are Chainlink BUSL-1.1 (submitted VERBATIM, no OB copyright added);
KycAllowlister is MIT. Publishing this specific Base Sepolia BUSL set was approved by Ilan directly (same
decision + zero-marginal-disclosure rationale as the Amoy validator; the source is already public and already
verified on other chains). **Caveat, recorded honestly:** the Etherscan `LicenseType` DROPDOWN field came back
EMPTY for all 7 (forge's standard-JSON path does not stamp it, and re-submission of an already-verified address
is refused). The governing marker is the SPDX identifier in the verbatim source, which is present and correct
(`BUSL-1.1` in the ACE contracts, `MIT` in KycAllowlister); only the explorer's separate label field is unset.

**State at gate time (SNAPSHOT):** MBT `0x2aedDb48` totalSupply = 1e18, held entirely by 0xFc99
(balanceOf(pool) = 0). Net allowlist = credentialed set = {0xFc99, pool, 0xCf309940}. Public-surface exposure
(flag-flip out of "soon", the "N EVM chains credential-gated" count) is GOVERNANCE-gated (CCID + Content +
Session 8), NOT this gate; the count must be re-checked against the live attached set at PUBLISH time.

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
> ### THE COMPLEMENT: every passing transfer depends on the target default being ALLOW
> The short-circuit rule above is only half the picture, and the other half is a live brick risk.
> `PolicyEngine.run` calls `_checkDefaultPolicyAllowRevert(target, payload)` **AFTER the policy loop
> completes**, not only when the chain is empty. Since NO policy in the ACE library returns `Allowed`
> except `BypassPolicy` (see the exhaustive audit above), **every production chain runs to the end of the
> loop and then consults the default.** If that default is REJECT the call reverts
> `PolicyRunRejected(address(0), "no policy allowed the action and default is reject")`.
>
> **Consequence: `setTargetDefaultPolicyAllow(target, false)` reads like "harden this target" and would in
> fact BRICK it.** Every transfer, mint and bridge on that target would revert even for fully admitted,
> fully credentialed parties, because an all-`Continue` chain has nothing to satisfy the default. Worse, a
> REJECT-ONLY verification would pass perfectly in that state: everything is rejected, including the things
> that should be. This is the concrete reason a PERMIT-PROOF is NECESSARY rather than good practice.
>
> **BLAST RADIUS: on the main Sepolia engine this is ONE GLOBAL FLAG, not a per-target one.**
> `_checkDefaultPolicyAllowRevert` uses the GLOBAL `defaultPolicyAllow` unless a target-specific override
> was set (`targetHasDefault[target]`). Measured on engine `0xd4b9F980...` by full-range event scan:
> **ZERO `TargetDefaultPolicyAllowSet` events ever**, and exactly ONE `DefaultPolicyAllowSet` (block
> 10641981, at engine setup). So NO target has a per-target override and **every ACE-gated target on that
> engine inherits the same global flag**: v1 MBT `0x044951AB`, v2 MBT `0x661d63bE`, the Canton lock vault
> `0xFE4a573C`, and every other target attached to it. **A single `setDefaultPolicyAllow(false)` call would
> brick all of them simultaneously**, while a reject-only test on any of them still passed.
> This is an ADMIN_ROLE setter callable at ANY time, so a gate check proves the configuration at that
> INSTANT only; a flip an hour later is silent and the first symptom is a user's transaction failing.
>
> **THEREFORE the permit-probe is promoted from a post-attach gate to a STANDING MONITOR** on every
> ACE-gated target (proposed by the Canton channel). It is a pure `eth_call`: no gas, no state change, one
> call, and the assertion is simply that the revert (if any) is NOT the AllowPolicy rejection. Detection
> drops from "the next user complaint" to the poll interval. Confirmed live at the time of writing: the
> global default is ALLOW, evidenced behaviourally by every permit-proof recorded in this document, each of
> which would have reverted otherwise.
>
> **Monitor design, three points that make the difference between a monitor and a green light:**
> 1. **Pre-check that the probe sender is still entitled** (`addressAllowed(sender)`), and report SKIP, not
>    ALERT, if it is not. Otherwise de-allowlisting the probe wallet raises a false "engine bricked" alarm,
>    and false alarms are how monitors get muted. It also sharpens the ALERT: the sender IS entitled and was
>    still rejected, therefore the ENGINE changed.
> 2. **Match the DENY SIGNATURE, not "did it revert".** A healthy probe usually DOES revert, just further
>    downstream (e.g. `ERC20InsufficientAllowance`, meaning it cleared the gate and stopped at approval).
>    Alerting on "reverted" would be permanently red. Match the AllowPolicy rejection specifically.
> 3. **Pure `eth_call`** so it needs no gas, no state change and no signing, and is therefore safe to run on
>    a tight schedule and safe to run from a channel that never signs.
>
> **A monitor's PASS is itself a SNAPSHOT** (per the reading convention at the top of this document), so the
> durable artifact is NOT a green run. **It is the NEGATIVE-CONTROL result proving the detector can fire**:
> a monitor that has only ever returned OK is indistinguishable from a monitor that CANNOT fail. The Canton
> channel proved theirs by firing the same probe from a non-allowlisted sender, reproducing exactly the
> rejection a bricked engine yields for an allowlisted one, and confirming the deny-matcher triggered.
> Reusable probe: `canton-ggbr-bridge/ops/ace-permit-probe.sh` (Canton channel).
>
> **Scope: the per-engine floor on Sepolia is NINE probes, not one.** Each PolicyEngine instance carries its
> OWN global flag. `0xd4b9F980...` covers v1 MBT + v2 MBT + the Canton vault, but each of the 8 multi-asset
> catalog assets has a SEPARATE engine with an independent default. A probe on one engine says nothing about
> another. Verified probe sender for all of them: `0xFc9933C8...`, which reads `addressAllowed = true` on
> both the legacy allowlist `0x7858b6e6...` and the multi-asset master `0xd2764716...`, and `validate = true`
> on the credential validator.
>
> **Auditability of the flag:** there is NO public getter for `defaultPolicyAllow` or
> `targetDefaultPolicyAllow`, so the current setting CANNOT be read directly. It IS recoverable from events
> (`DefaultPolicyAllowSet`, `TargetDefaultPolicyAllowSet`), and event enumeration is VALID here by the test
> stated elsewhere in this ledger: the two `ADMIN_ROLE` setters are the only writers of those fields and both
> emit. The cheap live check remains a permit-proof: if an admitted party can transact, the default is allow.
> Confirmed behaviourally across every chain gated in this document, since each permit-proof recorded here
> would have reverted otherwise. (Raised by the Canton channel from a live near-miss on the lock gate.)
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

## Multi-Asset Signing Session 3: 8 catalog assets (Sepolia 11155111) - Etherscan V2 - COMPLETE (2026-07-22)

Eight new catalog RWA assets: NTR (Northline Trade Receivables), SGI (Solara Grid Infrastructure),
VCC (Verdant Carbon Credits), HRT (Harmony Music Royalties), IEL (Ironclad Equipment Leasing),
CLW (Chateau Ledger Fine Wine), ABA (Atelier Blue-Chip Art), LLF (Lexfund Litigation Finance).
Sepolia HUB ONLY: no spokes, no vaults, no yield stacks, no CCIP lanes yet. Deployed by Ilan
(deployer `0xFc9933C8...`, nonce 759-909, 0.0967 ETH). Shared Sepolia master allowlist reused unchanged:
`0xd276471610692F3B5BECF433C867900E0291FD86`.

> **SCOPE CORRECTION BY THIS GATE: 56 ADDRESSES, NOT 40.** The handoff enumerated 5 contracts per asset.
> But each asset's PolicyEngine and LinkedComplianceCheck are minimal ERC1967 PROXIES (130-byte runtime
> that embeds the ERC1967 slot and delegatecalls), and **the compliance LOGIC lives in 16 implementation
> contracts that were not listed**. Passing the 40 would have cleared 8 investor-facing assets whose actual
> enforcement code was never verified. A proxy verifies cleanly on its own and says NOTHING about the logic
> it delegates to. All 16 impls are included below and in the verification count. The 8 engine impls are
> bytecode-identical to each other, as are the 8 LCC impls.

**Verification: 56/56 explorer-verified**, each confirmed by this gate via `getsourcecode` with source
present, NOT taken from the verification tooling's own output. Compiler for ALL 56:
**`FOUNDRY_PROFILE=ccid` (via_ir TRUE), solc v0.8.24+commit.e11b9ed9, optimizer 200, evmVersion cancun.**
Every one of the 56 on-chain creation inputs begins with the locally recompiled creation bytecode.

> **PROFILE IS PER-DEPLOYMENT, DO NOT INHERIT IT.** The Signing Session 1 audit of the SAME contracts
> recorded the DEFAULT profile as matching, and that prior was carried into this batch as an expectation.
> It was WRONG here: default produced 13200 bytes against 11761 on chain for PolicyEngine, and `ccid`/viaIR
> matched exactly. Confirm the profile by creation-bytecode prefix match every time rather than inheriting
> it from a previous batch of the same source.

**On-chain identity + wiring, verified independently by this gate:** all 8 tokens canonical EIP-55, 6668
bytes, `symbol()` correct, `totalSupply()` matching the approved catalog exactly, and
`balanceOf(deployer) == totalSupply` (genesis mint landed, nothing leaked). Support contracts report the
expected `typeAndVersion` (`BurnMintTokenPool 1.5.1`, `PolicyEngine 1.0.0`, `LinkedComplianceCheck 1.0.0`;
PoR feeds are MockV3Aggregator with none, as expected).

**CROSS-WIRING: all 8 clean.** Every token points at ITS OWN PolicyEngine, every pool at ITS OWN token,
every LinkedComplianceCheck at the shared master allowlist. This is the specific hazard of eight
near-identical stacks deployed from one parameterised loop, and per-asset verification CANNOT detect a
cross-asset swap by construction, however carefully it is done. Checked assets against each other, not
each asset internally.

**BOTH COMPLIANCE LEGS, on ALL 8** (the deploying channel had spot-checked 3): a transfer to a
non-admitted address reverts with the payload naming THAT ASSET'S OWN LinkedComplianceCheck (attributed by
policy ADDRESS, not by fact-of-revert), and a transfer to the admitted deployer succeeds. Each asset is
genuinely gated AND not bricked.

**Audit: [MULTIASSET-SS1-AUDIT-2026-07-16](audits/MULTIASSET-SS1-AUDIT-2026-07-16.md) CARRIES**, verified
rather than assumed: `src/RWATokenCCT.sol` (last changed 2026-04-15) and `src/policies/LinkedComplianceCheck.sol`
(2026-07-14) have ZERO commits since the audit date, and both are SHA-256 identical between `origin/main`
and the deploying worktree. 0 Critical / 0 High / 0 Medium, one Low on LinkedComplianceCheck recorded
by-design. Vendored: PolicyEngine = Chainlink ACE under the BUSL additional-use grant; BurnMintTokenPool =
CCIP; MockV3Aggregator = Chainlink mock; ERC1967Proxy = OpenZeppelin.

| Asset | RWATokenCCT | BurnMintTokenPool | PolicyEngine proxy / impl | LinkedComplianceCheck proxy / impl | PoR feed |
|---|---|---|---|---|---|
| NTR | `0x0a27077306B11432643402Cbd71E830D9DD55df3` | `0x72069897f8e26Ae0E669294Af5FbcE0C6A3C9d81` | `0x20bC79CC04cCf8625294CdB2320486b3E9C6998a` / `0x1Eb16E34bF113959DE4d3e1651d2A276C970cE4c` | `0x4EdA223AadfB99088f6bCBfaf4408980c9ABea49` / `0x77d1E619cEdD5741b44BE82bB4439263185eEB75` | `0x2De00761eBA8d188a25D2D669C22Ed72a8430Bc3` |
| SGI | `0xA0B04fe56662B2D1E56202Bc7759964057d507B2` | `0xaB76b48143FEE071401e4a14fBcf54Fd5Ca0b74a` | `0x32A5bAcDA70Ca3e3cE5efCf1E70F19a213CB6A0F` / `0xb00C9321006Ea8371AB21Be0d5FC85E258660155` | `0x1A150563C3Ba4A5f7A258734bc3cE31Db35ECFD1` / `0x74aa7C6679Fbd46D080802B41630255A78f4F570` | `0x7Ec38A4721B1EEDC8660AC01007B3Faad2f9f09f` |
| VCC | `0x110d738424Fc30Fca0475C980Aa9F6C3a0d6a894` | `0xbEf40eBbB611bad0602839A9B3957b52bfC9B77d` | `0x7CbdFaDB978865647286c73AD173328f05db8f33` / `0xf158E6127Bcad24d107F68202430283200c94858` | `0x51C94e5b8c7A7551024b22a6A58c4C1a1324BB93` / `0x59a6EA78805f1bb7C18339AD52A5995751e26a8A` | `0xa49aF57ed305959Ae9B37677DF7ca38F718b2F46` |
| HRT | `0x7Cf249F2c5B4C7f7Dae0B707B06dc5B8425D632e` | `0x18dc0F131bE562bdC301e86bD76B0BD2852E1c47` | `0x029765ae7D17137D6Ce16D3f1f2F16bD408CfE61` / `0x49E56eF7d2b5EE420c4725Bc727A9D53b6aD5fa9` | `0x902427c48f991EE55E580123bDaabe09DA98eE92` / `0x4F2c4c24A910A536D231fEfCE2A74E506C53375b` | `0xABaBCb01D932CAB229E2DCd00a7C28D7846ad777` |
| IEL | `0xA7cF769df5512c95fd75643947A5927beb8152E5` | `0x8f2f029e8da7c0A5a4b143f956316Ab86FA352F4` | `0x984e12820AeFE4c9C2E8d6A2a3c5b4E8A4D4b551` / `0xAE89C2c3f4C5eaE0872607864E51BDF7d44FA206` | `0x7E9a94fdf55b06D3C52e9de55F9C0Fc0467cb701` / `0x38D660837416314d7e43fE3351b5242a2dFD5496` | `0x7E282c120dd446C985859C1881e04C6E0a8B3E65` |
| CLW | `0xD5a7aA7394bDAd77693F2D1e836362Af7E3C3C5e` | `0xCA8b93a34F3e572CeB132A39a4b09715505dF3b8` | `0x9719Cd08EaEFA2FC8b4c9115e92D982d997cf568` / `0xb38D6E0Ad5b714238268B7d9fA98e4D949F75D7A` | `0xD4c9381e3822F87B6F52cc54c54cA53f388Dd178` / `0x8B9480Ed42050C2c363Cc0AAFc6D295F9329d29E` | `0x52FCb785167B9b2377138268194658771D81ebe2` |
| ABA | `0x015bc7862C8BeC7C7C8581Ac05C0b5D3dB9dCA9C` | `0xb4821e16e12bA184397424F12Efbfe81439b8b92` | `0xf60323A97c4014e35dd860458e1b78620C95313a` / `0xb911D19b9364968E1837B3020E4361910178b35a` | `0xe66b4C0A854e66bda770073c50968011Ffc3dA08` / `0x3950363A500Fb75f3C4C197EF14044F7402Baa41` | `0x46e84c1C7e9baf93a798D93b7DB1668A427F8B9f` |
| LLF | `0xcad34CC603C7643CDdeA19C742923e72876e7420` | `0x0eC3B9d03137C7a0A1745a768d019D28C69a19D3` | `0xF052fBBEd8b70b289007d5BA3C804cE8f9eCCfd2` / `0x28BF93791aE05B4F1C4F9fafc4F2ebc7C124563F` | `0x7452dAbF93B22563776757e40Bdf6C6f7DfA2Fc5` / `0x12878A6457acb79479b0893B417828E841FcDeD6` | `0x222A9532D7b1f686Ea5c693bF733D93a333eC036` |

> **Honest caveat on 36 of the 56:** Etherscan auto-verifies identical bytecode, so 3 submissions cascaded
> to 21 more (and 32 were already verified beforehand). Those cascade-verified entries return an EMPTY
> `ConstructorArguments` field from the API while carrying full source, full ABI, correct compiler settings,
> and correct `Proxy`/`Implementation` flags. Etherscan refuses re-submission ("already verified"), so this
> is not repairable. "Verified" and "verified with decoded args displayed" are not the same claim, and the
> difference is recorded rather than smoothed over.

**PoR state at gate time (SNAPSHOT):** all 8 feeds are on the refresh keeper and reading minutes old, after
Ilan funded the keeper key 0.4 ETH (tx `0x6aa32045456297b86f947c20996b1b49f4c763c261fe30e76cae0f47fb721f5c`,
status 1) and Session 8 added them (3 to 11 feeds, runway 88 days). Before that they were on an unrefreshed
24h clock. **Reserve headroom is ZERO on every asset** (e.g. NTR supply 5,000,000 vs reserves 5,000,000), so
a mint of even one token reverts `InsufficientReserves` regardless of feed freshness. Combined with the pools
having no lanes (`getSupportedChains()` = `[]`), no mint is reachable today by any caller, INCLUDING the
owner (whose implicit `onlyMintBurn` bypass means "mint authority revoked" does NOT mean "cannot mint").
**This binds at Session 4:** wiring a lane makes the pool a reachable minter, and an inbound delivery then
needs BOTH a fresh feed AND reserves above supply.

## OB Agent Network - AGENTS-12 Phase 1 (Sepolia 11155111 + Polygon Amoy 80002) - Etherscan V2 - COMPLETE (2026-08-31)

Open-repo (MIT) Phase-1 agent-delegation contracts from `ob-agent-network` (source commit `0c8db28`). CCID
deployed + ran its verify/audit leg; the Verify & Ledger independent leg (this entry) read both chains
directly. Deployer `0xd63352Cc9f96362D3D080B7c519e7f64f97e446a`.

**1. RevocationRegistry (deployed contract) - twin, same address both chains.**
`0x1EFa0Af5d7a814725ff2b8F47b0502634Ac4c26E` (deployer nonce 0, so IDENTICAL CREATE address on both chains).
No `typeAndVersion`, so the twin discriminator is the deployed bytecode: `keccak256(deployedBytecode) =
0xa92296da4b58cd523c1587ab8b275524778b57950454c4713b5a28e76c14c924`, read IDENTICAL on Sepolia AND Amoy
(same address + same bytecode-hash = genuine twin, not a same-address collision). Explorer-verified as
`RevocationRegistry` on both chains (independent `getsourcecode`), SPDX `MIT`, solc v0.8.24.

| Contract | Address | Sepolia | Amoy |
|---|---|---|---|
| RevocationRegistry | `0x1EFa0Af5d7a814725ff2b8F47b0502634Ac4c26E` | [yes](https://sepolia.etherscan.io/address/0x1EFa0Af5d7a814725ff2b8F47b0502634Ac4c26E#code) | [yes](https://amoy.polygonscan.com/address/0x1EFa0Af5d7a814725ff2b8F47b0502634Ac4c26E#code) |

**2. DelegationAdmission (INTERNAL LIBRARY - no standalone address).** All functions are `internal` (zero
public/external), so it is INLINED into its callers, NOT separately deployed. It is verified via its
CONTAINER: the source-verified `OBAgentWalletV2` ("Test Wallet 3"), whose verified source contains the
library at commit `0c8db28`.

| Container (OBAgentWalletV2) | Address | Verified |
|---|---|---|
| Sepolia | `0xdD5756d3713D704594f0b932cDB9bB0eF63758B4` | [yes](https://sepolia.etherscan.io/address/0xdD5756d3713D704594f0b932cDB9bB0eF63758B4#code) |
| Amoy | `0xF6af3A53c38031B7469D5e6a1Dc2eCF31D9F8832` | [yes](https://amoy.polygonscan.com/address/0xF6af3A53c38031B7469D5e6a1Dc2eCF31D9F8832#code) |

Container verified independently: explorer-verified as `OBAgentWalletV2` on both chains (identical 194703-char
source containing `DelegationAdmission` + the `isRevoked` cross-call), 4932B runtime with identical runtime-head
across chains (same code, per-chain CREATE2 salt gives different addresses).

**On-chain PERMIT + REJECT pair (the library's admission legs actually gate the DEPLOYED wallet, not just the
mocked unit tests).** The four legs (None / MissingIdentity / MissingCredential / Expired / Revoked) are
unit-tested in `DelegationAdmission.t.sol`, but unit tests MOCK the registries; the real proof is the deployed
wallet wired to the REAL RevocationRegistry:
- PERMIT: `execute()` ping, Sepolia `0xab5fbec9b75ac9da621a39d041fe73eb2b2f6d32bc629c6d01261eb9e2c3611c`
  status 1 (also Amoy `0xebd159c8a088322543c8ff351f6272546fd1f99037d70e4bed4d5e5c7280090f` status 1).
- REJECT (revocation leg), Sepolia, a REAL revoke -> failing-ping -> unrevoke bracket:
  `revoke(1)` `0x144004001b5fdb7d62e63badb0e3874a5fe888d306032b62a30f2b726c216032` (status 1) ->
  `execute()` ping at nonce 1 while revoked `0xdcd90448ac63ba074a90605f6307a06f1c65f4f1274ded5ad9d2388f11c344cd`
  **status 0 (REVERTED)** at block 11616159 -> `unrevoke(1)`
  `0xe832cf40cdd0ad591f3d25b6fe8d299dccc36538d38375f74a034adb23490eeb` (status 1).
  This gate RE-SIMULATED the failing op at its historical block: revert data
  `0x07b9fad00000...0004` = `DelegationRejected(uint8)` with arg **4 = Reason.Revoked** (enum order
  None=0/MissingIdentity=1/MissingCredential=2/Expired=3/Revoked=4, confirmed from source). The op was
  otherwise-valid (fresh nonce, valid signature), so it passed the sig+nonce checks and reverted SPECIFICALLY
  at the revocation leg - not sig, nonce, or another leg. The on-chain custom error is `DelegationRejected(Reason)`,
  NOT the `agent_delegation_revoked` string (that string is the standalone DelegationValidatorPolicy's reason,
  not in this wallet-internal path). Registry left CLEAN: post-`unrevoke`, `isRevoked(0xd63352Cc..., 1) == false`
  (SNAPSHOT read; the revoke was transient).

**Audit:** MIT, source commit `0c8db28`. CCID's verify/audit leg = PASS (correctness + spec-match); this gate
independently reproduced the RevocationRegistry bytecode-twin identity, the container source-verification, and
the full PERMIT+REJECT pair including decoding the revocation-leg revert. CCID's full record:
`SESSION-COORDINATION/CCID-AGENTS12-PHASE1-AUDIT.md`.

## Multi-Asset hub LendingVaults - 10 catalog assets (Sepolia 11155111) - Etherscan V2 - GATE PASS (2026-09-06)

Ten hub-sited LendingVaults (one per catalog asset), deployed by Ilan 2026-09-06 (staged by the Multi-Asset
Marketplace channel), verify/audit/ledger gate by Verify & Ledger. DISTINCT from + additive to the existing
SS2 Amoy AVR/TGLP vaults (those stay). Hub-sited where the collateral supply lives; no lane/spoke/mint - a
borrow deposits hub collateral into a hub vault. Deployer/owner `0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c`.
Shared Sepolia USDC (deployer-owned) `0x8bd2a0a9f90dc40a3a420c84553d40dd688035df`. Provider names display-only.

| Sym | Vault | Collateral | LTV | Rate(bps) | Price(6) | Provider |
|---|---|---|---|---|---|---|
| AVR | [`0xf9781594461C19884aF3D2e4ceEBCefa9d456086`](https://sepolia.etherscan.io/address/0xf9781594461C19884aF3D2e4ceEBCefa9d456086#code) | `0x8CaDD1Dd8841FC8dC88f7f27dd0a12F2ABb5Ac2D` | 6500 | 500 | 250000000 | Atlas Capital |
| TGLP | [`0x3a41261438f8a0ABB3A775Cf6E515c8FEaBFeD57`](https://sepolia.etherscan.io/address/0x3a41261438f8a0ABB3A775Cf6E515c8FEaBFeD57#code) | `0xa2951ee0A94aA6a37646B3112561f1b4eCd22e36` | 7500 | 420 | 25000000 | Atlas Capital |
| NTR | [`0x14044A1B4eFFcEB5eac5d9bEef0338bbC96dDB39`](https://sepolia.etherscan.io/address/0x14044A1B4eFFcEB5eac5d9bEef0338bbC96dDB39#code) | `0x0a27077306B11432643402Cbd71E830D9DD55df3` | 7000 | 600 | 1000000 | Meridian Lending |
| SGI | [`0x35756Be4390D8cf9938470e19880238a64a95031`](https://sepolia.etherscan.io/address/0x35756Be4390D8cf9938470e19880238a64a95031#code) | `0xA0B04fe56662B2D1E56202Bc7759964057d507B2` | 7000 | 550 | 50000000 | Meridian Lending |
| VCC | [`0xd5B3b6BBd12DF324724C0353d34B92b70011494e`](https://sepolia.etherscan.io/address/0xd5B3b6BBd12DF324724C0353d34B92b70011494e#code) | `0x110d738424Fc30Fca0475C980Aa9F6C3a0d6a894` | 5500 | 650 | 15000000 | Nova Finance |
| HRT | [`0x9533bb01123675C300befe2DcC6c004EDFb2Cac8`](https://sepolia.etherscan.io/address/0x9533bb01123675C300befe2DcC6c004EDFb2Cac8#code) | `0x7Cf249F2c5B4C7f7Dae0B707B06dc5B8425D632e` | 6000 | 600 | 8000000 | Nova Finance |
| IEL | [`0x21D41012AD026d29C92a3513a1EF2AFA88B25787`](https://sepolia.etherscan.io/address/0x21D41012AD026d29C92a3513a1EF2AFA88B25787#code) | `0xA7cF769df5512c95fd75643947A5927beb8152E5` | 6500 | 550 | 40000000 | Plumage Finance |
| CLW | [`0x566AdFa78FAF687324aD33e94eB40E5389c3A406`](https://sepolia.etherscan.io/address/0x566AdFa78FAF687324aD33e94eB40E5389c3A406#code) | `0xD5a7aA7394bDAd77693F2D1e836362Af7E3C3C5e` | 5000 | 700 | 120000000 | Plumage Finance |
| ABA | [`0x839e7F5d7669357BDd562F163602432C097f25D6`](https://sepolia.etherscan.io/address/0x839e7F5d7669357BDd562F163602432C097f25D6#code) | `0x015bc7862C8BeC7C7C8581Ac05C0b5D3dB9dCA9C` | 5000 | 700 | 500000000 | Atlas Capital |
| LLF | [`0x53DE2FDf5C08Cf898708268c4467F844C670B31C`](https://sepolia.etherscan.io/address/0x53DE2FDf5C08Cf898708268c4467F844C670B31C#code) | `0xcad34CC603C7643CDdeA19C742923e72876e7420` | 4500 | 800 | 75000000 | Meridian Lending |

**On-chain verify (my own reads off the chain, NOT the deploy log):** all 10 - codesize 4534; `collateralToken()`
== the expected hub collateral (this is ALSO how I confirmed the vault->symbol mapping - independent of the
nonce-deterministic run log's ordering); `ltvBps()`/`annualRateBps()`/`tokenPriceUSD()` == the approved terms
exactly; `usdc()` == the shared USDC; `owner()` == 0xFc99; USDC `balanceOf(vault)` == 100,000e6 (owner-mint at
deploy). All PASS.

**Explorer-verify (independently re-confirmed via `getsourcecode`, NOT taken from forge stdout):** all 10 =
ContractName `LendingVault`, compiler `v0.8.24+commit.e11b9ed9`, optimizer ON / 200 runs, **viaIR FALSE**.

**Audit:** source `src/defi/LendingVault.sol` blob `ac97b8cd171d9ab0fc9d0094d1e22a2b51b33bbc`, MIT, confirmed
byte-identical to `origin/main` - the already-audited source (3-analyzer run, 0 Crit/High/Med; the interest-
bearing-loan code landed 2026-05-01, BEFORE the audit + the SS2 deploy, and the file is untouched since, so the
audit covers it). **Deployed bytecode PROVEN against the audited source for ALL 10:** each vault's deployed
runtime, with the two immutables (`collateralToken`, `usdc`) masked, is BYTE-IDENTICAL to the locally-compiled
source. **Compile profile determined EMPIRICALLY, not inherited:** default (optimizer 200 / viaIR FALSE) yields
4534 bytes = deployed; the ccid/viaIR profile yields 3958 bytes (would NOT match). SS3 used viaIR - this batch
does NOT; checked the bytecode rather than assuming. Same LendingVault source as SS2 Amoy AVR/TGLP.

**Gate: PASS (all four legs).** Released the Multi-Asset defi.ts config PR. Provenance run log:
`OB-multi-asset/broadcast/hubvaults-run-2357.log`.

## OB Agent Network - AGENTS-13 autonomous-borrow credential (Polygon Amoy 80002) - CREDENTIAL ISSUANCE - GATE PASS (2026-09-05)

Credential issuance (NOT a contract deploy): two delegation credentials for agentId 1 written to the
`CredentialRegistry 1.0.0` at `0x94d6257c2f696019B0Bbf22b28b853689076D898`, adding the `borrow` action so the
OB agent can take a testnet loan through the OB framework broker. Types library `DelegationTypesV2.sol`
(`ob-agent-network`, SPDX MIT) is stateless (no deploy). Design: CCID / Rail 2B. Ilan signed all three
transactions himself (`0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c` = 0xFc99).

**ENFORCEMENT BOUNDARY (Legal Review matter 9, condition a - binds this entry and every doc/demo/mention):**
the `maxBorrowUsdc` / `borrowWindowAmount` / `borrowWindowPeriod` caps are **policy-enforced by a trusted OB
framework broker, on testnet** - they are **NOT enforced on-chain.** The borrow AgentOp carries native
`value = 0`, so `OBAgentWalletV2`'s on-chain per-op check passes at any size; the v2 caps are read only by the
off-chain broker. **A consumer that bypasses the broker and signs directly against the wallet CAN exceed these
caps on-chain.** On-chain amount enforcement is Phase 2C (v3 wallet). This entry never states or implies the
caps are "on-chain enforced" or that the agent "cannot exceed" them. Source NatSpec carries this verbatim
(`DelegationTypesV2.sol`, the v2-additions block). This issuance is the INTERNAL testbed (OB's own agent), NOT
outside-agent enforcement.

### Transactions (all independently read from Amoy chainid 80002 by this gate; `from == 0xFc99`, `status 1`)
| # | Tx | Block | Call (via AccessManager selector) | Registry event |
|---|---|---|---|---|
| A | [`0xfce03592...851e26`](https://amoy.polygonscan.com/tx/0xfce03592681eecf571cf0c58c98a43138b445e686538261b21e9fcb8d5851e26) | 46841687 | `registerCredential` (`0x8ab73618`) | CredentialRegistered ccid `0xc27694aa...`, typeId v2 `0xa4b0fb96...` |
| B | [`0xdafdc551...caad61`](https://amoy.polygonscan.com/tx/0xdafdc55196ef3b39e3ac4b57833e1fe559b1db20954d066156c825251acaad61) | 46841752 | `removeCredential` (`0x93893fcb`) | CredentialRemoved (`0x9094e025`, exact) v1 `0x57fe1f0a...` |
| C | [`0xfa5c2062...e9fd58`](https://amoy.polygonscan.com/tx/0xfa5c2062310a60047fa85ce444616ad05397ad1add65b6684d22164e7ce9fd58) | 46841797 | `registerCredential` (`0x8ab73618`) | CredentialRegistered v1 `0x57fe1f0a...` |

Block order A < B < C confirms v2 was issued BEFORE the v1 remove->re-add window, so there was never a moment
with neither credential present. The v1-absent transient window (B->C, ~45 blocks / ~90s) has v2 present
throughout; the wallet gates on v1, so any v1 op in that window fails CLOSED (safe direction). Testnet, no
traffic. Stated, not hidden as "no gap."

### Live end-state (this gate's own reads on the registry)
- `getCredentialTypes(ccid)` = `[v2 0xa4b0fb96, v1 0x57fe1f0a]` - BOTH present. `validate(ccid,v1)`=true,
  `validate(ccid,v2)`=true. `expiresAt` = 0 (never) on both (SNAPSHOT at block ~46841797+).
- **v1 (typeId `0x57fe1f0a...`, 6-field DelegationDataV1):** agentId=1, allowedActions=`[ping
  0xcd541a5f...879c7, borrow 0x4f943907...6170e]`, perActionCap=1e16, spendWindowAmount=5e16,
  spendWindowPeriod=3600, revocationRegistry=`0x1EFa0Af5d7a814725ff2b8F47b0502634Ac4c26E`.
- **v2 (typeId `0xa4b0fb96...`, 9-field DelegationDataV2):** same first six + maxBorrowUsdc=50000000 (50e6),
  borrowWindowAmount=200000000 (200e6), borrowWindowPeriod=3600. (Caps are DECLARED framework caps - see the
  enforcement-boundary block above; never on-chain bounds.)
- **CONSISTENCY INVARIANT (the load-bearing cross-credential check): allowedActions BYTE-IDENTICAL v1==v2** =
  `[ping, borrow]`, same order. No split-brain (wallet reads v1, framework reads v2). PASS.
- **typeId integrity:** both recomputed independently - v1 `keccak256(abi.encodePacked("ob.agent_delegation.v1",
  uint256(1)))` = `0x57fe1f0a...`, v2 (`...v2`,1) = `0xa4b0fb96...`; the agentId echoed inside both structs = 1
  (defense-in-depth). Action hashes recomputed: keccak256("ping")=`0xcd541a5f...879c7`,
  keccak256("borrow")=`0x4f943907...6170e` - match the on-chain values.
- **field-count exactness = versioning safety:** v2 decodes as exactly 9 fields (allowedActions offset 0x120);
  v1 as 6 (offset 0xc0). Both via the single-dynamic-tuple 0x20 wrapper (Phase-1 encoding gotcha), decoded
  byte-against the on-chain read.
- **revReg pointer identity:** `0x1EFa0Af5d7a814725ff2b8F47b0502634Ac4c26E` - EIP-55 canonical, codesize 895,
  `isRevoked(address,uint256)` responds - the REAL Phase-1 RevocationRegistry (source-verified on Amoy in the
  AGENTS-12 entry above), NOT the fixture placeholder.
- **no PII on-chain:** only agentId, two keccak action hashes, numeric caps, and the revReg pointer.

### Governance conditions (all satisfied at clear)
- **Legal matter 9 ON RECORD:** lite check, PROCEED (testnet), three binding conditions (a language / b
  Model-A-internal-only / c re-check triggers). Re-read on the artifact (`CCID-BORROW-AUTONOMY-GATE-NOTE.md`),
  not a relay.
- **Ilan-DIRECT authorization for the on-chain act:** SATISFIED by `from == 0xFc99` on all three txs (Ilan's own
  hardware signature) + this gate's decode-against-package (`policy_signed_write_satisfies_direct_auth`). The
  **off-chain PUSH of this ledger entry still requires Ilan's separate direct greenlight.**
- **(c) re-check triggers:** NONE fired (testnet / valueless / OB's own agent / no public marketing / no
  wallet-side-enforcement claim). Any trigger => full Legal check before ship.
- **Provenance:** Option-B (CCID/Rail 2B design), MIT, v1 schema/typeId byte-untouched (only the credential's
  allowedActions data changed). Pre-flight + at-issuance criteria: `VL-AGENTS13-BORROW-CREDENTIAL-GATE-CRITERIA.md`.

**Phase 2C (on-chain cap enforcement for outside agents):** dormant, awaiting Ilan's greenlight. Enforcement
must live at a mandatory chokepoint every borrow crosses - the wallet `execute()` OR a limit policy on an
OB-gateable borrow target (the `LimitsAggregatorPolicy` shape). A fully external agent (own wallet, non-gateable
vault) is reachable only by off-chain trust. This gate blocks any record calling outside-agent caps "enforced"
until Phase 2C is live and gated.

## OB Agent Network - AGENTS-13 DELTA-2: approve reissue + wallet-KYC (Polygon Amoy 80002) - GATE PASS (2026-09-08)

Follow-on to the AGENTS-13 borrow credential above. Ceremony of 6 txs, all Ilan-signed (`0xFc99`), status 1,
independently re-checked by this gate on Amoy (my own reads, not the deploying channel's sweep). Part 1 adds the
`approve` action so the wallet can grant OBBorrowHelper its MBT allowance; Part 2 gives the agent WALLET its own
operational `common.kyc` so it passes the MBT compliance gate.

### The 6 transactions (all `from == 0xFc99`, status 1, block order ascending)
| Tx | Purpose | Block | To |
|---|---|---|---|
| [TX1](https://amoy.polygonscan.com/tx/0x4336fdee581c71c6b705809283e847f660789a86bc57bd426658d26311176443) | removeCredential v1 | 47085966 | CredReg 0x94d6257c |
| [TX2](https://amoy.polygonscan.com/tx/0x7e8dd9d66b71c5927de7c8f233f74d8324c490e4a878cf75eb66f8c9dfe41086) | registerCredential v1 [ping,borrow,approve] | 47086005 | CredReg 0x94d6257c |
| [TX3](https://amoy.polygonscan.com/tx/0xf43c9c3598d40e694da459d3dff6a29e1545b4a2bed7b8f8e9d6cd91deb9c8e7) | removeCredential v2 | 47086433 | CredReg 0x94d6257c |
| [TX4](https://amoy.polygonscan.com/tx/0x7481713e809089a8f5ef0a8b841e79f92a2675a7952023cc5d3317a59acb64d1) | registerCredential v2 [ping,borrow,approve] | 47086612 | CredReg 0x94d6257c |
| [TX5](https://amoy.polygonscan.com/tx/0xc383381aa28aaf869c48bf20dcf807a2383423db488f1bef7bd4bc372b247c01) | registerIdentity(0xF6af -> 0xc6e30ac8) | 47086659 | IdReg 0xf53c87CB |
| [TX6](https://amoy.polygonscan.com/tx/0xff03f019a5358e1aadfa3c50c61d96924acfa09b28a3b7286eebca8d0a0e07df) | wallet common.kyc | 47086694 | CredReg 0x94d6257c |

TX2 was accidentally re-run once and reverted `CredentialAlreadyRegistered` (a harmless duplicate, no state
effect); the end state is correct. Both v1 and v2 are remove+re-add (4 txs), so there were transient windows -
v1-absent (TX1->TX2), a DIVERGENT window (TX2->TX3, ~428 blocks) where v1 was already `[ping,borrow,approve]`
while v2 was still `[ping,borrow]`, and v2-absent (TX3->TX4). Testnet, no traffic; stated honestly, not "no
gap". End state (post-TX4) both = `[ping,borrow,approve]`.

### Part 1 - approve reissue (agent ccid 0xc27694aa, my decode of the live blobs)
- v1 (6-field) and v2 (9-field) both decode `allowedActions = [ping 0xcd541a5f, borrow 0x4f943907, approve
  0x5219209e]` - **BYTE-IDENTICAL across v1 and v2** (consistency invariant). Delta vs prior = exactly `+approve`
  appended, order preserved.
- Everything EXCEPT allowedActions byte-identical to the pre-delta values: perActionCap 1e16, spendWindowAmount
  5e16, spendWindowPeriod 3600, revReg 0x1EFa0Af5; v2 also maxBorrowUsdc 50e6, borrowWindowAmount 200e6,
  borrowWindowPeriod 3600. typeIds unchanged (v1 0x57fe1f0a, v2 0xa4b0fb96). Both validate()=true. keccak256("approve")
  recomputed = 0x5219209e083275171774dab1df80982e9df2096516f06319c5c6d71ae0a8480c (the action-name hash, not the
  ERC-20 selector 0x095ea7b3). Rail 2B confirmed `[ping,borrow,approve]` is the complete least-privilege set (no repay).
- `approve` grants token-spending authority; the wallet's on-chain gate checks action-MEMBERSHIP only, not the
  spender/amount, so a broker-bypassing direct signer can approve an arbitrary spender/amount. Same bypass
  boundary as the borrow cap. EXECUTION note (borrow E2E, separate item): verify the actual allowance the wallet
  grants OBBorrowHelper is SCOPED, not unbounded `type(uint256).max` (standing drain authority).

### Part 2 - wallet-KYC, Option B (Legal matter 9 ADDENDUM 9a)
- `getIdentity(0xF6af3A53...)` = `0xc6e30ac8...` = the wallet's OWN ccid (`keccak256(abi.encodePacked("ob.ccid.v1",
  wallet))`, reproduced independently; cross-checked on the known-good principal pair). **NOT** Ilan's personal
  ccid 0x16c9d968 (Option A, discouraged) - the halt-if-personal guard PASSES.
- `getAccounts(0xc6e30ac8)` = [0xF6af3A53]; `getCredentialTypes(0xc6e30ac8)` = [common.kyc
  0x455af80b...]; `getCredential(0xc6e30ac8, common.kyc)` = `(0, 0x)` - EMPTY credentialData (no PII), matching
  the live common.kyc format. On IdReg 0xf53c87CB ("IdentityRegistry 1.0.0") + CredReg 0x94d6257c, the exact
  registries the MBT validator 0xCAEe7962 reads (`getCredentialSources(common.kyc)`).
- **WALLET permit proof (engine 0xfefa774d `check()` view):** `check(transferFrom, from=wallet, to=wallet)`
  returned clean (0x = the WALLET, as a transfer party, passes the whole 6-policy chain). Negative control
  (0xdEaD) reverted `0xfbdad4f4` (PolicyRunRejected), args[0] = AllowPolicy 0x5fe9d900, "address is not on allow
  list" - the chain discriminates. Both legs individually: AllowPolicy.addressAllowed(0xF6af)=true (dEaD false);
  validator.validate(0xF6af)=true (dEaD false). The allowlist leg was set by Ilan earlier (tx 0xfec416bd).
- **SCOPE CORRECTION (2026-09-08, caught by CCID/S2 test 1, verified by me):** the above probe used a
  wallet->wallet self-transfer, so it proved only the WALLET as a party. It did NOT cover the borrow's real
  op, which moves collateral wallet -> OBBorrowHelper 0x903b8667 (a MOVER) -> vault. I confirmed on-chain that
  the HELPER is currently uncredentialed (addressAllowed=false, validate=false, getIdentity=0x0), and that
  `check(transferFrom, from=wallet, to=helper)` REVERTS `0xfbdad4f4` at the AllowPolicy - i.e. the borrow flow
  reverts today at the helper (S2 test 1's atomic revert, no funds moved). The FULL multi-party borrow-flow
  permit (wallet AND helper AND vault all cleared) is PENDING the helper's own identity + common.kyc issuance
  (in progress: CCID issuing a mover credential, Token Controls allowlisting). LESSON: a permit proof must
  simulate the REAL op with the REAL parties, covering every mover - a self-transfer probe masks intermediaries.
- **MINT permit VERIFIED DIRECTLY (not inferred):** `check(mint 0x40c10f19, to=wallet)` returned clean 0x - the
  8-MBT mint to the wallet passes its own (mint-selector) policy chain.

**ENFORCEMENT / HONESTY (Legal 9a conditions):** the wallet common.kyc is an OPERATIONAL, TESTNET-ONLY grant
(agent-wallet footing, pools/vaults precedent) - the wallet is **NOT** a genuinely KYC'd person/entity. This
entry never states or implies the wallet is "KYC'd" or "verified". At any matter-9 escalation trigger the honest
fix is a distinct operational/agent credential type. MUST NOT propagate to mainnet ((c) trigger).

**Governance:** Legal matter 9 + addendum 9a on record (legal-review/DOCUMENT-LEDGER.md, read on the artifact +
Legal desk direct confirmation); Option B cleared, Option A discouraged. Ilan-direct auth for the on-chain act =
his 0xFc99 signature on the correctly-targeted writes (policy_signed_write_satisfies_direct_auth); this ledger
PUSH separately Ilan-greenlit. Provenance Option-B (CCID/Rail 2B), MIT types lib, existing audited ACE registries.

**Gate: PASS for the ISSUANCE (both parts): the approve reissue is correct and the wallet is properly bound +
credentialed + permitted as a transfer party; the 8-MBT mint to the wallet is directly verified as permitted, so
that mint may proceed.** This is NOT a clearance of the end-to-end borrow flow: that additionally requires the
OBBorrowHelper (mover) to be credentialed, which is pending. Actual autonomous borrow execution is a separate
verification item - and when it runs, decode `OpExecuted.success` (the wallet.execute inner result), not just
tx.status, and verify the wallet's approve allowance to the helper is SCOPED, not unbounded.

### AUTONOMOUS BORROW EXECUTION - E2E PROVEN (2026-09-15, this gate's own reads)
Tx [`0x073f508ebab9be240b8e788c3a6e5a7fb5e698ede627af559ba256f8d0e17be5`](https://amoy.polygonscan.com/tx/0x073f508ebab9be240b8e788c3a6e5a7fb5e698ede627af559ba256f8d0e17be5)
(Amoy 80002), status 1, block 47707364, from agent EOA `0xd63352` (autonomous signer), to agent wallet
`0xF6af3A53...` (`execute`), gasUsed 1,182,890.
- **`OpExecuted` inner result decoded: actionName="borrow", nonce 5, SUCCESS=TRUE** (target+nonce indexed;
  success at data word 2 - decoded against the wallet event layout, not tx.status).
- Full multi-party transfer legs on-chain: MBT `0xF6af`->helper `0x903b8667`->vault `0xf48f2683` (3e18 each);
  USDC vault->helper->`0xF6af` (24e6 each) - every mover (wallet, helper, vault) cleared the MBT compliance gate.
- Persisted state: `positions(96)`=(helper, 3e18 MBT, 24e6 USDC, active=true), nextPositionId=97; agent wallet
  USDC 0->24e6, MBT 8e18->5e18. State persists only on inner success (the OOG re-run left none) => proven.
- **Root cause of the earlier failed runs was OUT-OF-GAS** (harness gasLimit ~1.05M vs ~1.18M need; nested 63/64
  sub-call OOG), NOT a vault-code defect; the empty-returndata VaultCallFailed was the OOG signature. Scoped
  approve confirmed earlier (3e18, not max).
- **CAVEAT (unchanged, decoupled from the working borrow):** the Atlas vault `0xf48f2683` is a VARIANT build, NOT
  byte-identical to OB LendingVault@default (see the Plume twin-trap annotation above) - its source/audit
  provenance remains UNESTABLISHED. "The borrow works" is not "the vault source is audited"; that item stays open.

## Sonic Testnet (14601) wMBT v2 -> v3 migration - GATE PASS on both addresses (2026-10-06)

Handoff from Release Manager (PR #495 merged aa98c5b7 points the dApp HelpPanel + chains.ts at v3; cut
GATED on this gate's PASS, Session 8 holds the cut). This gate's own live reads on Sonic
(`https://rpc.testnet.soniclabs.com`, chainid confirmed 14601) and a from-source bytecode compile.

**1. WrappedMBTv3 `0x0259ae85183975f83297847e144bC4E1eCf908Bc` - PASS.**
- Identity: `typeAndVersion` = "WrappedMBT 3.0.0"; name "Wrapped Miami Beach Tower" / wMBT / 18.
- Live state: totalSupply 47000000000000000000 (47.0, the migrated supply), paused=false,
  owner `0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c` (0xFc99 testnet admin), `sanctionsRegistry()`
  = `0xD49E6EE6bBfaA44e8FCc23CE373B231fce6f57c6`.
- PROVENANCE (definitive): on-chain runtime == WrappedMBTv3.sol @ origin/main (aa98c5b) compiled with
  the via_ir profile, byte-for-byte, EXCEPT (a) three 20-byte windows holding the `sanctionsRegistry`
  IMMUTABLE = `0xD49E...57c6` (artifact immutableReference offset 582; source line 276 `immutable`) and
  (b) the trailing 32-byte CBOR metadata hash. No other byte differs. The immutable baked into code
  equals the `sanctionsRegistry()` read, so the sanctions binding is code-level and cannot be swapped
  post-deploy. Constructor reverts on a zero registry (src line 281). `isSanctioned` consulted at src
  lines 311 and 865.
- TWIN resolved by chain+typeAndVersion: `0x0259...` also exists on Minato (1946) as a
  GlobalSanctionsPolicy (DeployCanaryPolicySet broadcast); on Sonic it is WrappedMBTv3. Read on the
  live Sonic chain, not inferred.
- EIP-55: `0x0259ae85183975f83297847e144bC4E1eCf908Bc` is canonical (cast to-checksum identity);
  matches HelpPanel.tsx row exactly.

**2. GlobalSanctionsPolicy registry `0xD49E6EE6bBfaA44e8FCc23CE373B231fce6f57c6` (proxy, ERC1967) - PASS.**
- Identity: `typeAndVersion` "GlobalSanctionsPolicy 1.0.0"; impl (slot) =
  `0x8146668443F468F7b7C432660Ce00435Cc54FED0`, impl typeAndVersion "GlobalSanctionsPolicy 1.0.0".
- PROVENANCE: impl on-chain == GlobalSanctionsPolicy.sol @ origin/main (via_ir) byte-for-byte except the
  single 32-byte metadata-hash window. No immutables (ERC-7201 storage). Definitive body match.
- Functional: `isSanctioned(x)` returns false without revert (probed 0xdEaD and the owner key).
  `sanctionsAdmin` = `0x3055698887538cc6cF0d408e744B04F0806CB6E1`, owner `0xFc9933C8...`. Admin is
  separate from the deployer (satisfies the migration's own `sanctionsAdmin != deployer`).
- TWIN resolved: `0xD49E...` / impl `0x8146...` also appear in 06-contracts.md line 826 as a MINATO
  TransferRestrictPolicy 2.0.0 (same deployer+nonce cross-chain CREATE collision). On Sonic they are
  the GlobalSanctionsPolicy. Verified on the live Sonic chain.

**FLAGS (routed to RM + Session 8; neither is an address-correctness failure, both are public-exposure
readiness items):**
- **DOC CASCADE INCOMPLETE (Invariant #5).** `docs/public/06-contracts.md` (canonical registry) does NOT
  list v3 `0x0259...` or its registry `0xD49E...`; its Sonic 6.5 table still presents wMBT v2
  `0x670c3a58435cc13e9edA059F4093F31CedEEF7F3` and a DIFFERENT sanctions registry
  `0x646849292ab13bcD3Ef679f4e1a17839A658Da57` (impl `0x761a89...`) as the Sonic set. PR #495 updated
  chains.ts + HelpPanel only. The canonical doc must register v3 + `0xD49E...` and mark v2 + `0x6468...`
  superseded in the same cut, or the public contract registry contradicts the dApp.
- **SANCTIONS-ADMIN KEY SEPARATION REDUCED.** The migration bound v3 to a NEW registry (`0xD49E...`)
  whose `sanctionsAdmin` is `0x3055...`, the Sonic HOT allowlister / issuer key (see Allowlister row
  above). The previously-documented Sonic registry (`0x6468...`) used a dedicated, deliberately-separate
  sanctions key (`0x73ec6Df02b5C4e4223F4Cc8Af6b4b69B2ff92684`). So the same hot key now both allowlists
  and sanctions on the v3 path, less separation than the documented intent. Confirm this is deliberate.

Enforcement note: the sanctions binding is immutable + the registry is live and functional, but a live
sanctioned-transfer REJECTION proof was not run (needs a signed tx; attached != a fired guard). Deferred
to Execution Staging / S8 if a reject-proof is wanted before the cut.

**UPDATE 2026-10-06 (S8 disclosed the duplication; this gate independently corroborated every read).**
There are TWO distinct GlobalSanctionsPolicy proxies on Sonic, both owned by 0xFc99, both
typeAndVersion "GlobalSanctionsPolicy 1.0.0" (so typeAndVersion alone does NOT distinguish them):
- `0x646849292ab13bcD3Ef679f4e1a17839A658Da57` (2026-10-03): impl `0x761a89CF...`, sanctionsAdmin
  `0x73ec6Df02b5C4e4223F4Cc8Af6b4b69B2ff92684`, `registry()` REVERTS (older impl, no getter). This is the
  one the PUBLIC doc 06-contracts.md 6.5 lists. S8 could not establish it governs anything live; its admin
  key is undocumented in RUNBOOK/PROJECT_STATE.
- `0xD49E6EE6bBfaA44e8FCc23CE373B231fce6f57c6` (2026-10-06 migration): impl `0x8146...` (== main), admin
  `0x3055...` (the known Sonic hot allowlister/issuer key), `registry()` returns address(0) = standalone,
  holds its own list. THIS is the one v3 binds to, immutably.
Neither can be re-pointed at the other (`registry` set only under onlyInitializing, no setter); unifying
needs a redeploy of one side. Migration MIRRORED balances rather than moving them, so v2 `0x670c` reads
paused=true + the same 47.0 (NOT a double-count; 13 holders reconcile at exact wei, confirmed). One holder
is a contract, Nova Finance vault `0x49f1aa7C`, collateralToken() immutably v2, 1.000005285714428575 wMBT
on BOTH v2 and v3 and frozen because its collateral is the paused v2 (OPEN ITEM with Ilan, NOT resolved by
this PASS). v2's transferFrom spender-screening defect is permanent in that direct-deploy bytecode; pause
is the control, correctly.

**GATE DECISION: PASS STANDS; the cut does NOT need to wait on the duplication.** v3 (the exposed token)
is immutably bound to a genuine, functional registry (`0xD49E`) with a known admin, and the cut moves
users off a paused v2 onto the live v3, which improves the user-facing state. The duplication is a
governance/doc issue, not a v3-safety issue; the blocklist is empty today so nobody slips through now.
REQUIRED to ride WITH the cut (ties to the doc-cascade flag): correct 06-contracts.md so v3's authoritative
sanctions registry reads `0xD49E` (admin `0x3055`), and clarify/retire `0x6468`, else the public doc
misdirects any future v3 sanction to a registry v3 does not read (silent no-op; a real compliance-ops
hazard). WHETHER to unify the two registries (a redeploy) is an Ilan governance decision, surfaced to him.

**REJECT-PROOF (2026-10-06). Ilan authorized a live reject-proof before the cut.**
READ-LAYER PROOF (this gate's own, zero on-chain footprint, eth_call + state override, NOT a live tx and
NOT presented as one): with the only difference being the registry's `blocked[H1]` bit, a wMBT v3 transfer
between two allowlisted holders flips from PERMITTED to REFUSED:
- Control, no override: `eth_call v3.transfer(H0 0xCf3099... -> H1 0xC02c9Fd2..., 1)` returns 0x..01
  (success). Both allowlisted, H1 not sanctioned. (Doubles as a permit-proof: a real credentialed-to-
  credentialed transfer is allowed.)
- Override on (`0xD49E` slot `0x09d35ee8...214b` = blocked[H1], set to 1): same call REVERTS
  `0xae1427c1 + H1` = `AddressSanctioned(0xC02c9Fd2...)`. Selector/arg match exact.
  Attribution isolated: allowlist + balances held constant, only the sanction bit changed, so the revert
  is the registry's sanctions leg, not allowlist/balance. Proves v3's guard FIRES against the live deployed
  bytecode of both v3 and registry `0xD49E`. Slot derived independently (ERC-7201 base
  `0xa4f1...5700`, sanctionsAdmin at field 0 confirmed = 0x3055, blocked mapping at base+1).
LIVE ADMIN-WRITE LEG still open (what the read-proof does NOT cover): that `0x3055` can actually write a
sanction on the real registry. Needs a signed tx via Execution Staging; this gate does NOT sign and did NOT
pre-sign. Pre-staged for the signer to review: to `0xD49E` from `0x3055`,
`setSanctioned(H1,true)` = `0xe8579015...fd86...01`, restore `setSanctioned(H1,false)` =
`0xe8579015...fd86...00`. On land, this gate re-reads isSanctioned(H1) true-then-false and re-runs the
transfer eth_call (reverts AddressSanctioned while sanctioned, succeeds after restore), then ledgers it.

**UPDATE 2 (2026-10-06): flag B RESOLVED as a defect; ceremony target changed to the OB Tester wallet.**
S8 confirmed the hot-key sanctionsAdmin was NOT deliberate: during the migration S8 set 0xD49E's
sanctionsAdmin to 0x3055 (the Sonic hot issuer key already in hand) WITHOUT knowing the 2026-10-03 registry
used a dedicated sanctions key 0x73ec6Df0, collapsing a documented separation. Recorded as S8's error, not
a design decision. REMEDY (cheap, owner-only, one write): `setSanctionsAdmin(0xD49E, <dedicated key>)`.
S8 recommends AFTER the cut (pre-cut the dApp points at the PAUSED v2, so holding users there for key
hygiene is the worse trade). ILAN DECIDED 2026-10-06: remedy AFTER the cut. Agreed sequence: live
reject-proof lands -> cut releases -> setSanctionsAdmin rotate as a fast follow. Blocklist is empty in the
gap, so the hot key being admin creates no active exposure meanwhile.
SUPPORTING EVIDENCE for the rotation, now OBSERVED rather than principle (2026-10-07): on this same day the
SAME hot key 0x3055 signed credential issuance AND revocation on Sepolia (the Z backfill, see the CCID
section below) while also holding the Sonic sanctions-admin role. One key, two chains, two unrelated
compliance workflows in one afternoon: exactly the concentration the dedicated-key intent exists to
prevent. This strengthens the flag-B rotation case on an observed fact, not just design principle.
CEREMONY TARGET moved off real holder H1 (5.0 wMBT; a partial-failure between the two signed ops would
freeze a real balance) to the OB Tester wallet `0xC6757AB6Ba6e620f4f2C3FdC54180fC32AB04238` (= the TESTER
constant in MigrateSonicToV3.s.sol; RUNBOOK line 402 / PROJECT_STATE line 663 internal-test-wallet class).
This gate independently verified it: EOA (code 0x), zero wMBT on BOTH v2 and v3, not a minter, not owner,
already allowlisted (so both ceremony ops stay on the hot key 0x3055; no cold-key write), isSanctioned
false now. Strictly safe: a failed restore freezes nothing and touches no admin path.
Read-layer proof RE-RUN against the Tester to match the live target: control
`v3.transfer(H0 0xCf3099... -> Tester, 1)` = success; override blocked[Tester]=1 (slot
`0xe3c2c459...4bd6`) -> reverts `0xae1427c1 + Tester` = `AddressSanctioned(0xC6757AB6...)`. Exact match.
Pre-staged calldata to `0xD49E` from `0x3055`: setSanctioned(Tester,true) =
`0xe8579015...ab04238...01`, restore = `...00`. S8's own eth_call preflight of setSanctioned(Tester,true)
FROM 0x3055 returned 0x (no revert), so the hot key holds the admin role live and the write will not fail;
that is a simulation, NOT the artifact. Live run goes to Execution Staging; this gate does not sign.

**BLOCKER CORRECTION (2026-10-07, from S8; authority facts verified by this gate on live Sonic).** The live
ceremony is NOT a staged tx merely awaiting Ilan's signature. Its setSanctioned is onlySanctionsAdmin =
0x3055, a hot keeper-box key (issuer-service .env; NOT in Bitwarden; cast absent on that box). It is
operable on the box via bun+ethers (the Z backfill proved that today), but Execution Staging will not stage
it as an Ilan-signable op and is parked. Resolution S8 proposes and this gate verified: 0xD49E.owner() =
0xFc99 (cold admin Ilan holds); setSanctionsAdmin(address) (0x8fa4e560) is onlyOwner + notAdapter; eth_call
simulate setSanctionsAdmin(0xFc99) is CLEAN from 0xFc99 and REVERTS OwnableUnauthorizedAccount(0x3055) from
0x3055. So the chain is: Ilan AUTHORISES the rotation -> rotate 0xD49E sanctionsAdmin (from 0xFc99) to a key
Ilan controls -> THEN the reject-proof is signable on his path -> this gate verifies the live revert -> cut.
NET (framing corrected 2026-10-07, S8 retracted the "sole path" claim and this gate agrees): rotation-first
is a CHOICE, not a physical necessity. The ceremony COULD run today via the keeper-box bun+ethers path that
signed the Z backfill with 0x3055. We rotate first for ARTIFACT QUALITY: if the reject-proof is signed by
0x3055 and we rotate afterwards, the permanent ledgered artifact records the arrangement we are abandoning;
rotate first and the proof is signed by the dedicated sanctions key, so the record shows the arrangement we
actually want. The flag-B rotation thus moves ahead of the ceremony by CHOICE. It is not a staged tx
awaiting a signature either (nothing is staged); the next step is Ilan's rotation op. (Target key is Ilan's choice; for the flag-B separation it should
be a dedicated key, not the hot issuer key and ideally not the cold owner either.)
ILAN DECIDED 2026-10-07: rotate the sanctions admin to a dedicated key separate from 0x3055 and 0xFc99,
fully resolving flag B. TARGET (supplied by Ilan via S8): `0x73ec6Df02b5C4e4223F4Cc8Af6b4b69B2ff92684`, his
Sonic wallet. Verified suitable: canonical EIP-55; != 0x3055 and != 0xFc99; nonce 0 on Sonic (unused, so
genuinely dedicated); EOA, zero gas (needs funding from 0xFc99). Better than a fresh key because it is
ALREADY 0x6468's sanctionsAdmin, so rotating 0xD49E to it means ONE key administers BOTH Sonic sanctions
registries (a fresh key would have made three); the duplicate-registry problem remains but no longer needs
two keys to action a sanction.
POSSESSION-PROOF GATE BEFORE ROTATION: nonce 0 proves the key is unused but ALSO means it has never been
proven to sign; an address in a password manager is not proof the private key matches. S8 asked Ilan for an
offline `cast wallet sign -i` over a fixed message (signature only, no key, no gas, no tx) and will route it
here. THIS GATE will INDEPENDENTLY verify the signature recovers to 0x73ec6Df0 before any on-chain rotation,
and holds verification until it clears (verify the signer before trusting the key; third-party sig proof
from that software).
SEQUENCE: possession proof verified -> Ilan signs setSanctionsAdmin(0xD49E, 0x73ec6Df0) from 0xFc99 (owner)
-> this gate asserts sanctionsAdmin(0xD49E) == 0x73ec6Df0 SPECIFICALLY (not merely changed; setSanctionsAdmin
has NO zero-address or validity guard, so "changed to something" != "changed to the intended thing"), and
!= 0x0 / 0x3055 / 0xFc99, owner() still 0xFc99 -> 0x73ec6Df0 signs the reject-proof (setSanctioned(Tester,
true/false)) -> this gate verifies isSanctioned(Tester) true-then-false with the transfer reverting
AddressSanctioned while sanctioned -> cut releases. (This gate does not re-ask Ilan; S8 routes the address
and the possession proof.)

POSSESSION PROOF CLEARED (2026-10-07), independently verified by this gate (not inherited from S8):
message `OB sanctions-admin rotation proof 2026-10-07` (no trailing newline), sig
0x82b10ac1...f57b1111b. `cast wallet verify` POSITIVE against 0x73ec6Df0 ("Validation succeeded"); NEGATIVE
against 0x3055, 0xFc99, and a random address (fails each, so it is specifically that key and the verifier is
not always-passing); TAMPER control (altered date in the message) fails against 0x73ec6Df0, so the signature
binds to the exact message, not replayable. The private key behind 0x73ec6Df0 is held and signs, and is
distinct from the hot key and the owner. STAGING CALLDATA independently confirmed: provided
0x8fa4e56000000000000000000000000073ec6df02b5c4e4223f4cc8af6b4b69b2ff92684 decodes to setSanctionsAdmin(
0x73ec6Df0) and byte-matches this gate's own `cast calldata` reproduction (no hidden/wrong arg).
PROVENANCE, flag-B substance (per S8, recorded because it IS the point of the rotation, not a nicety): Ilan
signed on his Mac, NOT the keeper box. The keeper box hosts the hot key 0x3055; putting the new dedicated
key on that same host would make the on-chain role separation cosmetic while the compromise blast radius
stayed exactly as concentrated. The separation is only real if 0x73ec6Df0 never touches that box. Bitwarden
entry `OB-SONIC-SANCTIONS-ADMIN-TESTNET` corroborates intent but proves nothing cryptographically; the
SIGNATURE is what binds the key to the address. GATE CLEARED: rotation may proceed (fund 0x73ec6Df0 from
0xFc99 ~0.5 S, then Ilan signs setSanctionsAdmin from 0xFc99). This gate holds for the on-chain rotation to
assert sanctionsAdmin(0xD49E) == 0x73ec6Df0 before the ceremony.

ROTATION LANDED + VERIFIED by this gate (2026-10-07), FLAG B CLOSED.
- Funding: tx 0x3099de65928fa846f45fe54d3c288b2f5a77423406a351931616af9cfbfc4365, 1 S 0xFc99 -> 0x73ec6Df0,
  status 1, block 19037519. 0x73ec6Df0 balance now 1.0 S (gas for the ceremony).
- Rotation: tx 0x3fe729f6b1da12feea50f1378560b8842d0bd937cf39b4b4f9e954c089259594, status 1, block 19037630,
  from 0xFc99 (owner) to 0xD49E, signed by Ilan on his Mac.
- THIS GATE's reads: sanctionsAdmin(0xD49E) == 0x73ec6Df0 SPECIFICALLY (the intended address, not merely
  "changed"), and != 0x0, != 0x3055, != 0xFc99; owner() still 0xFc99. Transition EVENT-evidenced, not only
  the getter: rotation tx log on 0xD49E has topic0 0x44a4cfdd5671c7b60a55f9124ab4b1e457f16cc2fc319b68ea5fd7ca11416356,
  which equals this gate's own keccak256("SanctionsAdminTransferred(address,address)"); old (topic1)=0x3055,
  new (topic2)=0x73ec6Df0.
SEPARATION IS NOW REAL, not nominal: v3 allowlisting is onlyOwner (cold 0xFc99); 0xD49E sanctions is the
dedicated 0x73ec6Df0; the hot issuer 0x3055 holds NEITHER on this path. Flag B resolved.
BONUS: 0x6468's admin was already 0x73ec6Df0, so both Sonic sanctions registries now share one dedicated
key (the "better than a fresh key" argument paid off; actioning a sanction across both no longer needs two
keys). The duplicate-registry structure is unchanged, still an Ilan unify-vs-keep decision.
S8 CORRECTION (this gate did NOT carry it, recording for completeness): S8 briefly escalated to Ilan that
the funding value might be an exposed private key, after a single getLogs lookup returned nothing because it
ran before the tx was indexed. It was the funding tx all along; NOTHING was exposed and 0x73ec6Df0 is clean
(possession proof + negatives + tamper + rotation all stand). Lesson (S8's, same class as this gate's own
absence-discipline): a single negative lookup is not an absence, especially when the expected artifact is
exactly what you just asked for.

CEREMONY SIGNER IS NOW 0x73ec6Df0 (not 0x3055) - the point of rotating first, so the permanent reject-proof
artifact records the arrangement we keep. Target unchanged: OB Tester 0xC6757AB6..., gas funded. Next: new
key signs setSanctioned(Tester,true); this gate reads live isSanctioned(Tester)=true + the transfer reverting
AddressSanctioned; setSanctioned(Tester,false); this gate confirms clean; then cut.

LIVE REJECT-PROOF CAPTURED (2026-10-07) - the fired-guard proof against REAL stored state, signed by the
dedicated admin. OP1 (sanction): tx 0xce00cfd3e62ced5e7d09be1d8e20a29145a9211bff397bb5dffdd0dafe68eff6,
status 1, block 19040321, FROM 0x73ec6Df0 (the rotated admin) TO 0xD49E - so the new dedicated key genuinely
WROTE the sanction, proving the admin-write leg live (what the earlier storage-override preflight could not).
Event SanctionedSet: topic0 0xe120f1c2a7416d3ef8776f69c89583cdc9ab7f2c3e525f55df52c5b5ce453608 == this
gate's own keccak256("SanctionedSet(address,bool)"), indexed subject = Tester 0xC6757AB6..., data = 1.
THIS GATE's reads: isSanctioned(Tester) = true live; then the REAL transfer eth_call vs stored state (NO
override): transfer(H0 0xCf3099... -> Tester, 1) REVERTS 0xae1427c1 + Tester = AddressSanctioned(0xC6757AB6).
PERMIT CONTROL: transfer(H0 -> H1 0xC02c9Fd2..., 1) SUCCEEDS (H1 not sanctioned), so the guard is SPECIFIC
to the sanctioned recipient, not a blanket block; allowlist + balances held constant, so the revert is the
sanctions leg. This supersedes the override preflight: same result, now against actual stored state and with
the live admin-write proven. OP2 (restore setSanctioned(Tester,false)) cleared by this gate to fire; on land
this gate confirms isSanctioned(Tester)=false and the transfer eth_call clean, then signals RM to release
the cut.
TWO-SOURCED: S8 independently captured the same live refusal from the receipt + its own reads, with a
control to a DIFFERENT recipient (transfer(H0 -> 0xFc99, 1) returns true) than this gate's (H0 -> H1). Two
sessions, two independent controls, same AddressSanctioned(Tester) revert. ROTATION PROVEN BY USE: this gate
read 0x73ec6Df0 nonce = 1 now (was 0 immediately after the rotation, which was signed by 0xFc99), so OP1 was
the dedicated key's FIRST on-chain signature ever - the key genuinely administers the registry, a distinct
evidence axis from the getter and the SanctionsAdminTransferred event. Flag B is fully proven: by getter, by
event, and by use.
OP2 RESTORE VERIFIED by this gate (2026-10-07), REJECT-PROOF CYCLE CLOSED: tx
0x2aa40cb31761e147cc3d5c7921e64e13092ba098c1dd29c3f61c676e16a39acd, status 1, block 19040397, from
0x73ec6Df0; SanctionedSet(Tester) data = 0. This gate's reads: isSanctioned(Tester) = false; transfer(H0 ->
Tester, 1) eth_call now SUCCEEDS (0x..01, no AddressSanctioned). Full live cycle proven end to end:
sanction (OP1) -> guard fires AddressSanctioned -> restore (OP2) -> transfer permitted again. Tester
admission back to clean. CEREMONY COMPLETE.

=== SONIC v3 GATE: ALL V&L CONDITIONS SATISFIED (2026-10-07) ===
Both addresses PASS (identity + live state + byte provenance + EIP-55). Live sanctions reject-proof CAPTURED
and CLOSED (two-sourced, real stored state, discriminating control, signed by the rotated dedicated admin;
flag B proven by getter/event/use). Cut-gate resume condition (RM: V&L PASS on both) + Ilan's run-reject-
proof-before-cut both MET. Cut may release. FLAG A (Invariant #5) CONFIRMED DONE on origin/main 3582330 (this gate re-read
06-contracts.md): WrappedMBT v3 0x0259 registered as CURRENT, GlobalSanctionsPolicy 0xD49E as "v3 reads",
CREBridgeConsumer v3 0x031A...; v2 0x670c marked SUPERSEDED/PAUSED and 0x6468 marked "NOT the one v3 reads";
and it carries the operator-hazard note this gate raised verbatim in substance ("SANCTIONING A wMBT HOLDER:
WRITE 0xD49E, NOT 0x6468; a sanction written to 0x6468 is a silent no-op against v3"). Doc and on-chain
reality now agree. NO OUTSTANDING V&L CONDITION. Z MBT residual (PR #502) remains Ilan's leave-vs-sweep
call, not gating.

POST-CUT (2026-10-07, S8 reported; recorded with the forward hooks this gate owns):
- CUT TAKEN at commit 35823302 (zip /Users/ilan/Desktop/onchainbridges-dApp-2026-10-07-sonic-v3-3582330.zip,
  1,992,465 bytes, md5 dbcf5dd3441b4b8f1f654ee776ffd8a9, verified by rendering the fingerprinted dist).
  CUT IS NOT DEPLOY: the LIVE byte-comparison happens after Ilan uploads. FORWARD HOOK for this gate: when
  S8 routes the post-upload evidence, verify the LIVE dApp surface actually serves v3 0x0259 to users (the
  user-visible address is the thing that matters), not just that the cut built.
- WATCH ITEM (S8's open discrepancy, not resolved): the dist renders 3 Nova Finance venues where production
  shows 4. S8's explanation (the #495 offline-flag filter) is now corroborated by the controlled #495 A/B
  (4 on main, 3 on branch), but that is still not an observation of PRODUCTION. PRE-REGISTERED PREDICTION
  (S8, stated before the reading, so the post-upload check is discriminating not confirmatory): after upload
  production goes 4 -> 3 = CONFIRMED; if it STAYS at 4, the delisting did not reach the live surface and it
  is a FINDING, log it as one. This gate makes the venue count part of its post-upload live verification.
- Z MBT residual DISPOSITION: merged PR #502 records "leave it"; Ilan's silence ratifies, an override routes
  to RM as a row amendment. Effectively closed as leave-and-record unless Ilan overrides.

STANDING VERIFICATION PRINCIPLE (from Ilan's ruling + CCID Spike C, 2026-10-07; recorded before a PASS
request arrives): OB never holds customer keys/funds (see policy_ob_never_holds_customer_keys), and Tweed is
CUSTODIAL (Ilan's correction). Identity binding differs by custody model: self-custody is a cryptographic
proof of control; any custodial path (OB's or a licensed provider's) reduces to an OPERATOR ATTESTING ITS
OWN MAPPING. CONSEQUENCE FOR THIS GATE: a future artifact claiming "user-proven identity binding" for
custodially-held holders is NOT VERIFIABLE BY CONSTRUCTION, not merely unverified - do not PASS it as a
cryptographic binding; for a custodial path the verifiable object is the PROVIDER'S LICENCE, not a signature.
DE-ANCHOR FROM THE VENDOR NAME (2026-10-07, S8/Legal): state this in KEY-MODEL terms, not a vendor name.
Legal found Tweed is DEFUNCT (site dead mid-2026, seed-only), and its archived marketing claimed "100%
self-custodial, no custody licence needed" - the OPPOSITE of the custodial characterization - so the vendor
label is doubly unreliable as an anchor. The durable, vendor-agnostic rule: SELF-custody = cryptographic
proof of control (verifiable); any CUSTODIAL path = operator attesting its own mapping (verify the provider's
licence, never PASS a user-proven-binding claim as cryptographic). De-naming is S8's proposal to Ilan, not
yet ratified; recorded here as pending, and this gate does not anchor verification to the Tweed name meanwhile.

SEIZURE-PATH AUDIT on live wMBT v3 0x0259 (raised by Token Controls, settled by S8, INDEPENDENTLY
re-derived by this gate 2026-10-08): NOT LIVE, two-sourced + current-as-of-head.
CONCERN: v3 carries the clawback gate in bytecode with challengeWindow()=0 and attestorRegistry()=0x0
(pre-#503, no destination lock). forcedTransfer is onlyAgent and a zero challenge window makes a freeze
instantly clawback-eligible, so a lone agent could freeze any holder and force-transfer the balance. The
only thing preventing it is that no agent is appointed - a negative, the class we get burned on.
THIS GATE's independent re-derivation (different anchoring/chunking than S8, and extended PAST S8's
point-in-time read to the live head):
- State: challengeWindow()=0, attestorRegistry()=0x0, owner()=0xFc99, authorityEpoch()=1, isAgent()=false
  on all five plausible roles (owner 0xFc99, hot 0x3055, dedicated 0x73ec6Df0, CRE consumer 0x031A..,
  Allowlister 0x277C..). Non-exhaustive, supporting.
- EXHAUSTIVE negative via the AgentSet event (topic0 0xdfbbd189... derived by this gate, matches S8):
  source WrappedMBTv3.sol@aa98c5b (= deployed bytecode) emits AgentSet UNCONDITIONALLY on addAgent (line
  542) and removeAgent (550), so "no AgentSet event" == "no agent ever granted". Scan of 0x0259 for the
  AgentSet topic from block 19000000 to the live head 19049281 in five error-checked 10k chunks = 0 events,
  every chunk exit 0, no range-limit/error markers. POSITIVE CONTROL: an all-logs query on the same
  contract over 19010393-19016708 returned exactly 52 logs (matches S8), proving the instrument reaches the
  token's logs rather than silently returning empty. So NO AGENT has ever been appointed, to the current
  head - every onlyAgent path (forcedTransfer, freezePartialTokens, recoveryAddress, setWalletRetired) is
  uncallable by anyone, owner included. Confirms S8, now two-sourced and AS-OF head.
FINDING THAT SURVIVES (audit-class, this is the "enforced" claim resting on an operational non-action, not a
guard): holder protection from seizure on wMBT v3 rests ENTIRELY on "no agent appointed", which is ONE owner
tx away, with BOTH mitigations absent (challengeWindow=0, no destination lock, attestorRegistry=0x0).
RECOMMENDATION TO ILAN (routed): set challengeWindow to a real value (owner tx, NO redeploy) BEFORE any agent
is ever appointed; sequence the destination lock into the next wMBT deploy, since v3 is a direct deploy with
no proxy so PR #503 (merged code) cannot reach the live token. Accurate on all surfaces: #503 closes the
destination gap in MERGED CODE; the five EVM MBTs are pre-v2 with nothing deployed; Sonic wMBT v3 is deployed
and carries the gate but every agent-gated path is uncallable because no agent exists.

MITIGATION 1 LANDED (2026-10-08), verified by this gate's own live read: challengeWindow set to 30 days.
Two writes, both from 0xFc99 status 1: W1 setChallengeWindow(604800=7d) tx
0x970cc53558a11c4c3448356079eb14a2605bf5ffec8c027635128ffe45724e26 (blk 19049423) was the WRONG value (7d
came off shell history; Ilan chose 30d), caught by Execution Staging on the challengeWindow() read-back and
corrected by W2 setChallengeWindow(2592000=30d) tx
0xddce03e3b644f27bf9978ba93eec684e02f98f43e78229b3199f9aaa67190d4e (blk 19049451); W2 event (topic0
0x9469ae9d...) decodes old 604800 -> new 2592000. THIS GATE's authoritative live read-back: challengeWindow()
= 2592000 (30d), owner() 0xFc99 unchanged, isAgent(owner) false. FINAL state = 30 days per Ilan's decision.
EFFECT: a freeze is no longer INSTANTLY clawback-eligible; even if an agent were appointed it must freeze
and wait 30 days before forcedTransfer is eligible, restoring a real challenge window. RESIDUAL (still open):
the destination lock (clawbackDestination, #503) remains absent and cannot reach direct-deploy v3; sequence
it into the next wMBT deploy. Seizure risk now gated by BOTH "no agent appointed" AND a 30d window, down
from resting on the single operational non-action.

PR #503 (clawback destination lock) - V&L SCOPE RULING + CLAIM BOUNDARY (2026-10-08). This gate's charter is
DEPLOYED contracts. #503 deploys NOTHING and this gate verified the basis: it is an OPEN PR (gh: state OPEN,
mergedAt null, head 4a9ea54b43d4e7b44fb5d8958e7174201ec37a67 NOT on origin/main), and the one place the gate
is deployed - Sonic wMBT v3 0x0259 - REVERTS on a live clawbackDestination() call (function absent in the
deployed bytecode; 0 mentions in the deployed source @aa98c5b), so it is a direct deploy this PR cannot
reach. There is NO deployed artifact to verify and NO address to ledger, so this gate issues NO deployment
PASS on #503 and does not invent one to fill a resume slot. (Source review of the undeployed PR - 6 guard
call sites, fail-closed ClawbackDestinationUnset, the comment-only RWATokenV2 docstring fix that correctly
says onlyOwner CAN redirect - is S8's code-review lane; this gate did not independently re-review undeployed
code and does not vouch for it.)
ACCURATE CLAIM BOUNDARY (the thing this gate DOES own, so a false claim is caught here): the destination lock
is PROPOSED in open PR #503, NOT merged and NOT live; NO live clawback is destination-locked today; the only
deployed gate instance (Sonic v3) cannot be reached by this PR and would need a redeploy. If any future
artifact cites #503 as evidence that LIVE clawbacks are destination-locked, that is FALSE.
V&L REAL TRIGGER: verify the destination lock at the NEXT deploy that actually carries it to a live token
(the next wMBT deploy), not on the code merge. RM should re-scope #503's "V&L PASS" resume condition
accordingly; Ilan's word remains a separate gate. (RM confirmed the re-scope ACCEPTED in full 2026-10-08: no
deployment PASS, V&L verifies at the next destination-locked deploy, Ilan's word the remaining gate.)
PROVENANCE one-liner (TC asked for ledger completeness; this gate VERIFIED it at head 4a9ea54b rather than
inheriting, NOT a full copyright audit of undeployed code): the #503 diff touches 8 files (the 3
clawback-bearing tokens RWATokenV2/WrappedMBTv3/YieldTokenV2 + their 3 test files + 2 phase2 deploy scripts),
adds NO new files, and changes NO SPDX / license-header / pragma lines. The added code is original OB
authorship mirroring the existing IN-FILE eligibility/attestor pattern - error ClawbackDestinationUnset
(its own comment says "mirroring EligibilityPolicyUnset / AttestorRegistryUnset"), an onlyOwner
setClawbackDestination, and a fail-closed view guard (revert when unset) - a clean-room restatement of an
in-file pattern, not a third-party import. No third-party code, SPDX unchanged. This closes the last
V&L-adjacent item on #503.
#503 MERGED 2026-10-08 (this gate verified: gh state MERGED, mergeCommit bae2652ab1c4fbc052341c8c086513666f1a6c60
on origin/main; the squash used --match-head-commit 4a9ea54b, the exact head this gate provenance-read, so the
read attaches to the merged content; RM reports post-merge main CI SUCCESS). Lock is now in the MERGED source
(clawbackDestination appears 6x in each of RWATokenV2/WrappedMBTv3/YieldTokenV2 on main, was 0 pre-#503).
CLAIM BOUNDARY RE-CONFIRMED post-merge by a live read, and it matters MORE now not less: merging to main did
NOT touch any deployed bytecode - live Sonic v3 0x0259 STILL reverts on clawbackDestination(). So the lock is
on main and still NOT live anywhere; zero live clawbacks are destination-locked; any artifact citing merged
#503 as live-locked is FALSE. V&L deployment gate ARMS at the next destination-locked deploy (next wMBT
deploy); nothing further on #503.

POST-UPLOAD LIVE VERIFICATION of the Sonic v3 cut (2026-10-09, this gate's own fresh re-run; S8 had done it
2026-10-08 but my filing was lost to a reboot, so re-derived independently). Host rwa-testnet.onchainbridges.com.
- BYTE-COMPARE (cut zip on Ilan Desktop 3582330 vs live curl, md5s computed by THIS gate from both ends, not
  inherited): index.html 5f2400881f188bf5edb7efd2cabd434a == ; bundle assets/index-B_Siephm.js
  1a2de5947d653929ee35b857c967f1c0 ==, 2,020,141 bytes, real vite JS not the SPA-fallback. The LIVE surface
  IS the cut, byte-for-byte (not an HTTP-200 check; this host returns 200+SPA-index for missing files).
- v3 0x0259ae85 present in the live bundle (2). 0xD49E (v3 sanctions registry) ABSENT - correct, operator-
  side, not a dApp address. v2 0x670c3a58 appears 3x, all benign by this gate's OWN context read: (1) a
  `paymaster:` field (cross-chain address twin, NOT wMBT), (2) the Sonic Nova venue config (collateral wMBT,
  the delisted venue), (3) the YT stack config (`mbt:`). No live user-reachable surface points at paused v2.
- HOST-SIDE PHP survived the upload, checked by BODY not status (catch-all-rewrite trap): canton-secret.php
  0 bytes (present), solana-rpc.php + sonic-rpc-proxy.php {"error":"POST only"} (present, executing),
  canton-holding.php {"error":"lockId must be a non-negative integer"} (present). solana-rpc.php is not in
  the repo, so nothing would have reminded anyone it existed - it survived.
- PRE-REGISTERED PREDICTION RESOLVES: production venue count 4 -> 3, CONFIRMED (not a finding). S8 live-
  RENDERED the page (Nova HRT + VCC on Sepolia, MBT on Solana Devnet; Sonic venue GONE, string "Sonic" absent
  from that route's DOM); this gate proved the live bundle byte-identical to what S8 rendered and read the
  Sonic venue's offline flag in the bundle config. SECOND LIVE RENDER by this gate (own browser, 2026-10-09):
  the live "Earn yield" venue table shows exactly THREE Nova Finance venues - HRT (Sepolia), VCC (Sepolia),
  MBT (Solana Devnet) - and NO Nova/Sonic venue; "Sonic Testnet" appears in no venue row. 4 -> 3 CONFIRMED by
  two independent live renders on byte-identical bundles. (Also observed: the venue-disclosure footer is
  present - venues are OB-operated testnet stand-ins, terms OB-set for demonstration.)
- INFERRED, NOT OBSERVED (per S8, recorded as such): the bridge picker reading "Sonic (paused)" was verified
  against the dist pre-cut, not re-observed live (not on the loaded route). Identical-by-construction via the
  byte-match, but that is reasoning from identity, not a live reading.
CARE POINT (S8, this gate noted): setSanctionsAdmin has NO zero-address guard; setting admin to 0x0
succeeds (recoverable, since onlyOwner can set it again). So the post-rotation check MUST assert
sanctionsAdmin == the INTENDED new address (and != 0x0, != 0x3055, != 0xFc99), not merely that it changed.

RETRACTED (2026-10-07) - a "dead-admin" finding briefly recorded here (0x6468's admin 0x73ec6Df0 nonce 0 /
zero gas / undocumented, inferred possibly-unheld and un-administrable) is WITHDRAWN. The on-chain
observations were all correct, but the inference was wrong: ILAN HOLDS 0x73ec6Df0 (it is his Sonic wallet,
supplied as the rotation target). Undocumented + unused does NOT mean unheld; key custody is not an on-chain
fact (nonce 0 proves the key is UNUSED, not that nobody holds it). Both S8 and this gate carried the bad
inference; struck rather than downgraded. 0x6468 IS administrable by 0x73ec6Df0 (Ilan) and its owner 0xFc99.
Doc-cascade note for flag A stands narrowly: 0x6468 is superseded by 0xD49E for the v3 path and should be
marked superseded in 06-contracts.md; it is NOT dead-admin. Flag-A SCOPING (per CCID): fixing 0x6468 in the
docs is the registry-steward/doc lane; CCID owns the identity/credential registries, not the sanctions-policy
registry, and Sonic is allowlist-only on their side with no credential leg, so there is no CCID artifact in
this cascade.
HYGIENE FOLLOW-UP (not a finding; CCID's valid meta-point survives the retraction): 0x73ec6Df0 is HELD by
Ilan but was UNDOCUMENTED in RUNBOOK/PROJECT_STATE/06-contracts. Since it is becoming the sanctionsAdmin of
BOTH Sonic registries (0x6468 already, 0xD49E after the rotation), its location (Ilan's Sonic wallet) should
be written into the key inventory. Low-stakes on testnet, but "document where every admin key lives" is the
mainnet secure-signing-gate discipline, so record it rather than let an admin key stay location-undocumented.

## CCID test-wallet Z common.kyc backfill (Sepolia) - TESTNET-ONLY, TEMPORARY - CLOSED as issue+revoke pair (2026-10-07)

TESTNET manual credential backfill, Ilan-authorized testnet-only (recorded on the coordination board
CCID-SESSION-STATE.md line 150; Z classed internal never-KYC'd test EOA in CCID-Z-BACKFILL-STAGING.md
line 13, RUNBOOK 3.C non-allowlisted). Issued by CCID as the Canton EVM-refusal test recovery step; it is
TEMPORARY and will be revoked at Canton delivery cleanup. NOT a real KYC record and NOT a mainnet pattern
(mainnet uses real KYC, never hand-issued, per policy_manual_credential_backfill_testnet_only).

Subject Z = `0xBCB0acB94a63c15e91320Be1D15026b151e6BD96`. Sepolia IdentityRegistry
`0x89EC36234fF2D75C306f27913e7421791fC888c7`, CredentialRegistry
`0xDc5Acd767633E7B29C7f317DBB57c52F597bCe6F` (address-level validator 0xdAbf8046 reads these).
ISSUE txs (both status 1, signer `from` = 0x3055, the testnet issuer key):
- registerIdentity 0x386e37e8961786779f016fca41610e30b22eec1c02241562d451d9a64a7bb51c (block 11864174)
  https://sepolia.etherscan.io/tx/0x386e37e8961786779f016fca41610e30b22eec1c02241562d451d9a64a7bb51c
- registerCredential 0xe6d8c596d253e5470be333f88baf0031269f739e506c489068f33bf2c05cd410 (block 11864175)
  https://sepolia.etherscan.io/tx/0xe6d8c596d253e5470be333f88baf0031269f739e506c489068f33bf2c05cd410

THIS GATE's OWN independent live reads (taken 2026-10-07, after correcting my first-pass wrong signatures:
getCredentialTypes/validate are keyed by the CCID bytes32, NOT the address; validate(address,bytes) lives
on the separate validator, not these registries - my initial address-keyed calls reverted and that was a
signature mismatch, NOT credential absence):
- getIdentity(Z) = `0x60b6e01e41b51d34c6b0d20f8a708c412c96992f6750a1cca7285fa46120f3eb`, and this EQUALS
  ccidFor(Z) reproduced independently as keccak256(abi.encodePacked("ob.ccid.v1", Z)). Identity bound.
- getCredentialTypes(ccid) = [`0x455af80b0708d48777181ab288cb8aad0befe0f51a15ddafdd875abdf26278c6`] = common.kyc
  (keccak256("common.kyc"), reproduced; my earlier 0x8617e5e5 was a hex-as-string tooling slip, corrected).
- validate(ccid, common.kyc, 0x) = TRUE. Credential live NOW, so cleanup has not yet run (issue->cleanup
  window, consistent with CCID).

REVOKE leg (cleanup) VERIFIED by this gate 2026-10-07, both txs status 1, signer `from` = 0x3055:
- removeCredential 0x522ad88a0a1095fdb588d8dde3eb29cbcc45e994b4b879d81241f4a466a3eb0a (block 11864233)
  https://sepolia.etherscan.io/tx/0x522ad88a0a1095fdb588d8dde3eb29cbcc45e994b4b879d81241f4a466a3eb0a
- removeIdentity   0xeba93863f1a0e88c7e49a0e8307f1947ccaaceebe3fc81c74260a0aba277489e (block 11864234)
  https://sepolia.etherscan.io/tx/0xeba93863f1a0e88c7e49a0e8307f1947ccaaceebe3fc81c74260a0aba277489e

CLOSE ANCHORED ON THE CREDENTIAL-LIFECYCLE EVENTS, read from the tx receipts (not a range getLogs scan),
NOT on validate() alone. CredentialRegistered (topic 0x7e9980c3..., ccid 0x60b6e01e, type common.kyc
0x455af80b) at block 11864175, paired with CredentialRemoved (topic 0x9094e025, same ccid, same type) at
block 11864233 on CredentialRegistry 0xDc5Acd76. Plus this gate's state reads: getIdentity(Z)=0x0,
getCredentialTypes(ccid)=[].
WHY NOT validate() ALONE (methodological, per S8, adopt as a general rule): validate(Z) reads false both
when the credential is REVOKED and when the credential is still present but the wallet is DE-ALLOWLISTED.
Canton disallowed Z at about the same moment CCID revoked, so for a window validate(Z)=false was consistent
with the false attestation still being live. A reading consistent with two materially different states is
not a verification; the distinguishing signal is the CredentialRemoved event / getCredentialTypes=[], which
is what this close rests on. See feedback_validate_false_conflates_revoke_and_deallowlist.
TWO-SOURCED, not relayed: S8 independently found the removeIdentity hash from the event log before CCID sent
it, and the two are identical. Both legs corroborated across two sessions.
SIGNING PROVENANCE (per S8): both issue and revoke were bun+ethers one-offs with base64+sha256
verify-before-execute and an in-script signer guard, NOT the standard issuer-service webhook path, because
cast is absent on the keeper box.
PAIR CLOSED (issue+revoke), credential NOT LIVE, admission fully reverted to pre-test.

RESIDUAL, OPEN pending Ilan's ruling (admission reverted, BALANCE not): Z holds 1.0 MBT (the delivered test
unit) on Sepolia MBT `0x044951AB857C7e23b5570D4ca11466ACACF861A8` (this gate read balanceOf(Z)=1e18,
totalSupply=1066.5). Z is de-admitted, so it cannot move it (S8 confirmed a transfer from Z reverts at the
AllowPolicy). Conservation holds: Sepolia 1066.5 + Canton 1.0 = 1067.5 (S8's cross-chain accounting; Canton
leg not this gate's chain). S8 recommends leaving it and recording it in RUNBOOK §3.C rather than spending
another production compliance touch to sweep it; Ilan has not ruled. Recorded OPEN so a future reader
finding an immovable 1.0 MBT on a de-admitted wallet does not open an investigation. Authoritative doc home:
PR #502 (head cf4499b67a80d0dbb1767e46444cedaafe75380a) adds it as a line on the RUNBOOK 3.C row for Z; S8
confirmed the transfer-from-Z revert is PolicyRunRejected at the AllowPolicy. Leave-and-record is S8's and
Canton's recommendation; if Ilan elects to sweep, the row is updated, not removed.

## OB Agent Network - AGENTS-15 Phase 2C step-12 licensing-gate (Polygon Amoy 80002) - GATE PASS (2026-10-11)

Routed by Agent Session 2B (packet AGENTS-15-STEP-12-LICENSING-GATE.md, branch phase2c/v3-wallet-spec-draft
HEAD 53a7839). This gate's OWN reads on Amoy (keyed Alchemy, chain-id 80002 confirmed). PASS, nothing
mismatched.

ARTIFACTS (code present on Amoy, identity read by this gate; 2B reports Sourcify exact_match on Sepolia+Amoy):
OBAgentWalletFactoryV3 0xea4D351F884A33F34508F24cA6A0ad009145ff4E (20121B), OBBorrowHelperV3
0xB6F376F14cC84611aCD013F7F9D2B2BD1Da17AC2 (3213B, behaviorally confirmed as the helper in both the credential
and the attack path), TestAttackCollateral 0x07727CF00bdB972e62b9836B90A812d473E572F0 (symbol "TAC", test
fixture), test v3 wallet 0xC5544446D5a50EA7e9512cE1C3C986A792CF2939 (15543B). CredentialRegistry
0x94d6257c2f696019B0Bbf22b28b853689076D898 = "CredentialRegistry 1.0.0".

(1) CREDENTIAL ISSUANCE tx 0x5843e501d55ebd06a39a7414a3d283324171d8ae929a1603ba32e5a9771c6034: status 1,
from 0xFc99, to CredentialRegistry 0x94d6, block 49784018. CredentialRegistered (topic 0x7e9980c3...) for
agent ccid 0xc27694aa21e2047e4de78a18235668799aebc941b1355321e6706ed5dcf248bd, credentialType
0xb9b4a116d18adfaf0fb3fc04648e57755dd20a7dc8329bbb1470d81aacda100b; credentialData embeds helper 0xB6F3 +
caps 10e18 / 25e18 + window 3600s (read by this gate from both the event and the getCredential return).
STATE live: getCredentialTypes(ccid) includes 0xb9b4a116 (among 3 types the agent holds) and validate(ccid,
0xb9b4a116, 0x) = TRUE. The byte-for-byte identity of credentialData to CCID's canonical cast abi-encode is
per CCID build-step-5 (their lane); this gate read the on-chain blob and confirms it embeds the expected
helper + caps, but did not independently re-encode it.

(2) c.iii ADVERSARIAL ATTACK-ATTEMPT tx 0x3cb9663e27146ffecbfe314b57d0ab87bc2068f79912414edb69b235c17835a6:
status 0 FAILED (as required), from agent-deployer 0xd63352Cc, to test wallet 0xC554, block 49784222. This
IS the on-chain c.iii evidence, and it is a GENUINE GUARD-BLOCK, not OOG/phantom (verified by trace + state,
not status alone):
  - Mechanism (the amount-cap-bypass vector, fb_amount_cap_calldata_arg): wallet.execute(helper, 0,
    "helper.borrow", ...) signed by the agent declared op amount 1 wei, so the wallet granted a SCOPED
    allowance of exactly 1 (ScopedAllowanceGranted). The helper's forwarded arbitrary calldata then tried
    transferFrom(wallet -> attacker 0x..BAd0, 500e18), far exceeding both the 1-wei scoped allowance and the
    10/25e18 credential caps.
  - It REVERTED: innermost custom error 0x13be252b (the outflow/allowance bind), bubbling 0x375bdfbc (helper)
    -> 0x72866feb (wallet). DETERMINISTIC custom-error revert, NOT OOG (gasUsed 405288 < 500000 limit; a
    clean error bubble, not gas exhaustion).
  - AUTHORITATIVE no-movement (this gate's live balance reads): TAC.balanceOf wallet 0xC554 = 1000e18 (full,
    unchanged), attacker 0xd633 = 0, helper 0xB6F3 = 0, attacker target 0x..BAd0 got nothing (atomic
    rollback). So the attack moved zero. The outflow bind holds: a forwarded call cannot exceed the scoped
    per-op allowance.
  - TRACE-LEVEL RESOLUTION (2026-10-11): Legal Review + CCID both confirmed receipt+payload but could NOT
    trace which-gate-fired on public Amoy nodes (publicnode / rpc-amoy.polygon.technology: "historical state
    not available"). This gate's keyed Alchemy endpoint HAS the archive: confirmed available at block
    49784222 (cast code returns bytecode, historical balanceOf serves 1000e18 - not a latest-state fallback,
    so the cast run replay is faithful per fb_cast_run_resim_not). Named revert frames, innermost out:
    (1) TAC.transferFrom(wallet -> 0x..BAd0, 500e18) reverts InsufficientAllowance() [0x13be252b, cast sig
    confirmed] - the 1-wei scoped allowance enforced by the token; (2) OBBorrowHelperV3 wraps it as
    VaultCallFailed(address,bytes) [0x375bdfbc, source line 78 + cast sig]; (3) the wallet wraps it as
    InnerCallFailed(bytes) [0x72866feb] - caveat now CLOSED, SOURCE-CONFIRMED by this gate: the decl is in
    the closed-core repo ob-agent-network-core at src/OBAgentWalletV3.sol:102 (error InnerCallFailed(bytes
    returnData)) with the revert site at line 359 (if (!success) revert InnerCallFailed(returnData) inside
    execute()); cast sig of the declared signature = 0x72866feb (confirmed DIRECTLY at the deployed-wallet commit fc99c98
    via git show fc99c98:src/OBAgentWalletV3.sol lines 102/359; fc99c98 is an ancestor of my working tip
    085a28e, so the decl is stable from the deploy commit to now). (3b) STRONGER (a)-not-(b): the same file declares
    OutflowExceedsCap(uint256,uint256) and EconomicWindowExceeded(...) - the gate-14 / sec 4.11(b) errors -
    at lines 100-101, and NEITHER appears in the revert chain, so gate-14 provably did not fire; only the
    scoped-allowance path (4.11a) did. DETERMINATION: the mechanism is sec 4.11(a)
    SCOPED-ALLOWANCE (VaultCallFailed path), NOT sec 4.11(b) gate-14 OutflowExceedsCap. Gate-14 did NOT fire,
    and that is the point: the attack DECLARED a 1-wei op (which passes the cap gate), then forwarded 500e18;
    the scoped allowance (= declared amount) is the load-bearing defense, exactly fb_amount_cap_calldata_arg /
    fb_scoped_per_op_allowance. This resolves the (a)-vs-(b) inference that Legal + CCID flagged they could
    not distinguish; two-sourced at receipt+payload by them, trace-level by this gate.

HALT/flags: none. External-language unblock is Matter 21 R2's, gated additionally on counsel P.5 (not this
gate). Deployment identity on Sepolia twin not re-read here (gate chain is Amoy); resolve by chain +
typeAndVersion if a Sepolia claim is later made.

## OB Agent Network - AGENTS-15 Phase 2C step-13 TW3-Amoy migration ceremony (Amoy 80002) - V&L legs ACTIVE, PARAMETER-BLOCKED (2026-10-11)

Ilan directed "go for step 13" 2026-10-11. V&L runs the live-registry-state reads (the ceremony gate); CCID
holds the CREATE2 handoff; signers: 0xFc99 for registry writes, agent-deployer for factory.createWallet,
TW3's own key for user-side fund-move. HALT-and-flag on any tripwire; never re-point on a CREATE2 mismatch.
SCOPE CAVEAT recorded UP FRONT (per S8 2026-10-11, do not let scoped-green read as clean): the agent-network
stack has NOT been V12-scanned (the 18+5-finding V12 batch 1ab7833 on the RWA stack, 2026-10-05, never
pointed at this surface). AGENTS-15 sec 11 carries "scoped evidence green on tested shapes, static-analysis
surface UNMEASURED". V&L records its OWN live observations as independent evidence at each step ALONGSIDE
this caveat; a V&L PASS on a step is scoped to what that step's live read covers, not a clean-bill of the stack.
MY FOUR LEGS (sec 6.3), fixtures pinned: IdentityRegistry 0xf53c87CB "IdentityRegistry 1.0.0", CredentialRegistry
0x94d6 "CredentialRegistry 1.0.0".
- Step 1 (pre): getIdentity(TW3 principal)=ccid; getCredentialTypes(ccid) shows v1+v2 PRESENT, NO stale v3; no v3 wallet artifact.
- Step 3 (post-factory-deploy): assert deployed wallet == the address in the WalletCreated EVENT (read from the event, NOT re-derived from CCID's inputs - independence). ABORT on mismatch. This is where wallet BYTECODE-provenance to core commit fc99c98 closes (the gap flagged at step 12; step 12 only source-confirmed, did not bytecode-match the CREATE2 wallet).
- Step 8 (post-v3-issuance): v3 credential PRESENT on ccid, v1+v2 STILL present (not yet revoked), new wallet admission reads v3.
- Step 12 (post-decommission): v1+v2 ABSENT on ccid, v3 PRESENT, old v2 wallet DENIES admission.
DERIVATIONS VALIDATED 2026-10-11 against step-12 known-good vectors (done NOW, pre-ceremony, to catch any
encoding error before step 1): ccidOf = keccak256(abi.encodePacked("ob.ccid.v1", addr)) reproduced
ccidOf(0xd63352Cc) = 0xc27694aa... (matches the step-12 getIdentity read); delegation typeId vN =
keccak256(abi.encodePacked("ob.agent_delegation.vN", uint256(agentId))) reproduced delegationTypeIdV3(1) =
0xb9b4a116... (matches the step-12 known-good, confirming agentId is uint256-encoded in the packed hash).
Both formulas now pinned; step-1 is turnkey on substituting TW3's principal + agentId.

STEP-1 LIVE READ DONE 2026-10-11 (params relayed by 2B from Ilan's on-chain read of v2 wallet 0xF6af3A53:
principal = agent = deployer = 0xd63352Cc9f96362D3D080B7c519e7f64f97e446a, agentId = 1). RESULT: FLAG, NOT a
clean pre-state.
- DERIVATION CROSS-CHECK PASS (independence held): my cold ccidOf(0xd63352) = 0xc27694aa... == on-chain
  getIdentity == 2B's value; my cold delegation typeIds v1 0x57fe1f0a / v2 0xa4b0fb96 / v3 0xb9b4a116 ==
  exactly the three types getCredentialTypes(ccid) returns. No divergence across me / chain / 2B.
- COLLISION confirmed by my own read: factory.walletAddressForAgent(0xd63352, 1, 0xd63352) =
  0xC5544446D5a50EA7e9512cE1C3C986A792CF2939 = the step-12 TEST wallet. TW3's production principal+agentId are
  identical to the step-12 fixtures, so CREATE2 landed the step-12 test wallet on TW3's production v3 address.
- PRECONDITION VIOLATED ("no stale v3 artifacts"): v3 credential 0xb9b4a116 is LIVE (validate=true) on TW3's
  production ccid - it is the step-12 TEST credential (TAC-pointing, caps 10/25e18), not a clean slate. And the
  production v3 wallet 0xC554 already holds 1000e18 TAC (the step-12 test load) and carries the c.iii attack tx
  0x3cb9663e in its history. v1 0x57fe1f0a + v2 0xa4b0fb96 present = TW3's expected historical setup (fine).
- ROOT CAUSE: the step-12 licensing-gate test used TW3's REAL production (principal, agentId) as its fixture,
  so the test credential + test wallet occupy TW3's production slots. 2B frames this as "scheduling
  convenience, not scope creep"; this gate does NOT accept that framing - a live test credential and a
  test-laden wallet on a production identity is a stale-artifact tripwire, which is exactly what step-1 exists
  to catch.
GATE POSTURE: step-1 is NOT a clean pass; HOLD before step-3 (createWallet) / step-7 (issuance) until Ilan
decides the path. Options (2B flagged the step-7 credential-replacement decision to Ilan): overwrite /
revoke-then-reissue the live test v3 cred, OR a DIFFERENT agentId (which yields a fresh v3 typeId AND a clean
wallet address, avoiding inheriting the 1000 TAC + attack history - the only path that gives TW3 a clean
production wallet). The disposition of the 1000 TAC and the attack-tx history in 0xC554 is part of the
decision. This gate flags; Ilan decides; I re-read after the chosen path executes.

STEP-1 RE-READ, PATH 1 (agentId=2), 2026-10-11: CLEAN PRE-STATE CONFIRMED. Ilan chose Path 1 (new agentId=2
for a fresh wallet + fresh v3 slot). This gate re-derived independently and cross-checked (no divergence):
my cold typeIdV3(2) = 0x28812a44765c29bfd301cb3e49bdca6b8509b4fe997d40bf7a491d2c8d6e9f51 == CCID's; my
walletAddressForAgent(0xd63352, 2, 0xd63352) = 0x6D297af08432462b161023ADCe813E1Ae1a24aFA == CCID's, and
cast code there = EMPTY (fresh, no wallet yet). Pre-state: validate(ccid, v3(2)) = FALSE and typeIdV3(2) is
NOT in getCredentialTypes(ccid) -> the NEW v3 slot is clean; v1 0x57fe1f0a + v2 0xa4b0fb96 validate TRUE
(TW3's historical setup present, expected). So "no stale v3 artifacts" HOLDS for the agentId=2 production
target; step-1 PASSES for Path 1. The orphan agentId=1 artifacts (v3(1) 0xb9b4a116 still in getCredentialTypes,
wallet 0xC554 with 1000 TAC + the c.iii attack tx) remain as the SEPARATE secondary flag - 2B surfaced
3 options to Ilan (leave / remove TAC cred only / full cleanup incl TAC recovery); non-blocking for Path 1,
sequences separately. My flag record stands either way.

STEP-13 POST-CEREMONY V&L VERIFICATION (2026-10-12, this gate's own live Amoy reads; 2B's packet 73f00a2
reports the ceremony complete and attributes gates 3/8/12 to V&L - this is V&L actually running them NOW, not
a prior confirmation). All PASS except the bytecode leg (compile in progress):
- GATE 8 (post-issuance): validate(ccid 0xc27694aa, v3(2) 0x28812a44) = TRUE. v3 credential live.
- GATE 12 (post-decommission): validate v1(1) 0x57fe1f0a = FALSE and v2(1) 0xa4b0fb96 = FALSE (removed);
  getCredentialTypes(ccid) = [v3(2) 0x28812a44, orphan v3(1) 0xb9b4a116]. v1/v2 delegation creds gone, v3 live.
  The orphan v3(1) (step-12 test cred) still present = the known secondary flag, Ilan cleanup pending.
- GATE 3 ADDRESS leg: WalletCreated event (tx 0x153d65714672bb8f6194dbecd91be66c4a00afc4a45e9204d1cedfb91744438a,
  block 49987034) topic1 = deployed wallet 0x6D297af0... == this gate's independent walletAddressForAgent
  derivation. deployed == predicted, read FROM THE EVENT (not re-derived). agentId topic = 2.
- MBT MIGRATION: MBT (Amoy 0x71A8e443f223cFe59498968758dB49F2D114dCE2, symbol "MBT") balanceOf(v3 0x6D29) =
  30e18, balanceOf(v2 0xF6af3A53) = 0. The 30 MBT moved v2 -> v3 (confirmed by the step-6 Transfer event too).
- V2 WALLET DO-NOT-DE-ADMIT (reconciliation of the original step-12 "old v2 wallet denies admission" spec,
  which the packet's scope decision SUPERSEDES): 0xF6af3A53's OWN ccid 0xc6e30ac8... still has common.kyc
  (validate TRUE) - its token-holder admission deliberately persists (open borrow collateral return +
  Legal-matter-9 grant). What the decommission removed is the v1/v2 DELEGATION creds on the PRINCIPAL ccid
  0xc27694aa, NOT the v2 wallet address's holder admission. So "old v2 wallet denies admission" is NOT the
  on-chain reality and should not be asserted; the correct statement is "v1/v2 delegation authority removed;
  v2 wallet address stays admitted by design."
CORRECTIONS to 2B packet (verify-the-peer, not reconcile-to-it):
  (a) OBSERVATION #2 method name: the v3 wallet identity accessor is TYPE_AND_VERSION() (ALL-CAPS public
      constant, source line 78, selector 0xcc4c84b6), NOT typeAndVersion() - which REVERTS. 2B's VALUE is
      right, the method name is wrong. ESCALATED CORRECTION (2026-10-12): 2B then over-corrected to "NO live
      self-identity getter exists on the wallet" and S8 "confirmed independently" - that is FALSE, a
      false-absence from probing the wrong name. 2B/S8 probed typeAndVersion/version/VERSION/contractName/
      name/walletVersion/implementationVersion (all revert); none is the actual accessor. This gate read
      TYPE_AND_VERSION() = "OBAgentWalletV3 3.0.0" LIVE on the production wallet 0x6D29 AND the step-12 twin
      0xC554, with lowercase typeAndVersion() reverting as the negative control. The getter EXISTS; the
      absence-claim is a probed-the-wrong-accessor error (grep!=absence class). Their Finding #6 lesson
      (self-reported string vs bytecode-provenance are different claim classes) stands as a lesson, but its
      factual premise "no getter exists" must be corrected to "the getter is TYPE_AND_VERSION(), camelCase
      variants revert". Relayed to 2B.
  (b) LINE 61 "gate-3 bytecode-provenance to fc99c98 confirmed via WalletCreated event": the WalletCreated
      event proves deployed == predicted ADDRESS, which is NOT bytecode-provenance. Address-match != bytecode
      match. V&L's actual bytecode-provenance (compile OBAgentWalletV3@fc99c98, compare runtime to 0x6D29) is
      RUNNING NOW; result PENDING, recorded here as pending, not confirmed.
GATE-3 BYTECODE PROVENANCE: CONFIRMED (2026-10-12). The deployed v3 wallet 0x6D29 runtime == OBAgentWalletV3
@fc99c98 compiled (viaIR, optimizer 200, solc 0.8.24, cancun). Method: source is byte-identical fc99c98 ==
085a28e (git diff empty), so the prebuilt core artifact (085a28e) is the fc99c98 build; compared its
deployedBytecode to the on-chain runtime. EXACT LENGTH MATCH (15540 hex). 287 differing bytes in 18 clusters,
ALL attributable to per-instance immutables, ZERO code-logic divergence:
- 20-byte windows (x8): owner/principal 0xd63352 (x3 incl 7702-style self-auth sites), CredentialRegistry
  0x94d6 (x2), IdentityRegistry 0xf53c (x2), the wallet's OWN address 0x6d297af0 (x1) - all per-wallet
  immutables a factory CREATE2 deploy bakes in.
- 23-byte window "OB Agent Network Wal..." + 32-byte windows: OpenZeppelin EIP-712 immutables (ShortString
  _name, _hashedName/_hashedVersion, cached domain separator / cachedThis).
- scattered 1-3 byte diffs: the nonzero bytes of mostly-zero immutables (e.g. the cached chainId 80002).
A clean body-match modulo immutables is SELF-VALIDATING on compiler settings: if solc/profile/optimizer
differed, code bytes would diverge throughout, not only at immutable slots. So this closes the bytecode leg
that step-12 left as source-only and that 2B's packet had conflated with the WalletCreated-event address
match. Gate-3 now has BOTH legs: deployed==predicted ADDRESS (WalletCreated event) AND runtime==source
BYTECODE (this compare). (The background cold fc99c98 worktree build FAILED exit 2 on a
worktree/symlink issue - moot: the evidence here is the prebuilt byte-identical-source artifact compare,
which stands independently; I did not rely on the cold build.)
=== STEP-13 V&L VERDICT: PASS on all four legs (1 pre-state Path-1 clean, 3 address+bytecode, 8 post-issuance,
12 post-decommission). Migration independently confirmed end-to-end. Non-blocking open: orphan agentId=1 v3(1)
test artifacts (Ilan cleanup choice). Scope: Amoy only; agent-net stack still not V12-scanned (sec-11 caveat
stands); V&L PASS is scoped to these live reads, not a clean-bill of the stack. ===

## BNB Testnet (chain 97) RWA stack - GATE PASS on deployed artifacts, with public-exposure conditions (2026-10-12)

Routed by the BnB RWA Bridge channel (deploy-result SESSION-COORDINATION/bnb-deploy-result-2026-10-12.md),
commit bead24d, FOUNDRY_PROFILE=ci (via_ir), deployer/owner 0xFc99, nothing public pending this PASS. This
gate's OWN reads on BNB chain 97 (keyed Alchemy, chain-id confirmed 97).

IDENTITY + TWINS (read on BNB; twins resolved by chain + typeAndVersion):
- RWATokenCCT (MBT) 0x3BC55A7Da74e027b6F363E12e6F4Cfd0282e43f3: name "Miami Beach Tower"/MBT/18, totalSupply
  0, owner+getCCIPAdmin 0xFc99, sanctionsRegistry()=0x8c57 (immutable). No typeAndVersion (RWATokenCCT has
  none; identity via bytecode provenance below).
- BurnMintTokenPool 0x2bC9...9265 = "BurnMintTokenPool 1.5.1". PolicyEngine 0x22e3 "PolicyEngine 1.0.0",
  AllowPolicy 0x045A "AllowPolicy 1.0.0", PausePolicy 0x060F "PausePolicy 1.0.0", GlobalSanctionsPolicy proxy
  0x8c57 + impl 0x4dE6 both "GlobalSanctionsPolicy 1.0.0".
- TWIN 0x43CC10a0bbEb70d8EFCb45714e0e0F4C49500c75: on BNB = PausePolicy IMPL ("PausePolicy 1.0.0", 4533B);
  same address is Sonic TestUSDC and (per BnB) Plume MBT - a 3-way cross-chain CREATE collision. Resolved by
  reading BNB.
- TWIN 0x5fe9d900945b60B3BEcEDe4382BEd2653EC99D40: on BNB = the MockV3Aggregator PoR; same address is an Amoy
  AllowPolicy. Resolved by reading BNB.

BYTECODE PROVENANCE (MBT) CONFIRMED: on-chain 0x3BC5 runtime == the OB-bnb build artifact
(out/RWATokenCCT.sol, viaIR/optimizer-200/solc-0.8.24/cancun = the ci profile), EXACT length 18004, 104
differing bytes in 7 clusters, ALL per-deployment immutables and zero code divergence: CCIP chainSelector
uint64 b8159170038f96fb (x4 sites), sanctionsRegistry 0x8c57 (x2), assetId bytes32 (x1) = 104 bytes exactly.
No metadata-hash diff -> the artifact IS the deploy build. Source @bead24d (src/RWATokenCCT.sol, commit
confirmed in the main repo) carries the enforcement below, so the deployed bytecode carries it.
NOTE (ties to todo_s8_gates_mainnet_testbed_v12): this is a POST-1ab7833 build - its bytecode differs from
the six earlier MBTs (V12 source fixes AND via_ir profile). It is the FIRST OB deployment with the V12 burn
guard LIVE (proven below).

COMPLIANCE ENFORCEMENT:
- BURN GUARD (V12 fix) LIVE + DISCRIMINATING, proven by this gate keyless: from the authorised pool,
  burn(0xdEaD,0) reverts 0x584ca52c = BurnTargetNotCaller(0xdEaD, pool) (my cast sig matches), while
  burn(pool,0) returns 0x (positive control). Owner path reverts 0x82b42900 Unauthorized (no mint/burn
  authority - the NON-discriminating control, not the guard). So an authorised burner cannot burn an
  arbitrary target, only its own balance. THREE-SOURCED (this gate + BnB channel + Execution Staging all
  reproduced the self-only guard). ES first framed it as "authorised-burn reverts balance = discriminates";
  superseded - this gate decoded that revert 0xf4d678b8 = InsufficientBalance() (pool has 0 balance at ts0),
  which only shows the pool REACHED the balance check (is authorised), NOT the self-only target guard; the
  guard proof is BurnTargetNotCaller 0x584ca52c on burn(arbitrary,0). ES corrected to the same. Reproduced:
  burn(0xdEaD,0)->0x584ca52c, burn(pool,0)->0x, burn(pool,1)->0xf4d678b8.
- SANCTIONS enforced IN-FUNCTION (not via the engine chain, by design): RWATokenCCT @bead24d
  _requireNotSanctioned(account) { if(account!=0 && sanctionsRegistry.isSanctioned(account)) revert
  AddressSanctioned; } called on from AND to in _transfer (src 480-482), on the SPENDER in transferFrom (251,
  the V12 spender-screening), and on the recipient in mint (272), via the bound immutable 0x8c57. The engine
  policy chain getPolicies(token, transfer/transferFrom/mint) = [AllowPolicy 0x045A, PausePolicy 0x060F] -
  sanctions correctly NOT in the engine (it is in-function), allow+pause are. A LIVE state-override
  reject-proof of the in-function sanctions check is the STANDING permit/reject item, deferred (token is
  totalSupply 0, no allowlisted holders to isolate the sanctions revert from the allow revert without
  multi-contract overrides; pre-public). Enforcement is established by named-reverting-function + bytecode
  provenance (deployed==source), and the burn-guard live proof shows this bytecode's guards do fire.
- CCIP wiring: TAR 0xF8f2A446...8f2B getPool(token)=pool 0x2bC9, pool.getToken()=MBT 0x3BC5, token CCIP admin
  0xFc99. Consistent.

FLAGS (conditions for PUBLIC exposure; none makes a deployed ADDRESS wrong):
1. SANCTIONS vacuous + deployer-admin: 0x8c57 blocklist EMPTY (isSanctioned probe false - normal initial
   state, nobody slips through now), registry()=0x0 (standalone), owner+sanctionsAdmin BOTH = deployer
   0xFc99. Same class as the Sonic flag B. Rotate sanctionsAdmin to a dedicated key before/with public use.
   RESUME HOOK: Execution Staging stages setSanctionsAdmin(0x8c57) to Ilan's OB-222 key and routes it to this
   gate; on land I assert sanctionsAdmin(0x8c57) == the OB-222 address SPECIFICALLY (not just changed, != 0x0,
   != 0xFc99) - per the Sonic rotation lesson that setSanctionsAdmin has no zero/validity guard.
   >> RESOLVED 2026-10-12: Execution Staging executed it. Independent live reads on chain 97 (not ES's value):
      sanctionsAdmin(0x8c57) = 0xCf309940276b1e1396AED22aC4e1E7D2E3dd27d6; owner() unchanged 0xFc99; tx
      0xbad0d23d19e4af6274bad1af0bc894de804c34750c3af0d0a160ad6a0ba074f4 status 1, to 0x8c57, from owner 0xFc99,
      selector 0x8fa4e560 = setSanctionsAdmin(address), arg cf309940...27d6. Mechanic of condition 1 satisfied
      (off deployer, != 0x0, owner unchanged).
      FINDING (role concentration, relayed to ES): 0xCf3099 is NOT a fresh dedicated key - it is the long-standing
      "RWA-KYC-Approved" investor/driver demo EOA (allowlisted+funded on all 5 chains, nonce 12, bridges/borrows in
      demos; RUNBOOK.md:451, QA/UAT docs, Stellar demo wallet) and already issuerAdmin+sanctionsAdmin on other OB
      chains (06-contracts.md:927,1025 = the "OB-222 convention"). So off-the-deployer + convention-consistent, but
      a HOT MULTI-ROLE EOA (investor actor + issuerAdmin + sanctionsAdmin on one key; per ES also the forced
      signer/recipient of the pending position-96 recovery), not a dedicated cold admin.
      Acceptable on testnet (empty blocklist, mocks); HARD LINE for the mainnet testbed gate: mainnet must use a
      dedicated cold sanctions-admin distinct from any investor/actor key. 06-contracts.md still reads "pending"
      (now stale, non-blocking).

PR #510 diff-vs-ledger (2026-10-12, Release Manager routed, head 83a78d81dd9fcc0182c65d73711bdb05ddb4eeac):
read the PR head directly. Files RUNBOOK.md / deployments.json / docs/public/06-contracts.md (new section 6.22).
All 15 OB-deployed addresses in the PR match this ledger line-for-line and each label-address pairing is correct.
TWO addresses in the PR were OUTSIDE the original gate scope (external Chainlink infra, not OB contracts): CCIP
Router 0xE1053aE1857476f36A3C62580FF9b016E8EE8F6f and LINK 0x84b9B910527Ad5C03A9Ca831909E21e236EA7b06. Live-read
on chain 97 to avoid passing from memory: Router typeAndVersion = "Router 1.2.0"; LINK = "ChainLink Token"/LINK/18.
Both match the doc's labels - now in scope. 0x000...dEaD in the diff is the burn-guard probe sink, not a contract.
Doc text satisfies all three public-exposure conditions (PoR labeled MOCK; twins 0x8c57/0x22e34A3a/0x5fe9 resolved
per-chain; admin re-point off 0xFc99 noted pending). Confirmed to Release Manager: clear to merge from V&L side;
public push remains Ilan's.
2. PoR = MOCK: 0x5fe9 is a MockV3Aggregator (description reads literally "v0.8/tests/MockV3Aggregator.sol",
   latestAnswer a static 1e24). NEVER a real Proof-of-Reserve; must not be presented externally as PoR
   (honest-display; pol_never_claim_jurisdiction neighbour; dec_por_feed_every_rwa needs a REAL feed).
3. TWINS (above) must be resolved by chain in any doc; do not let 0x43CC / 0x5fe9 be read as their twins.
Recorded in CONTRACT-VERIFICATION-AUDIT-LEDGER.md (local, uncommitted; public push gated on Ilan).

## Not live (documented for completeness)

## Not live (documented for completeness)

- **Base Sepolia (84532):** NOT a supported network (`SUPPORTED_CHAINS` excludes it). Early test
  deploys exist but several addresses have no code (deterministic-address artifacts). Nothing live
  to verify; revisit only if Base is added to the dApp.

(Astar Shibuya was previously listed here as DEPRECATED; it was re-activated 2026-06-19 and now has
its own verified section above.)

## BNB chain-97 CCID Phase-0 registry stack (OB-218) - V&L GATE: PASS (deployed shadow stack), 2026-10-12

Routed by CCID channel, Ilan-signed deploy from 0xFc99, receipts broadcast/DeployCCIDRegistries.s.sol/97/run-latest.json
(18 txs, blocks 0x821d5e8..0x821d635). ALL reads taken independently on chain 97 (keyed Alchemy), nothing inherited
from CCID's read-backs. Scripts: scratchpad ccid_verify.sh + ccid_verify2.sh.

Addresses (proxy / impl), typeAndVersion read live, all codesize 130 (ERC1967 proxies):
- RegistryAdminEngine (PolicyEngine 1.0.0)    0xA56efb5A85A2C9c2e07478Ce257CEdEcEE9cA7f6 / 0x670c3a58435cc13e9edA059F4093F31CedEEF7F3
- IssuerGatePolicy (OnlyAuthorizedSenderPolicy 1.0.0) 0x2aedDb48a3b6028244ed31C0fdDEB2B72Fce3bE1 / 0x3520f45AfBbEc76484bD146Cb260C03D0Faec5F5
- IdentityRegistry 1.0.0                       0x5D273AAE0547FD33c0C5247487a160cd26f38FE4 / 0x49f1aa7C252183E79D4944c54698a335Ed65C150
- CredentialRegistry 1.0.0                     0x358f29328B1EA25B04B11628d659b49320a67c07 / 0x73b6517F74835436051d37B34eB663d879759536
- ValidatorPolicy (CredentialRegistryIdentityValidatorPolicy 1.0.0) 0x0AE06A1AA4872FB2649A3CF9a0dF942532c47fB3 / 0xc38E9d1290f4448b32Cef2C4beF8570AB4D82A14

WHAT PASSED (independent):
1. Identity + TWINS resolved ONLY by chain-97 + typeAndVersion + exact-bytecode (never by address/grep). These 5
   addresses are HEAVY cross-chain twins (deterministic nonce reuse from deployer 0xFc99); the role each holds
   ELSEWHERE is a different-chain coincidence and does NOT bear on chain 97. Twin map (observed by S8/BnB/CCID in
   origin/main deployments.json + 06-contracts.md, positive control passed; NOT re-read live by me and NOT needed
   for this gate):
     - IssuerGate 0x2aedDb48 ALSO booked as AllowPolicy AND MBT RWA-token (double-booked) - the most dangerous twin.
     - RAE 0xA56efb5A ALSO CREBridgeConsumer v2; RAE impl 0x670c3a58 ALSO Sonic WrappedMBT v2 (permanently paused).
     - IdReg 0x5D273AAE ALSO amoy.lockVault / LockVault(CRE source) / a lowercase PausePolicy entry. (A prior
       "== Minato token engine" attribution was CCID's, from a script _cfg HARDCODE not a live read, and CCID has
       WITHDRAWN it - do NOT carry it as an observed collision.)
     - IdReg impl 0x49f1aa7C ALSO Base BurnMintTokenPool AND the superseded Sonic Nova LendingVault v2 that appears
       in the standing LendingVault block above - confirmed DIFFERENT contract here by chain-97 tAV + exact bytecode,
       so the LendingVault block does NOT touch this chain-97 address and no LendingVault code is in the CCID stack.
     - CredReg 0x358f2932 / impl 0x73b6517F: no twin found (bounded negative, those two files only).
   METHOD NOTE (S8, load-bearing): 103 of 435 registry addresses are multi-role; the registry is UNSAFE to key by
   address and does not yet carry chainId per row (S8 owns that docs fix). Every address in THIS section is chain-97.
2. Gate completeness: enumerated the FULL state-mutating ABI of both registries (not CCID's count of 7 on faith).
   Registry-write selectors = exactly 7, each gated by the issuer gate on engine E: IdReg registerIdentity
   0xf176f9a1 / registerIdentities 0x2694f92f / removeIdentity 0x322b93d6; CredReg registerCredential 0x8ab73618 /
   registerCredentials 0xf6635baf / renewCredential 0x22fe00bb / removeCredential 0x93893fcb -> each getPolicies =
   [0x2aedDb48]. Controls: attachPolicyEngine / transferOwnership / random selector -> getPolicies = [] (so the
   instrument discriminates; those are Ownable-gated, not engine-gated). No ungated registry-write selector exists.
3. Gate authorizes EXACTLY {0xFc99}: senderAuthorized(0xFc99)=true, (0xCf30/0xdEaD)=false, AND event enumeration -
   1 SenderAuthorized (to 0xFc99), 0 SenderUnauthorized, 0 post-deploy changes.
4. defaultAllow=true confirmed BEHAVIORALLY (read-only eth_call, no state change): unauthorized registerIdentity +
   registerCredential revert 0xfbdad4f4 wrapping "sender is not authorized" (the gate's PolicyRejected); authorized
   issuer 0xFc99 path returns 0x (success) - so Continue->defaultAllow allows. Specific gate fired, not an incidental revert.
5. Gate un-strippable / un-bypassable: engine admin roles (DEFAULT_ADMIN 0x0, ADMIN_ROLE, POLICY_CONFIG_ADMIN_ROLE)
   held by and granted only to 0xFc99 (RoleGranted enumeration over deploy range); IR+CR getPolicyEngine()==E and
   owner()==0xFc99; IR.attachPolicyEngine from a non-owner reverts OwnableUnauthorizedAccount (0x118cdaa7).
6. Validator points at the NEW BNB registries: getCredentialSources(common.kyc 0x455af80b...)=[(IR, CR, 0x0)].
7. SHADOW-SAFE: live MBT 0x3BC5 on token engine 0x22e34A3a still [Allow 0x045A3c91, Pause 0x060Fb0C8] on
   transfer/transferFrom/mint; the validator is NOT attached. The deploy did NOT change live MBT enforcement.
8. Bytecode provenance: all 5 impls' on-chain runtime keccak == this repo's compiled chainlink-ace out/ artifacts,
   EXACTLY (equal length + equal hash; no immutables). Deployed code == the intended audited ACE build. (Scope: this
   proves deployed==built; it does not re-audit the BUSL chainlink-ace source, which is Chainlink-audited upstream.)
   PROFILE (BnB flagged): the CCID deploy used FOUNDRY_PROFILE=ccid (foundry.toml [profile.ccid] = default +
   via_ir=true; artifact metadata: solc 0.8.24, optimizer 200, viaIR true, evmVersion cancun). The EXACT byte-for-
   byte match of all 5 impls is itself dispositive that the artifacts I compared were built under THIS deployment's
   profile - a profile mismatch (viaIR/optimizer/solc) diverges pervasively, not just at a tail, so 5 exact matches
   cannot occur across a profile mismatch. BnB's "verify under the profile of THIS deployment" is satisfied.

PARITY (pre-ATTACH precondition; CCID backfilled {0xFc99, pool} with common.kyc, 4 txs status 1). Credential side
INDEPENDENTLY verified by me on chain 97: VP.validate(0xFc99)=true, VP.validate(pool 0x2bC9)=true, VP.validate(
0xdEaD)=false (neg control); IR.getIdentity(0xFc99)=0x9fa0f6b1..., (pool)=0x21b4b77c..., (dEaD)=0x0; AllowPolicy
0x045A3c91 addressAllowed(0xFc99)=true,(pool)=true,(0xCf30/0xdEaD)=false. So both allowlisted-and-named addresses
ARE credentialed. ALLOWLIST-EXACTNESS (that NO other allowlisted address lacks a credential = the real bricking
check) is NOT independently closed here: my AddressAllowed full-span enumeration hit the endpoint getLogs range cap
(empty result = false-absence trap, not a verified empty), so exactness rests on CCID's AddressAllowed scan (their
positive-control claim) UNTIL I enumerate it myself at the ATTACH gate. This is exactly the F2 bricking check.

FLAGS (not blockers for the testnet shadow deployment; they bind on ATTACH + mainnet):
F1. ROLE CONCENTRATION / key hygiene: the authorized badge issuer is 0xFc99 - the deployer, engine admin (all 3
    roles), registry/validator owner, and token owner, ALL the same hot key. The deploy script's OWN header (line 46)
    prescribes "a fresh dedicated badge-issuer key"; it was set to the deployer instead. CCID's "only 0xFc99" is
    correct, but this is the same role-concentration class as the BNB sanctions-admin flag. For ATTACH/mainnet:
    authorizeSender a dedicated badge key, move engine POLICY_CONFIG_ADMIN to a separate (ideally timelocked) admin.
    >> F1 RESOLUTION 2026-10-12 (Ilan's chat decision, RELAYED by BnB - not witnessed by me here): "proceed with
    0xFc99" for the BNB TESTNET attach. Badge issuer stays 0xFc9933C8...Fd33c; the concentration remains DISCLOSED
    in 06-contracts 6.22.1 (PR #516). F1 CLOSED AS A TESTNET DECISION ONLY. The dedicated-cold-key requirement
    stands as a HARD CONDITION BEFORE MAINNET (unchanged); do not let any mainnet/public-production step proceed on
    the shared 0xFc99 key.
F2. ATTACH IS A SEPARATE GATE. This PASS covers the deployed SHADOW stack only. Enforcing credentials on the live
    token (adding the validator to the MBT engine's transfer/transferFrom/mint chains) is a distinct action that must
    be re-verified when staged: policy ORDERING (sanctions/pause vs credential), and that it does not brick existing
    MBT holders who lack the new credential. CCID's "parity-100% after {0xFc99, pool} backfill" is pre-ATTACH.
    F2 EVIDENCE BANKED (for when I run the gate): BnB reproduced CCID's AllowPolicy storage-slot derivation for a
    state-override sim (slot 0x5d220cd1...efeab = allowList[0xdEaD]; controls deployer=1/pool=1/dEaD=0; override
    flips the result only on the right slot, wrong-slot control reverts). CAVEAT for my gate: that proves the
    validator READS allowlist membership correctly (the validate() mechanism) - it does NOT answer allowlist-SET
    EXACTNESS (that no OTHER allowlisted address lacks a credential), which is the actual bricking check and still
    needs a full AddressAllowed enumeration (my earlier attempt hit the getLogs range cap). At attach I run BOTH:
    (a) enumerate the live allowlist set, (b) validate() every member, (c) policy ORDERING on the MBT engine.
    EXACTNESS INDEPENDENTLY CONFIRMED (pre-attach, point-in-time) 2026-10-12: the keyed Alchemy URL caps getLogs at
    ~10 blocks; publicnode https://bsc-testnet-rpc.publicnode.com answers 20k-block chunks. My own enumeration on
    AllowPolicy 0x045A3c91 (chainId read =97, head 136466846): AddressAllowed = EXACTLY 2 (0xFc99 + pool 0x2bC9, in
    the deploy block; positive control = the two deploy-time writes appear), AddressDisallowed = 0, every chunk OK
    (no false-absence). Direct: addressAllowed true for {0xFc99,pool}, false for validator/0xdEaD/0xCf30; owner()=
    0xFc99 (sole caller of allowAddress). Both members validate()=true (verified above) => NO allowlisted-but-
    uncredentialed member as of block 136466846, so an attach today would not brick. MUST RE-RUN AT ATTACH: owner
    0xFc99 can add addresses between now and then; exactness is point-in-time, not a standing property.

    >> F2 GATE RESULT 2026-10-12: PASS on the ATTACH PLAN (PR #518, script/ccid/AttachValidator.s.sol bnb; 3 ops on
    engine 0x22e34A3a: addPolicy(MBT 0x3BC5, transfer 0xa9059cbb, validator 0x0AE06A1A, [from,to]); (transferFrom
    0x23b872dd,[from,to]); (mint 0x40c10f19,[to])). Verified independently:
    - ORDERING correct (append validator at index 2 -> [Allow,Pause,Validator]). From chainlink-ace SOURCE:
      PolicyResult enum {None,Allowed,Continue}; AllowPolicy.run REVERTS PolicyRejected for a non-allowlisted
      account else returns Continue (NEVER Allowed); PausePolicy.run REVERTS when paused else Continue (NEVER
      Allowed). So NO pre-validator policy short-circuits with terminal Allowed -> the Validator IS reached for
      allowlisted+unpaused ops (not a phantom control); pause still reverts BEFORE the credential check; the
      credentialed pass-path is Allow Continue -> Pause Continue -> Validator Continue -> engine DEFAULT.
    - DEFAULT = allow (liveness): token engine 0x22e34A3a storage read - global defaultPolicyAllow (slot 0x9876..
      9f00)=1, targetHasDefault[MBT]=0 (no per-target override) => effective MBT default = allow. So a compliant
      op passes and a NON-credentialed party reverts at the Validator (intended). Attach does NOT change this default.
    - EXTRACTORS set so the declared params feed the right accounts: transfer/transferFrom extractor 0x428F8ACE
      (ERC20TransferExtractor -> from,to), mint extractor 0xf3186574 (ERC20MintExtractor -> to).
    - NO-BRICK: allowlist re-enumerated independently FRESH (publicnode, head 136469459, 20k chunks, positive
      control, 0 chunk errors) = EXACTLY {0xFc99, pool}; both validate()=true; MBT totalSupply=0. No allowlisted-or-
      holding address would be bricked.
    CONDITIONS ON THIS PASS: (a) the executed attach tx must MATCH this plan exactly (append on engine 0x22e34A3a,
    these selectors/params, not replace); (b) a FINAL allowlist re-enum + validate() immediately before Ilan signs
    (owner 0xFc99 can add an address in between - point-in-time); (c) POST-ATTACH I re-verify getPolicies(MBT, each
    sel) == [Allow,Pause,Validator] in that order AND behaviorally that a non-credentialed party reverts at the
    validator while a credentialed one passes, before the attach is considered enforcing. CCID's mandatory pre-attach
    identity re-verify (engine/mbt/validator by typeAndVersion+wiring) should carry (b).

    >> POST-ATTACH (c) CONFIRMED - VALIDATOR IS ENFORCING, 2026-10-13 (attach Ilan-signed, CCID routed; I verified
    independently on chain 97, all eth_call no-write):
    - ORDERING: getPolicies(MBT, transfer/transferFrom/mint) = [Allow 0x045A3c91, Pause 0x060Fb0C8, Validator
      0x0AE06A1A] on all 3. Allow+Pause PRESERVED at idx 0/1, Validator appended at idx 2 => was an APPEND not a
      replace (cond a satisfied by the resulting state). TX-LEVEL cond (a) also confirmed: the 3 attach txs
      0x796639a8 (blk 136470499) / 0xb30ba9ce (136470504) / 0xe54cd460 (136470508), all status 1, from 0xFc99 to
      engine 0x22e34A3a, decode to addPolicy(MBT, transfer/transferFrom/mint, validator 0x0AE06A1A, [from,to]/
      [from,to]/[to]) - matches the plan exactly.
    DOC RATIFIED: read PR #519 at head f9eac380 (the doc carrying my V&L confirmation, written by the BnB channel):
    it states my scope accurately, honestly frames the validator rejection as a STATE-OVERRIDE SIMULATION not a live
    tx (no real allowlisted-but-uncredentialed address exists), keeps both caveats (F1 hard pre-mainnet, explorer
    outstanding, nothing-public-before-Ilan) and the jurisdiction/accreditation non-claim. No overclaim; ratified.
    STRONGER PROOF AVAILABLE IF THIS EVER GOES EXTERNAL (banked, not run - internal record stands on the simulation):
    the state-override reject is adequate internally, but a NON-simulated proof is cheap on testnet - allowlist a
    throwaway address WITHOUT crediting it, attempt a real mint to it, observe the live revert at the validator
    (totalSupply 0 so nothing at risk; the transient allowlisted-uncredentialed state is exactly what the validator
    catches). Requires 2 testnet writes - NOT mine to execute (CCID/Execution Staging would stage; I verify). Only
    needed if an external claim is contemplated; do not run under pressure / invent it ad hoc.

    >> PROVE-MINT CEREMONY (Ilan-decided 2026-10-13; spec SESSION-COORDINATION/bnb-prove-mint-ceremony.md) - V&L
    DESIGN PASS. 5 owner txs from 0xFc99: setMintBurnAuthority(0xFc99,true) / mint(0xFc99,1e18) / transfer(0xFc99,
    1e17) / burn(1e18) / setMintBurnAuthority(0xFc99,false); ends at baseline. Live baseline I read myself (block
    ~136474/03:02Z): minter(0xFc99)=false, minter(pool)=true, supply 0, recipient 0xFc99 allowlisted+validate()=
    true; feed answer 1e24 updatedAt 1791840154 (FRESH, maxStaleness 86400, expires ~1791926554 = 2026-10-13T21:22Z
    unless keeper refreshes). CONDITIONS: (1) re-read feed updatedAt immediately before step 2 - HOLD if (now-updatedAt)
    >86400 or step 2 reverts StaleReserveData; (2) ATOMIC - steps 1-5 are one ceremony, must reach burn(step4->supply
    0)+revoke(step5->minter false), never stop after step 2 (lingering hot-key minter + supply). DESIGN CATCH raised to ES: burn(uint256) is
    onlyMintBurnOrBridge, so revoke (step 5) must be ORDERED AFTER a confirmed burn-to-zero - if burn reverts but
    revoke fires "unconditionally", supply is stranded at 1e18 and UN-burnable (no minter). Authority-teardown
    (minter=false) and supply-teardown (supply=0) are SEPARATE invariants; both must hold at end. Safe order:
    burn -> read supply==0 -> revoke; if burn reverts, HOLD with authority and retry burn, do not revoke early.
    RESOLUTION: ES chose UNCONDITIONAL revoke (if-guard wraps only 2-4) - a defensible authority-first tradeoff
    (for a hot key, a dangling minter is worse than stranded recoverable testnet supply). Accepted. Residual edge
    (burn reverts -> supply stranded 1e18, minter revoked) is remote (trivial self-burn, no PoR/policy gating) and
    recoverable (re-appoint/burn/revoke). V&L BACKSTOP: my post-verify enforces END-STATE supply==0; if non-zero I
    flag immediately for the cleanup. Feed clean-abort (stale -> skip 2-4, still appoint+revoke -> supply stays 0).

    >> PROVE-MINT VERIFIED 2026-10-13 (executed, Ilan-signed; I verified independently on chain 97, not inherited).
    5 txs all status 1, all from 0xFc99 to MBT: (1) appoint 0xbfc8d137 sel 0x54147dcb; (2) mint 0x52f34515 sel
    0x40c10f19 - Transfer 0x0->0xFc99 amount 1e18 (0x0de0b6b3a7640000), a REAL credentialed mint through the live
    [Allow,Pause,Validator]+PoR chain = PERMIT PATH PROVEN; (3) transfer 0xf5f65663 sel 0xa9059cbb - Transfer
    0xFc99->0xFc99 1e17 (through the transfer chain on from+to); (4) burn 0xd30a4f94 sel 0x42966c68 - Transfer
    0xFc99->0x0 1e18 = self-only burn of own balance; (5) revoke 0xcbaea5be sel 0x54147dcb. END-STATE baseline:
    totalSupply 0, balanceOf(0xFc99) 0, minter(0xFc99)=FALSE, minter(pool)=TRUE (untouched) - no lingering
    authority, NO stranded supply (burn succeeded; backstop clean). BOTH issuance mappings false on 0xFc99:
    mintBurnAuthorities=false AND bridgeAuthorities=false (onlyMintBurnOrBridge checks both; BnB's add, I re-read) =
    no mint/burn capability of either kind left on the hot key. Compliance intact: 0xFc99 still allowlisted+
    validate()=true; getPolicies(MBT,mint) still [Allow,Pause,Validator]. SCOPE held: permit path + PoR + self-only
    burn proven live; validator REJECTION remains the override sim (not claimed live). Docs 6.22.1: the "no real
    mint / non-zero transfer executed" line must flip in the same change that records these 5 hashes.

    >> disableReserveChecks (BNB Option 2, staged by ES for Ilan, selector 0x18ba2555) - SCOPE CONFIRMED by me
    from src/RWATokenCCT.sol: disableReserveChecks() onlyOwner emits ReserveChecksDisabled+ReserveFeedUpdated and
    sets reserveFeed=address(0), reserveFeedDecimals=0, NOTHING ELSE (no policy/allowlist/mintAuth/supply/canonical
    touch); _checkReserves line 457 `if (reserveFeed==address(0)) return;` so it becomes a no-op. CLAIM IMPACT
    (ledger + external): after this tx the PoR/reserve gate on BNB MBT is OFF. The prove-mint "PoR check passed" is
    AS-OF block 136475016 ONLY, and it was a MOCK feed (answer 1e24, never real reserves - mock-PoR-never-real).
    So post-disable the BNB MBT mint controls are [Allow, Pause, Validator] = allowlist + pause + credential, with
    NO reserve gate. Reconciliation points: (a) BNB MBT must NOT be included in any "PoR-gated / reserve-gated every
    asset" claim after this (counterexample to dec_por_feed_every_rwa); (b) any "BNB reserve-gated" doc statement
    goes false (docs flip = BnB lane); (c) MAINNET LINE: disabling reserve checks is acceptable on testnet with a
    mock feed, NEVER on mainnet with a real PoR feed - reserve checks must be ON for real value. Reversible via
    setReserveFeed(0x5fe9d900). POST-VERIFY RESUME HOOK (when ES routes the tx): reserveFeed==0x0, reserveFeedDecimals
    ==0, policy chain [Allow,Pause,Validator] + allowlist + supply + mintAuth all UNCHANGED, and a credentialed mint
    sim still passes but no longer depends on feed freshness (StaleReserveData can't fire with feed==0).
    >> VERIFIED 2026-10-13 (executed; I re-verified independently on chain 97). tx 0x9cb4900c18db65de27ff17df65839b
    839dddcdc51e98b9dd81adb412bd4a5a42 (block 136479590, status 1, from 0xFc99, sel 0x18ba2555, events
    ReserveChecksDisabled 0x35a6629a + ReserveFeedUpdated 0xae9e3383). POST: reserveFeed()=0x0, reserveFeedDecimals()
    =0; owner/totalSupply(0)/minter(pool)=true/addressAllowed(0xFc99)/validate(0xFc99) all UNCHANGED; getPolicies on
    transfer+transferFrom+mint all still [Allow,Pause,Validator]. BEHAVIORAL: mint(pool,1e24+1) from pool -> 0x
    (reserve gate OFF - a mint exceeding the old mock reserve 1e24 now passes), mint(0xdEaD,1) -> 0xfbdad4f4
    AllowPolicy (credential/allowlist enforcement INTACT). Scope was exactly as claimed - only the feed nulled.
    THREE-WAY agreement (me + ES + BnB) on all post-state + behavioral. PR #524 RATIFIED at head
    33b70e6201401f549cb6570a64b4ea311667c27c (docs flip): it honestly removes every "PoR/reserve-gated" claim for
    BNB ("No reserve gate on BNB ... it never had a real Proof-of-Reserve feed ... any mint/burn authority the owner
    appoints can issue without a reserve check ... BNB supply must never be described as reserve-checked, reserve-
    backed or PoR-gated. Reserves are attested at the hub (Sepolia)."), matches my verified on-chain facts, adds no
    new contract addresses, no overclaim. The "reserves attested at the hub (Sepolia), destinations not individually
    PoR-gated (same as the other 6)" framing RESOLVES the PoR-every-asset counterexample honestly (hub-attested
    model, not per-destination feeds). CLARIFICATION on surface: this is an issuance-CONSTRAINT change (reserve gate
    removed), NOT an authority change - the mint/burn authority SET is unchanged (minter(pool)=true, (0xFc99)=false);
    it is the 2nd BNB issuance-surface change today (1st = prove-mint appoint/revoke) and is PERMANENT until
    setReserveFeed. UNVERIFIED-BY-ME note (BnB-reported): Base's reserve feed twin 0x3520f45A = a Base MockV3Aggregator
    (1 MBT) is the same mock-PoR situation and is NOT covered by this change - if Base is ever called reserve-gated
    that is also only a mock. BASE INDEPENDENTLY CONFIRMED by me now (current state, chain 84532 block 48047462 - NOT
    a change, NOT decided): token 0x2aedDb48 "Miami Beach Tower"/MBT (twin of chain-97 CCID IssuerGate PROXY, resolved
    by chain) owner 0xFc99, reserveFeed()=0x3520f45A, totalSupply 1e18 (Base HAS 1 MBT, unlike BNB's 0); tAV +
    disableReserveChecks() both REVERT = OLDER build (reserve-off there = setReserveFeed(0), not disableReserveChecks).
    Feed 0x3520f45A = MockV3Aggregator (answer 1e24, 18dec, 1056B; twin of chain-97 CCID IssuerGate IMPL). So Base's
    reserve gate is a MOCK, not real PoR - same class as BNB pre-disable, consistent with hub-attested reconciliation.
    STATUS: NO change on Base, Ilan NOT asked (S8 raising) - do NOT ledger Base as done. If a Base reserve-off is ever
    decided it'd be setReserveFeed(0) (owner-gated; non-owner reverts Ownable) and I verify then.
    >> BASE DECISION LANDED 2026-10-13 (Ilan's chat "Yes to the Base question", RELAYED by BnB - not witnessed by me;
    spec SESSION-COORDINATION/base-disable-reserve-check.md). Disable Base's reserve check, same hub-only reasoning
    as BNB. Staged for Ilan via ES, NOT signed yet. LEVER (confirmed by my Base read + spec): token 0x2aedDb48
    setReserveFeed(address(0)) - Base build has NO disableReserveChecks(); setReserveFeed accepts 0. INTENT LIVES HERE:
    on-chain this emits ONLY ReserveFeedUpdated(0x3520f45A, 0x0) - NOT a "ReserveChecksDisabled" event - so the chain
    cannot distinguish "disable" from "repoint"; the decision-to-disable is recorded in this ledger + 06-contracts,
    not derivable on-chain. POST-VERIFY RESUME HOOK (when ES routes the tx): (1) the tx ARG was address(0) [a disable,
    not a repoint to another feed] AND reserveFeed()==0x0 after; (2) status 1, from owner 0xFc99, chain 84532, exactly
    ONE event ReserveFeedUpdated(old,0), no others; (3) UNCHANGED: owner, totalSupply 1e18, minter(pool 0x49f1aa7C =
    Base pool twin), addressAllowed(0xFc99), and the live Base policy chain (read it, do not assume BNB's); (4)
    behavioral: pool mint 5,000,000 MBT -> 0x (reserve gate off), mint(0xdEaD,1) still reverts at the policy.
    Reversible via setReserveFeed(0x3520f45A). CLAIM IMPACT: after this neither BNB nor Base is reserve-gated (both
    mock before); the hub-attested model (reserves at Sepolia) is the only real PoR claim. Do NOT mark done until I
    verify the executed tx. Knock-on (NOT my lane, noted): por-keeper-base -> expected-down then timer off; Sepolia
    hub keeper ops/por-keeper UNTOUCHED.
    >> BASE VERIFIED 2026-10-13 (executed, Ilan-signed; I verified independently on chain 84532, incl. the behavioral
    pair ES left open). tx 0x7c2a220e6e7d76c86b1048dac075d0ce07eea5669d568a4902bd0bee56e2d458 (block 48047597,
    status 1, from 0xFc99, to token 0x2aedDb48, sel 0x2b0e8cd7). DISABLE-NOT-REPOINT confirmed at 3 levels: calldata
    arg decoded = address(0); the single event (1 log total) = ReserveFeedUpdated(old 0x3520f45A -> new 0x0); and
    the authoritative state read reserveFeed()==0x0. UNCHANGED: owner 0xFc99, totalSupply 1e18, minter(pool
    0x49f1aa7C)=true, and the Base mint policy chain (READ not assumed: getPolicyEngine()=0xa1a58d40, getPolicies
    (mint)=[0x2b7B6f83, 0x55d0Ed9c, 0x6dea0c23] - Base-specific, 3 policies, intact). BEHAVIORAL (closed ES's gap):
    mint(0xFc99,5e24) from pool -> 0x (5e24 > old mock reserve 1e24, so pre-disable this reverted InsufficientReserves;
    now passes = reserve gate OFF and 0xFc99 passes the Base chain), mint(0xdEaD,1) -> 0xfbdad4f4 policy revert
    (compliance INTACT). INTENT (reserve check DISABLED, not a feed repoint) lives here since the chain only says
    ReserveFeedUpdated. Reversible setReserveFeed(0x3520f45A). NET: neither BNB nor Base is reserve-gated now (both
    mock before); real PoR claim = hub-attested at Sepolia only.
    >> BASE FINDING (surfaced while diff-vs-ledgering PR #525, 2026-10-13): Base MBT IS credential-gated, NOT
    "allowlist-only". Read live on chain 84532: engine 0xa1a58d40 getPolicies on transfer/transferFrom/mint ALL =
    [AllowPolicy 1.0.0 0x2b7B6f83, PausePolicy 1.0.0 0x55d0Ed9c, CredentialRegistryIdentityValidatorPolicy 1.0.0
    0x6dea0c23]. Validator is ATTACHED **and ENFORCING** (not the attached!=enforcing trap): getCredentialSources
    (common.kyc)=[(idReg 0x53d88a3F, credReg 0xbf9206B3, 0x0)] (real Base registries), validate(0xFc99)=true /
    validate(0xdEaD)=false, and the override sim (allowList[0xdEaD]=1 so Allow passes) reverts AT THE VALIDATOR
    0x6dea0c23 "account identity validation failed". => PR #525's 6.13 line "No CCID identityValidator yet ...
    allowlist-only, same as the un-flipped chains" is FALSE and must be corrected before ship (underclaim, but still
    a false public statement). Base is allowlist + pause + credential on all 3 selectors. Count/claims reconciliation
    (Base in the credential-gated EVM set; the "Base outside the 5-chain CCID rollout" framing is stale) = S8 claim-
    gate. VITE_BASE_ENABLED is NOT "true" in webapp/.env* (chains.ts: Base FLAG-GATED OFF in prod) - so the dApp
    flag-gated-off claim is accurate; Base renders "soon", not in the active dropdown.
    #525 CORRECTED + RE-RATIFIED at head 2a01fbc1709813b0b886b46a5472569043bbc300: the false "no validator/allowlist-
    only" line is replaced with "CCID credential validator ATTACHED (corrected 2026-10-13...)" + the 3-selector chain,
    and it correctly attributes ENFORCEMENT to V&L's ledger read ("not something this entry re-shows") rather than
    re-claiming it - honest (BnB read attachment; I read enforcement). Reserve-disable claims unchanged + accurate;
    no overclaim. Clear to merge. HEAD MOVED to dfdbb164763aa25060cf24f1ae47340f9400b6b5 (re-diffed): the ONLY new
    delta vs 2a01fbc is por-keeper-base RETIREMENT text - which is HOST/systemd state on box 644B825, OUTSIDE V&L's
    on-chain lane (I do not verify box state / no box ops). The doc honestly sources it to the operator's terminal
    output and hedges the ~04:10 UTC as INFERRED, and does not attribute the keeper claim to V&L. On-chain content
    (validator line, reserve-disable, via_ir note) unchanged + still ratified; keeper-ops claim neither confirmed nor
    disputed by me (not my lane, honestly attributed). Clear from the on-chain side.

    >> FORWARD NOTICE 2026-10-13 (Ilan "Let's do #1", relayed by BnB; spec SESSION-COORDINATION/bnb-redeploy-
    destination.md; NOTHING broadcast yet): BNB chain 97 will be REDEPLOYED as a true DESTINATION. This SUPERSEDES
    (does NOT delete) the entire current chain-97 stack I verified this session: old MBT 0x3BC55A7D (supply 0), old
    CCID registries (RAE 0xA56efb5A / IssuerGate 0x2aedDb48 / IdReg 0x5D273AAE / CredReg 0x358f2932 / Validator
    0x0AE06A1A), and the attach/prove-mint/reserve-disable on them. Those entries remain valid AS-OF-BLOCK historical
    records but are SUPERSEDED once the new stack lands - do not treat old 0x3BC5 / 0x0AE06A1A as current then. Note:
    old validator 0x0AE06A1A is bound to the OLD engine so it cannot serve the new token (a new CCID stack is part
    of the redeploy). The new destination posture to VERIFY when it lands: canonical = Sepolia selector
    16015286601757825753, reserveFeed 0x0 (hub-attested, no per-destination feed), pool bridgeAuthorities=true /
    mintBurnAuthorities=false (destination mints via bridge, not direct), sanctionsRegistry 0x8c57Abe7 reused
    (immutable on the token), policy chain + ownership + burn guard; THEN new CCID stack (full registry-write gating
    + parity gates, same rigor as the first) + attach. This is the CLEAN version of the hub-attested reconciliation
    (uses the #522-corrected hub-vs-destination deploy topology). Event-driven: verify the actual DEPLOYED addresses
    when routed, read the spec then.

    >> BNB DESTINATION REDEPLOY - GATE HELD 2026-10-13, FINDING: DUPLICATE DEPLOYMENT (do NOT pass either stack yet).
    Broadcast cut 512305e; BnB routed read-backs for ONE stack but chain 97 has TWO complete, coherent, TAR-registered
    destination MBT stacks (I verified both live, deployer 0xFc99 nonce now 108, both deploys landed):
      STACK A (BnB's routed read-backs, their stated nonces 60-83): token 0x3953a90Ad44Dc771E7A1a1Fc4608Bb89f13cA068
      <-> pool 0x7CC5c66e0B3ec3aa7106c003ef45D0a1B33E9e08 (pool.getToken()==A; TAR getTokenConfig admin 0xFc99, pool
      0x7CC5), engine 0xB917335d, Allow 0x044951AB, Pause 0x388078c3.
      STACK B (what the evidence file run-latest-destination-redeploy-2026-10-13.json recorded, nonces 84-95; that
      file then VANISHED from its path mid-verify): token 0xce8C237c6b8bF91df589eA740d4d65Cbb75F7DDd <-> pool
      0xf48f2683aEA6E5C1f395Eef71b012c3FD554a797 (pool.getToken()==B; TAR admin 0xFc99, pool 0xf48f2683).
    BOTH tokens: owner 0xFc99, supply 0, canonicalChainSelector 16015286601757825753 (Sepolia), isCanonicalChain
    FALSE, reserveFeed 0x0, sanctionsRegistry 0x8c57Abe7 - identical DESTINATION posture, BOTH BurnMintTokenPool
    1.5.1, BOTH registered in TAR 0xF8f2A446. (Note: B's pool 0xf48f2683 is also the AMOY Atlas LendingVault addr - a
    cross-chain CREATE twin; on chain 97 it is a real BurnMintTokenPool bound to token B.)
    RISK: both are CCIP-registered, so a lane / the dApp / docs / the next CCID attach could build on EITHER; the
    record-of-record (run-latest*.json -> B) and BnB's verification (A) name DIFFERENT tokens = wrong-target split.
    NEEDED before any PASS: (1) which token is THE canonical BNB destination MBT - A or B? (likely A = first deploy /
    BnB-verified, B = an accidental re-run, but that is INFERENCE, team confirms); (2) the OTHER stack un-registered
    from TAR + not wired/documented, or explicitly retired, so CCIP cannot route to it; (3) the evidence record
    restored and pointing at the canonical token. Only then do I run the full destination gate (bytecode provenance,
    burn guard, policy chain, engine roles) on the CANONICAL stack. Relayed to BnB + ES; RM/CCID gated meanwhile.
    >> RESOLUTION 2026-10-13: BnB confirmed the finding - destination deploy BROADCAST TWICE (73s apart, same key/
    command); STACK A (nonces 60-83)=BnB's, STACK B (nonces 84-107)=ES's/run-latest duplicate. Team recommends A
    CANONICAL, B abandoned (supply 0); CANONICAL SELECTION IS ILAN'S (BnB asked, pending). STACK A FULLY GATED by me:
    identity (PolicyEngine/AllowPolicy/PausePolicy 1.0.0 by tAV; token "Miami Beach Tower"/MBT/18); destination
    posture (canonical=Sepolia 16015286601757825753, isCanonicalChain FALSE, reserveFeed 0x0, sanctions 0x8c57 reused
    = bytecode immutable); pool bridgeAuth(0x7CC5)=true/mintBurn=false (#522 topology); policy [Allow 0x044951AB,
    Pause 0x388078c3] all 3 selectors; TAR admin 0xFc99 pool 0x7CC5; engine 0xB917335d DEFAULT_ADMIN=0xFc99 only;
    burn guard LIVE (0x584ca52c from pool, self-burn 0x, owner 0x82b42900); BYTECODE PROVENANCE CONFIRMED (on-chain
    == OB-bnb-redeploy artifact, 104 diff bytes/7 clusters ALL immutables [chainSelector b8159170038f96fb x4,
    sanctions 0x8c57 x2, assetId x1], metadata identical). NOT credential-gated yet (CCID re-attach pending; old
    validator 0x0AE06A1A bound to old engine, can't serve new token). OPEN FLAGS: (1) canonical A-vs-B = Ilan's call;
    (2) B (0xce8C237c / pool 0xf48f2683) STILL TAR-REGISTERED (getTokenConfig admin 0xFc99) - "abandoned in place"
    does NOT un-register; remains CCIP-routable -> must be UN-REGISTERED from TAR or confirmed never lane-wired.
    PR #528 RATIFIED at head 61315293 (A correct + B recorded unreferenced-duplicate + base_sepolia corrected to the
    real 6.13 stack incl validator 0x6dea0c23 [matches my Base reads] + "do not claim BNB credential-gated before
    attach"; no overclaim). Shared GSP 0x8c57 sanctionsAdmin=0xCf3099 unchanged.
    >> UPDATE 2026-10-13: ILAN DECIDED "A" CANONICAL (token 0x3953a90A / pool 0x7CC5 / engine 0xB917335d) - flag #1
    CLOSED, A is the BNB destination stack (double-broadcast was an accidental paste per Ilan, benign). TAR cleanup
    scope CONFIRMED LIVE by me (chain 97): THREE registered MBT tokens, all admin 0xFc99 - A->0x7CC5 (KEEP),
    B 0xce8C237c->0xf48f2683 (un-register), OLD 0x3BC5->0x2bC9Bb1d (un-register). Fix = setPool(B,0)+setPool(OLD,0)
    from admin 0xFc99 (BnB fork-rehearsed: 26,536 gas, non-admin reverts 0xed5d85b5); NOT yet decided (Ilan asked),
    nothing staged. GATE #2 OPEN until those land and I read getPool(B)==0 / getPool(OLD)==0 / getPool(A) UNCHANGED.
    BnB: no lane/remote-chain on any pool today, so the 3 are inert (their read) - reduces urgency, not the need.
    HOLD SCOPE for CCID (answered): the hold does NOT gate CCID-on-A (CCID deploy step D on A's engine 0xB917335d,
    backfill E {0xFc99, pool 0x7CC5}, attach F) - A is canonical+fully-verified and CCID #529 targets A's engine;
    CCID work is bound to A and independent of the B/OLD duplicate-TAR issue (separate engines/tokens, B/OLD inert).
    I gate each CCID step's RESULT against A SPECIFICALLY (engine 0xB917335d / token 0x3953a90A / pool 0x7CC5, NOT
    B's twins 0x905142640A / 0xce8C237c / 0xf48f2683 [=Amoy USDC / Amoy Atlas vault]). The B/OLD TAR un-registration
    stays a REQUIRED separate item (hygiene vs future wrong-target; must be done + one-token-registered before public
    exposure / docs show BNB as a single token). #528 ratified head 613152936 is current+stable (ls-remote).
    ROOT CAUSE confirmed by ES (broadcast side): the destination command was handed to Ilan by BOTH BnB and ES
    independently, and he ran it twice (identical command, 73s apart) - an accidental double-run, not two different
    reads. ES categorically did NOT sign either (never-signs guardrail); both are Ilan's key. COORDINATION HAZARD
    worth a guard: two channels staging the SAME signer command -> double-execution risk; a single-stager / dedupe
    convention would prevent recurrence (not my lane to fix, flagged). A cross-checked independently by me + RM + ES
    (three-way agreement). TAR cleanup (setPool B/OLD ->0) pre-flighted + signed-held for Ilan by ES; gate #2 stays
    HELD until I read the live post-state.
    CCID #529 CONFIG PRE-CHECKED (origin/main, before step D signs): DeployCCIDRegistries bnb _cfg.tokenPolicyEngine
    = 0xB917335d (A's engine), AttachValidator bnb _cfg engine=0xB917335d / mbt=0x3953a90A (A) - targets STACK A, NOT
    B's 0x905142640A / 0xce8C237c. (AttachValidator bnb validator field filled from step-D deploy log.) Registry-admin
    engine is a DEDICATED separate PolicyEngine, defaultAllow=true + OnlyAuthorizedSenderPolicy (same model as the
    first BNB CCID). F1 CARRIES FORWARD: CCID_ISSUER=0xFc99 again (single hot key = issuer+deployer+admin) - testnet-
    accepted per Ilan's earlier decision, HARD pre-mainnet condition; I re-confirm it on the step-D result. Each
    CCID step verified on-chain against A (engine 0xB917335d / token 0x3953a90A / pool 0x7CC5) with the NOT-B check.
    >> GATE #2 VERIFIED + BNB DESTINATION HOLD LIFTED 2026-10-13 (my own live read, chain 97 block 136611245):
    getPool(B 0xce8C237c)=0x0 (was 0xf48f2683), getPool(OLD 0x3BC5)=0x0 (was 0x2bC9Bb1d), getPool(A 0x3953a90A)=
    0x7CC5c66e UNCHANGED; getTokenConfig admins all 0xFc99 unchanged (B/OLD pool fields cleared to 0x0, A config
    intact). Transition unambiguous (I held the before-state). B and OLD are NO LONGER CCIP-ROUTABLE; A is the SOLE
    routable MBT on chain 97. Duplicate/third-token wrong-target hazard CLOSED at the state level -> the BNB
    DESTINATION HOLD LIFTS for stack A. (Deployer nonce 128 at my read, up from 110 - the CCID step-D deploy is
    proceeding; separate thread.) PROVENANCE PENDING (non-blocking): the two setPool tx hashes/receipts are ES's to
    route; I'll attach the per-tx calldata-literal + PoolSet-event confirmation when they arrive, but the live state
    is authoritative for the gate.
    >> PROVENANCE ATTACHED (gate #2 fully closed): tx1 0x094907d2...f0f7c (block 136610072) status 1, from 0xFc99,
    to TAR, calldata setPool(0xce8C237c[B], 0x0), PoolSet event token=B / previousPool 0xf48f2683 / new 0x0; tx2
    0xa68ede49...0839 (block 136610496) status 1, from 0xFc99, to TAR, calldata setPool(0x3BC5[OLD], 0x0), PoolSet
    event token=OLD / previousPool 0x2bC9Bb1d / new 0x0. Each tx hit its LITERAL target, neither touched A. (tx1 was
    RBF-replaced at nonce 108 - one mined effect.) State + provenance both confirmed; gate #2 DONE.
    >> CCID-ON-A SHADOW STACK (step D, commit f82c9cf, 18 txs nonces 110-127, deployed once) - SHADOW VERIFY PASS
    2026-10-13 (my own chain-97 reads, full first-BNB-CCID rigor). Addresses (tAV, all codesize 130): RegistryAdmin
    Engine 0xFE4B265b (PolicyEngine 1.0.0); IssuerGate 0xBe010733 (OnlyAuthorizedSenderPolicy 1.0.0; twin=Minato
    validator elsewhere, resolved on 97); IdentityRegistry 0x4EcF0cc7; CredentialRegistry 0xc8567316; ValidatorPolicy
    0xF7e63f6c (CredentialRegistryIdentityValidatorPolicy 1.0.0). PASSED: registries->RAE 0xFE4B265b + owners 0xFc99;
    ALL 7 registry-write selectors gated to issuer 0xBe010733 (admin selector ungated control); issuer authorized set
    EXACTLY {0xFc99} (1 SenderAuthorized, 0 unauth, event-enum); RAE admin roles all+only 0xFc99 (RoleGranted enum,
    3 roles); defaultAllow=true + gate enforces (unauth registerIdentity/registerCredential revert 0xfbdad4f4 "sender
    not authorized", auth 0xFc99 -> 0x); attachPolicyEngine owner-gated (non-owner 0x118cdaa7); validator getCredential
    Sources(common.kyc)=[(IR 0x4EcF0cc7, CR 0xc8567316, 0x0)] = the NEW A-CCID registries; SHADOW-SAFE: A's token
    engine 0xB917335d still [Allow 0x044951AB, Pause 0x388078c3] on all 3 selectors, validator NOT attached; BYTECODE
    PROVENANCE all 5 impls byte-identical (keccak) to chainlink-ace artifacts. F1 CARRIES FORWARD (issuer=0xFc99,
    testnet-accepted, hard pre-mainnet). NEXT: parity gate after CCID backfills {0xFc99, pool 0x7CC5}; then F2 ATTACH
    gate (ordering + no-brick on A's engine + override enforcement-proof; CONFIRM the validator is added to A's engine
    0xB917335d, NOT B's). #529 config pre-checked = targets A.
    >> STEP E PARITY GATE PASS 2026-10-13 (my own chain-97 reads, block ~136620480; backfill {0xFc99, pool 0x7CC5}
    4 txs nonces 128-131 all status 1 from issuer 0xFc99 to IR 0x4EcF0cc7 [registerIdentity 0xf176f9a1] / CR
    0xc8567316 [registerCredential 0x8ab73618]). Credential side: validate(0xFc99)=true, validate(pool)=true,
    validate(0xdEaD)=false on validator 0xF7e63f6c. ccids RE-DERIVED INDEPENDENTLY keccak(ob.ccid.v1||addr):
    0xFc99=0x9fa0f6b1..., pool 0x7CC5=0x6b127b5c... - both MATCH getIdentity + ES. getCredentialTypes = [common.kyc
    0x455af80b...] for both. NO-BRICK precondition MET: A's AllowPolicy 0x044951AB allowlist = EXACTLY {0xFc99,
    pool} (AddressAllowed enum via publicnode = 2, addressAllowed true both, dEaD false), both credentialed =>
    parity 100%, no allowlisted-but-uncredentialed holder (and supply 0). A engine 0xB917335d UNTOUCHED (E-before-F:
    [Allow,Pause] x3, no validator). PARITY VERIFIED. F2 attach now unblocked on the parity side; still needs #530
    merge + the attach staged; then I run the F gate (ordering [Allow,Pause,Validator] on A's engine + no-brick +
    override enforcement-proof + validator-bound-to-A-engine-0xB917335d confirm).
    >> STEP F ATTACH - F2 GATE PASS, BNB STACK A CREDENTIAL-GATED + ENFORCING 2026-10-13 (my own chain-97 reads,
    block ~136626742; Ilan-signed, clean #530 build 3baa911, 3 addPolicy txs). ORDERING: getPolicies(A engine
    0xB917335d, A token 0x3953a90A, transfer/transferFrom/mint) ALL = [Allow 0x044951AB, Pause 0x388078c3, Validator
    0xF7e63f6c] - validator appended LAST, Allow+Pause preserved (APPEND not replace), count exactly 3, on A's engine
    (NOT B's 0x905142640A). PER-TX: 3 addPolicy txs (transfer 0xae5daa17 blk136626254 / transferFrom 0xea3736d7
    136626258 / mint 0xa3906758 136626263) all status 1, from 0xFc99, to A engine 0xB917335d, addPolicy(A token
    0x3953a90A, sel, validator 0xF7e63f6c, [from,to]/[from,to]/[to]) - append on A, not B. ENFORCING (my own override
    proof): mint(0xFc99,1) from pool -> 0x (credentialed passes full chain); mint(0xdEaD,1) -> reject at AllowPolicy
    0x044951AB (gates first); mint(0xdEaD,1) + override allowList[dEaD]=1 -> reject AT THE VALIDATOR 0xF7e63f6c
    "account identity validation failed" (credential gate reached + enforcing, not phantom); validate(0xdEaD)=false.
    NO-BRICK confirmed (parity: allowlist exactly {0xFc99,pool} both credentialed). => BNB STACK A mint/transfer/
    transferFrom are now [Allow,Pause,Validator] = allowlist + pause + credential ENFORCED, same posture as Base.
    BNB CCID-ON-A REBUILD COMPLETE (D shadow -> E parity -> F attach, all V&L-verified). F1 (CCID_ISSUER=0xFc99)
    carried = HARD pre-mainnet. S8 to reconcile the cred-gated EVM table (BNB-A back in the enforcing set; read the
    table, not an integer). REMAINING before BNB PUBLIC: explorer verification of chain-97 contracts + Ilan's push.
    >> PR #532 RATIFIED (diff-vs-ledger) at head 719426c6ad52fbcca4119d94bd6a21d63c122d2c: the full cleanup + CCID
    stack + backfill + attach record. All addresses in my verified set (no new/unverified); hashes attributed to
    ES/CCID; stack identity/7-selector-gating/allowlist-exactness/byte-identical-impls attributed to V&L; enforcement
    reads to CCID (blk 136626724) + Lane Wiring (136626736) - my own F2 override proof corroborates. Claims all
    accurate, NO overclaim: destination/no-reserve-feed/pool-bridge-authority; duplicates un-registered; "sanctions
    list EMPTY (not sanctions-screened)" = correct framing (NO sanctions claim); no lane; hot key 0xFc99 hard pre-
    mainnet. Credential-gated count deferred to S8's per-chain table (not an integer). Clear to merge from V&L.
    >> OWNERSHIP CLARIFICATION on the remaining public-exposure item "explorer verification of chain-97 contracts":
    this is a PUBLISH/DEPLOY action (submit source to the chain-97 explorer so third parties read verified source) -
    NOT a V&L-performed task, and it was flagged UNTESTED on chain 97 (Etherscan V2 logs module unsupported on the
    key; no verify SUBMISSION attempted). What V&L PROVIDED is BYTECODE PROVENANCE (on-chain runtime == repo artifact,
    byte-identical modulo immutables - DONE for stack A + the 5 CCID impls). V&L will CONFIRM explorer verification
    once submitted (getsourcecode matches), but does NOT submit it. So before BNB public: (a) someone in the build-
    cutter/deploy lane SUBMITS explorer verification on chain 97 (owner TBD, not V&L), (b) V&L confirms it, (c) Ilan's
    push. Do not leave (a) assigned to V&L or it stalls.
    >> EXPLORER VERIFICATION - GATE ITEM 6 RULING: SATISFIED (FULLY), 2026-10-18, on my OWN live reads (Etherscan V2
    getsourcecode, chainid=97). ALL 10 stack-A objects explorer-verified: token 0x3953a90A (RWATokenCCT), pool
    0x7CC5 (BurnMintTokenPool), engine/allow/pause PROXIES 0xB917335d/0x044951AB/0x388078c3 (ERC1967Proxy) + their
    impls 0x90250a98 (PolicyEngine)/0xf8888292 (AllowPolicy)/0x53d88a3f (PausePolicy), and the VALIDATOR proxy
    0xF7e63f6c (ERC1967Proxy, SourceCode_len 33472 + ABI + Proxy=1 + Implementation->0x3a0c7208, identical to its
    verified siblings) + validator impl 0x3a0c7208 (CredentialRegistryIdentityValidatorPolicy). This CORRECTS the
    prior stale ledger line ("not verified; submission not attempted" - wrong by all 10). RESOLUTION of a two-reads-
    disagree: S8 read the validator proxy as NOT verified earlier today; my later live read shows it VERIFIED - the
    gap closed between the two reads (I confirmed full SourceCode/ABI, not just a name). So (a) submission is DONE,
    (b) V&L confirmation DONE (this read); only (c) Ilan's push remains before BNB public on the explorer axis.
    TWIN-TRAP (resolve by chain, confirmed via chain-97 getsourcecode ContractName): allow impl 0xf8888292 is a
    Sepolia mock PoR feed elsewhere but AllowPolicy on 97; pause impl 0x53d88a3f is the Sepolia POOL elsewhere but
    PausePolicy on 97. Each is verified AS its chain-97 contract.
    GATE ITEM 3 (sanctions list empty) - V&L read, formal revision deferred to Ilan/Legal: Legal ruled the list is
    empty on EVERY chain by design, so item 3 is NOT a BNB-specific unmet gap - it is a CLAIMS CONSTRAINT ("never
    describe BNB [or Base] as sanctions-screened"; BNB/Base are 3-policy, per pol_never_claim_jurisdiction), already
    enforced (chains.ts + the doc "must not be described as sanctions-screened"). I concur it should be reframed as
    the claims constraint, not treated as unmet; the gate-wording change is Ilan's/Legal's call (S8 put it to Ilan).
    >> EXPLORER-VERIFICATION SURFACE CORRECTED + CONFIRMED via SOURCIFY 2026-10-18 (RM executed the submit on Ilan's
    instruction via SOURCIFY; BscScan/Etherscan NATIVE verification is BLOCKED on the free tier for chain 97, so my
    earlier "Etherscan V2 getsourcecode" read was reflecting Sourcify-proxied data, NOT a BscScan badge). I confirmed
    directly against the authoritative registry (my own curls, https://sourcify.dev/server/v2/contract/97/<addr>):
    STACK A 10/10 EXACT_MATCH (from deploy commit 512305e4) - token/pool/engine+allow+pause proxies+impls/2
    extractors; CCID STACK 10/10 MATCH (runtime matches cut f82c9cf; metadata hash differs -> match not exact, no
    logic discrepancy, consistent with my byte-identical provenance) - engine/issuer/idreg/credreg/validator proxies
    +impls. 20/20 source-verified. NAMED LIMITATION (honest, in the doc): BscScan shows NO badge (chain-97 submit
    needs a paid Etherscan plan); Sourcify is the verification surface; paid BscScan badge is an optional follow-up.
    So gate item 6 = MET on the authoritative surface. PR #543 (head 541d0efd) diff-vs-ledger: item 6 grades/framing
    ACCURATE + my provenance correctly credited; item 3 reframe (CEO-instructed honest-claims posture: BNB 3-policy,
    never sanctions-screened, empty list = fleet-wide design, not an exposure precondition) matches my concurrence +
    pol_never_claim_jurisdiction. SCOPE of my sign-off: items 6 + 3-substance only; items 1 (lane proven both ways),
    2 (gas overhead), 5 (pre-demo/audit tooling) are Lane Wiring/CCIP/webapp domains, stated MET + attributed to them,
    NOT independently verified by me (CCIP delivery is its own gate, SUCCESS != delivered). No overclaim (reserve +
    sanctions lines are correct negations). Nothing wrong on what I checked.
    >> BNB STACK A ALLOWLIST - 0xCf30 ADDED AS CREDENTIALED HOLDER, 2026-10-22 (BnB flagged; my own chain-97 reads
    block 138344119). addressAllowed(0xCf30 0xCf309940...27d6)=true, validate(0xCf30)=true, getIdentity=0x16c9d968...
    == my recompute keccak(ob.ccid.v1||0xCf30) -> a legitimate allowlisted+credentialed HOLDER (0xCf30 = demo KYC
    wallet, Ilan holds key). Allowlist per spot-reads + ES/BnB reports = {0xFc99, pool, Router(spender), 0xCf30};
    HOLDER-credentialed invariant HOLDS (0xFc99/pool/0xCf30 validate=true; Router uncredentialed spender by design,
    validator-gated from holding). Provenance (ES nonces 141/142; deployer nonce 143) PENDING ES hashes+authz.
    EXACTNESS CAVEAT (honest, observation vs inference): I could NOT complete a no-unexpected-5th AddressAllowed
    enumeration this session - publicnode/bnbchain dataseed PRUNE old logs (false-absence: full-range returned 0 with
    the deploy-block positive control NOT firing), and keyed Alchemy caps getLogs at ~10 blocks (refused the full
    range, suggested [0x823ffe8,0x823fff1]=10 blocks). Known members spot-verified; set-COMPLETENESS pending a
    capable archival getLogs endpoint (or BnB/ES enumeration from one). I do NOT assert "exactly 4, no 5th" as
    V&L-verified this session. Only-0xFc99-can-allowAddress (owner-gated) bounds the risk; AddressDisallowed read also
    hit the cap (unreliable), so no removals-claim either.
    >> HEADS-UP / STANDING-BLOCK FLAG - BNB LENDING VENUE #550 (TestUSDT + LendingVault "Victory Capital"), not yet
    deployed/staged: its vault will be a HOLDER (collateral) + spender on stack A. repay() FIX PR #517 is MERGED
    (2026-10-14, "repay() must return collateral to the borrower, not msg.sender"). GATE WHEN IT LANDS: the deployed
    #550 LendingVault MUST be built from post-#517 code - I verify repay() behavior on the DEPLOYED vault (returns to
    borrower, NOT msg.sender) BEFORE Ilan's E2E test; merged != deployed. A user-facing venue with the unfixed repay()
    is the LIVE PERMISSIONLESS EXPLOIT under the standing LendingVault public-exposure block. Also verify the vault's
    allowAddress + registerIdentity + registerCredential (holder credentialed; spender allowlisted).
    >> ALLOWLIST DRIFT ON BNB STACK A - INVESTIGATED, RESOLVED BENIGN 2026-10-18 (BnB flagged; my own chain-97 reads
    block 137453668 + source). CHANGE: A's AllowPolicy 0x044951AB allowlist went from {0xFc99, pool} to {0xFc99,
    pool, ROUTER 0xE1053aE1} (AddressAllowed enum = 3, Disallowed 0). Router 0xE1053aE1 = CCIP "Router 1.2.0";
    addressAllowed(router)=true but validate(router)=FALSE (allowlisted-but-uncredentialed). allowAddress(router) tx
    0x62142ba27e1e2b0120a646872c8208bce2e9b9126bcedacf0dcc0ecca661c32e, from 0xFc99 (AllowPolicy owner), sel
    0x08af4d88, status 1. Policy chains UNCHANGED [Allow,Pause,Validator] x3. So #532's "allowlist exactly {0xFc99,
    pool}" is STALE -> correct to {0xFc99, pool, router}.
    NO COMPLIANCE BYPASS (the security crux, proven): an uncredentialed address CANNOT receive/hold MBT - mint(router,
    1) from pool AND transfer(router,0) to=router BOTH revert AT the validator 0xF7e63f6c. The credential gate for
    HOLDERS is intact.
    WHY the router is allowlisted (read from src, RWATokenCCT.transferFrom L249-259): L252 `if (!allowlistPolicy.
    addressAllowed(msg.sender)) revert SpenderNotAllowlisted` = IN-FUNCTION allowlist check on the SPENDER/mover,
    SEPARATE from the engine (comment: "The SPENDER, which the engine structurally cannot screen (V12 #307173)").
    ERC20TransferExtractor produces only from/to/amount (NO spender), so the policy engine screens HOLDERS only. The
    CCIP Router is the spender when it pulls user tokens in ccipSend, so it trips SpenderNotAllowlisted -> needed
    allowAddress; it is NOT credential-screened because a spender is a mover not a holder - BY DESIGN per V12 #307173.
    Movers are allowlist-gated-only; holders are allowlist+credential. The router never holds (validator gates).
    PARITY INVARIANT RESTATED: "every allowlisted HOLDER is credentialed" (router is an allowlisted SPENDER, not a
    holder; the validator independently bars it from holding). The attach no-brick stands (router added later, can't
    hold). RECORD FIX for BnB: #532 allowlist line -> {0xFc99, pool, + router as authorized spender/mover, uncreden-
    tialed by design V12 #307173}. External-exposure note: BNB's spender path is allowlist-gated-only (not credential),
    documented design, not a gap.
    >> PR #541 RATIFIED (diff-vs-ledger, MY SCOPE) at head f6358d6f40aec051df34b066a4a7ad13637cf87b: my allowlist/
    spender correction is in verbatim-accurate and attributed to V&L (allowlist {0xFc99,pool,Router}; every allow-
    listed HOLDER credentialed; Router = authorized spender uncredentialed by design V12 #307173; mint(Router,1)/
    transfer(Router,0) revert at validator = cannot hold; allowAddress tx 0x62142ba2 from 0xFc99). The #532 "exactly
    {0xFc99,pool}" sentence correctly RESTATED as "at that time" (historical, not contradicting). Sanctions framing
    intact ("must not be described as sanctions-screened", L43). No audited/jurisdiction/reserve-gated overclaim.
    SCOPE: I ratify ONLY my verified items; the CCIP LANE-WIRING + DELIVERY claims ("proven both ways", RUN hashes,
    OffRamp, balances, gas overrides) are Lane Wiring/S8's domain, correctly ATTRIBUTED to them, NOT verified by me
    (CCIP delivery is its own gate; "SUCCESS != delivered"). NEW pre-mainnet condition the doc correctly flags: CCIP
    rate limiters DISABLED both directions (joins F1 hot-key + dedicated-cold-issuer on the mainnet-gate list).
    Clear to merge from V&L on the allowlist/spender correction. REMAINING before BNB PUBLIC exposure: explorer verification of chain-97 contracts
    + Ilan's push; and the CCID-on-A D/E/F gate (in progress). POST-VERIFY RESUME
    HOOK (when ES routes the 5 tx hashes): each tx status 1 from 0xFc99; mint+transfer SUCCESS through live [Allow,
    Pause,Validator]+PoR = the live-enforcement proof; burn is self-only; END-STATE baseline = minter(0xFc99) FALSE +
    minter(pool) TRUE + supply 0 + balance(0xFc99) 0. SCOPE: proves live credentialed mint/transfer + PoR + self-only
    burn; does NOT prove live validator REJECTION (stays state-override sim) - docs must say exactly that, and the
    "no real mint/non-zero transfer executed" line in 6.22.1 flips in the same change.
    - BEHAVIORAL (the enforcing proof, policy discriminated by address in the 0xfbdad4f4 wrapper):
        (a) mint(0xFc99,1) from pool 0x2bC9 -> 0x  (credentialed+allowlisted passes full chain; no brick)
        (b) mint(0xdEaD,1) from pool, no override -> revert policy=0x045A3c91 AllowPolicy "address is not on allow
            list" (Allow gates first)
        (c) mint(0xdEaD,1) from pool + override allowList[0xdEaD]=1 (slot 0x5d220cd1...efeab, derived independently
            = BnB's) -> revert policy=0x0AE06A1A THE VALIDATOR "account identity validation failed" => with Allow
            bypassed the VALIDATOR rejects an allowlisted-but-UNcredentialed recipient in-chain. Proves the
            credential gate is REACHED and ENFORCING, not phantom. (Override is no-write; no parity change.)
        (d) transfer(pool,0) from 0xFc99 -> true (both credentialed)   (e) validate(0xdEaD)=false.
    - NO-BRICK holds: allowlist {0xFc99,pool} both credentialed, totalSupply 0.
    ENFORCING DECLARATION LEDGERED: the BNB chain-97 common.kyc credential validator is LIVE on the MBT transfer/
    transferFrom/mint path as of post-attach. Operational note (not a gate failure): allowlist alone no longer
    suffices - a newly allowlisted address without a common.kyc credential will revert at the validator on mint/
    receive (intended enforcement). F1 (0xFc99 concentration) remains a HARD pre-mainnet condition; explorer
    verification of chain-97 contracts remains an outstanding public-exposure condition.

EXPLORER VERIFICATION (public-exposure condition, spans the whole BNB chain-97 deployment - MBT stack AND CCID
stack): the canonical RUNBOOK gate is "explorer-verified AND AI-audited." This gate substituted BYTECODE PROVENANCE
(on-chain runtime == repo-compiled artifact, byte-for-byte) for the deployed==source property, which is a strong
substitute. But explorer verification itself is OUTSTANDING and UNTESTED on chain 97 (per BnB: Etherscan V2 getsourcecode
returns empty name = not verified; the V2 `logs` module returns "Free API access is not supported for this chain" on
the current key; a verify SUBMISSION was not attempted). Before PUBLIC exposure (third parties read source on the
explorer), the chain-97 contracts should be explorer-verified and that path proven. Not a blocker for the shadow
deploy; a condition for the public surface.
F3. Completeness scope: RoleGranted enumerated over the deploy block range (3 grants, all 0xFc99); post-deploy
    RoleGranted not separately swept. Bounded - only DEFAULT_ADMIN (0xFc99) can grant, and live hasRole negatives
    (0xCf30/0xdEaD) are false.

GATE: PASS for the deployed shadow stack. ATTACH and public exposure proceed on this PASS subject to F2 (ATTACH =
its own gate) and disclosure of F1. Nothing public until Ilan's push.

ALLOWLIST PROVENANCE + EXACTNESS, BNB stack A AllowPolicy 0x044951AB857C7e23b5570D4ca11466ACACF861A8 (V&L own reads,
chain 97, 2026-10-22; method notes below). The allowAddress(0xCf30) write that BnB flagged as unattributed is now
NAMED, and the full allowlist is enumerated exhaustively (previously blocked by getLogs limits; resolved via
alchemy_getAssetTransfers external-category on the only authorized caller).
  METHOD: allowAddress(address) is onlyOwner; owner() == 0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c (read live, an
  EOA), so every SUCCESSFUL allowAddress MUST be a top-level external tx from 0xFc99. alchemy_getAssetTransfers
  {fromAddress:0xFc99, toAddress:0x044951AB, category:[external], excludeZeroValue:false, 0x0..latest} returns the
  complete set (4 txs, under the 1000 maxCount, no pageKey = not paginated). All 4 decode to selector 0x08af4d88 =
  allowAddress(address); disallowAddress (0xb7c58d7a) appears in NONE, so no removals. getLogs-based sweeps were
  defeated (Alchemy free = hard 10-block cap even topic-filtered; drpc refuses archival getLogs at 137M;
  publicnode/dataseed prune; Blast dead; Etherscan/BscScan V2 account module is paid on chain 97) - getAssetTransfers
  is the sound substitute because the onlyOwner+EOA property makes the external-tx set exhaustive for successful adds.
  THE 4 allowAddress CALLS (block, tx, arg, role), all from 0xFc99, all status 1:
    1. 136577843  0x3cefdc1951450553b40724e4600544c65a7671a2c73c86c70385828090913ada  0x7cC5c66e0b3EC3aa7106C003ef45D0a1b33e9E08  BurnMint pool
    2. 136577843  0x91c3785dfc2a78eabd59cd9c7ff3b2a596c99e48d79a1429aede3199b3d50339  0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c  issuer (self)
    3. 137451850  0x62142ba27e1e2b0120a646872c8208bce2e9b9126bcedacf0dcc0ecca661c32e  0xe1053aE1857476f36A3C62580FF9b016e8EE8F6F  CCIP Router (the V12 #307173 spender drift, benign)
    4. 137957376  0xe67e27ade57ea50c9e227a13026564b56e629bae5bb44fd80aff4b21a6045c94  0xCf309940276b1e1396AED22aC4e1E7D2E3dd27d6  demo KYC wallet 0xCf30
  => AllowPolicy allowlist (stack A) = {pool, issuer 0xFc99, CCIP Router, 0xCf30}, 4 members, no removals, AS OF this
  enumeration (block ~138344xxx, 2026-10-22 ~22:1xZ). ** SUPERSEDED SAME DAY: a 5TH member, the lending vault V
  0xbdA5e22c, was allowAddress'd at 22:29:37Z (block 138349905) - see the LENDING VENUE section below. Re-enumerated
  via the same getAssetTransfers method: 5 allowAddress, 0 disallowAddress, all 5 live addressAllowed==true. The
  allowlist is now EXACTLY {pool, issuer, Router, 0xCf30, vault V}, 5 members. ** AS-OF discipline: my 4-member relay
  to BnB was correct at its read time and is corrected here + in a follow-up relay.
  The earlier EXACTNESS caveat (could not rule out an unexpected allowlisted address) is CLOSED: the enumeration is
  exhaustive (onlyOwner + EOA owner), so membership is whatever the current getAssetTransfers set says, no more.
  allowAddress(0xCf30) RECORD (answers BnB's who/when/authority question): tx
  0xe67e27ade57ea50c9e227a13026564b56e629bae5bb44fd80aff4b21a6045c94, block 137957376, 2026-10-20T21:22:26Z UTC,
  from/signer 0xFc99 (== owner, the only authorized caller), to 0x044951AB (stack A AllowPolicy), status 1, emitted
  AddressAllowed(0xCf30) (topic0 0x5d20d759..., topic1 ...cf30...27d6). SCOPE/ATTRIBUTION: the on-chain facts (signer
  0xFc99 = owner; timestamp; success; event) are V&L-VERIFIED. The *authorization* behind it is ATTRIBUTED: same
  signer and same day (2026-10-20) as the 0xCf30 credentialing that ES/BnB attribute to Ilan's own per-op
  authorization (registerIdentity blk 137961222 / registerCredential blk 137961227), landing ~3,846 blocks earlier in
  the same owner-key session; I did not read a separate chat authorization for this specific write, I read that the
  same owner key signed it the same day. No longer an unattributed write: it is the owner key under the same
  authority as the rest of stack A.

BNB LENDING VENUE (chain 97): repay() HARD GATE = PASS (bytecode provenance) + vault V FULLY ADMITTED (V&L own
reads, 2026-10-22, block 138350701 @ 22:35:35Z unless noted). Standing #550 obligation: before Ilan's borrow/repay
E2E, prove the DEPLOYED vault's repay() returns collateral to the BORROWER, not msg.sender (the LendingVault.repay()
permissionless-drain class, fixed by PR #517). ES read source LendingVault.sol:217; nobody had compared the DEPLOYED
bytecode. This gate does that.
  VAULT V = 0xbdA5e22c7037898D5C03b835487134F322B82c41 (providerName "Victory Capital"; owner 0xFc99; collateralToken()
  0x3953a90Ad44Dc771E7A1a1Fc4608Bb89f13cA068 = CANONICAL stack-A MBT "Miami Beach Tower"/MBT (ledger-confirmed canonical
  A, engine 0xB917335d); usdc() 0xCE2C962D2d0636Cf3ee54De65B281D5427f31724). Deploy commit stated main d8627c8.
  REPAY() GATE - METHOD = BYTECODE PROVENANCE (the deployed runtime IS what executes, not the source file):
    - Deployed runtime = cast code V = 3979 bytes, metadata tail solc 0.8.24 (0x0008180033).
    - Source repay() @ d8627c8 (git show) pays `collateralToken.safeTransfer(pos.borrower, collateralReturn)` with an
      in-code comment naming the exact exploit ("NOT msg.sender: repay() is permissionless..."); read to END of path:
      USDC pulled from msg.sender, collateral to pos.borrower, emits PositionRepaid(id, pos.borrower, ...). d8627c8 ==
      HEAD 267ff2e == working tree on LendingVault.sol (empty diffs).
    - DEFAULT profile (no via_ir) compile did NOT match (4551 vs 3979 bytes, pervasive diff). WITH via_ir (optimizer
      200, solc 0.8.24) the compile == deployed, 3979 bytes, only 192 differing bytes in 9 clusters: EIGHT 20-byte
      slots each holding usdc (0xCE2C962D, x5) or collateralToken (0x3953a90A, x3) == the two immutables (compiled
      artifact has 0x000..0 placeholders there; live getters confirm the addresses), + ONE 32-byte metadata-hash
      cluster (3936..3967). => deployed executable logic BYTE-IDENTICAL to d8627c8 source via_ir-compiled, modulo
      immutables+metadata. The ENTIRE vault (not just repay()) is the d8627c8 source. REPAY() GATE: **PASS** - the
      deployed repay() returns collateral to pos.borrower.
    - PROVENANCE NOTES: (a) deployed compiled WITH via_ir (default profile has none) - record for explorer verification
      (must verify via_ir-on or it fails, same lesson as the ccid profile). (b) d8627c8 is NOT an ancestor of HEAD
      267ff2e (deploy commit diverges from current branch tip); harmless here because LendingVault.sol is identical
      across d8627c8/HEAD/worktree and the full runtime matches d8627c8. (c) metadata hash differs (local recompile
      paths) = "match" not "exact_match"; runtime logic identical. Behavioral live borrow/repay is Ilan's signed E2E
      (ES routes results); this gate is the pre-E2E static proof.
    METHOD HARDENING (re-run 2026-10-23 answering S8's load-bearing method challenge; NON-CIRCULAR decomposition):
      compared RUNTIME (cast code vs artifact deployedBytecode, not creation bytecode). Masks derived from the
      COMPILER'S OWN output, not from the diff: immutable offsets from the artifact's `immutableReferences` (AST 659
      @753/1186/1946, AST 662 @620/973/1294/2345/2725, each a 32-byte slot), metadata region from the CBOR trailer's
      declared length (last 2 bytes => 51+2). VAULT: 3979 bytes each, 192 whole-string diffs = 160 in compiler-declared
      immutable slots + 32 = the CBOR metadata hash + ZERO elsewhere. So executable bytecode is byte-identical; only
      the metadata HASH differs (NOT a whole-string exact match; also NOT a stripped-trailer compare - 100% of the
      delta is accounted for and none is in opcodes). A zero-residual EXECUTABLE match is only achievable if solc
      0.8.24 + via_ir + optimizer-on + runs 200 ALL match (each changes codegen), so the match certifies the settings
      tuple; profile determined empirically (default no-via_ir does NOT reproduce), not read from the deploy script.
      Immutable slots hold the exact live getter values (usdc 0xCE2C962D x5, collateralToken 0x3953a90A x3) - data
      verified, not dismissed. TESTUSDT 0xCE2C962D ALSO VERIFIED (S8's Q5): deployed runtime 1614 bytes, via_ir/0.8.24/
      200, 32 whole-string diffs = all metadata hash, 0 immutables, 0 residual; TestUSDT.sol byte-identical d8627c8<->
      HEAD. Both deployed contracts == d8627c8 source. GATE STATUS per S8's framing: SOURCE-verified (S8) + BYTECODE-
      verified (V&L, this method) + NOT YET LIVE-DISCRIMINATED (the only repay so far had msg.sender==pos.borrower
      0xCf30, identical under fixed vs exploitable code). ES has a NON-borrower repay staged (Ilan-signed) = the live
      discriminator; V&L will verify it at event level (collateral Transfer recipient == pos.borrower != caller).
    >> LIVE-DISCRIMINATED 2026-10-23 (V&L own event-level reads): the non-borrower repay EXECUTED and PASSED. A SECOND
    cycle ran (nextPositionId=2). Position 1: borrow 0x29fa4e90ef125e0a6cc08a26f9c49325d2e49c27e730600d35cf5681055042f4
    (blk 138481946, 0xCf30 opens position 1, borrower=0xCf30), then REPAY
    0xff31dde9f4053ee7db06bf7caf962081d0de68506fbab2d2d415aab78fa32d5d (blk 138483277, status 1, sel 0xd8aed145)
    SIGNED BY 0xFc99 (NON-borrower; caller != pos.borrower, so it discriminates). Events: USDT Transfer 0xFc99->V
    6.700004 (caller pays debt from msg.sender) + MBT Transfer V->0xCf30 1.0 (collateral returned to pos.borrower
    0xCf30, NOT to caller 0xFc99) + PositionRepaid(id 1, borrower 0xCf30, colRet 1.0). Post-test (blk 138484176,
    15:17:39Z): 0xFc99 MBT = 0 (the caller gained NOTHING), 0xCf30 MBT = 1e18 (borrower got the collateral).
    COUNTERFACTUAL FALSIFIED: the exploitable repay() targets msg.sender (0xFc99), which would have let 0xFc99 walk off
    with 0xCf30's collateral; observed collateral went to the borrower and the caller ended with 0 MBT. ATTRIBUTION:
    signed by owner key 0xFc99, consistent with ES's staged non-borrower test; on-chain facts V&L-verified, Ilan
    go-ahead is ES's to confirm. GATE now FULLY CLOSED on all three legs: SOURCE-verified (S8) + BYTECODE-verified
    (V&L, executable == d8627c8, exploit absent) + LIVE-DISCRIMINATED (this tx, non-borrower repaid, collateral to
    borrower not caller).
    >> CONFIRMING SECOND RUN 2026-10-25 (position 3, V&L own event-level read): S8 staged a pre-tx entry for "the first"
    non-borrower repay; it is actually the SECOND (position 1 above was the first, 2026-10-23). By the time V&L read
    (block 138902799, 2026-10-25T19:37:20Z) Ilan had already executed it and position 3 was CLOSED with end-state ==
    S8's pre-stated FIX-WORKS column (0xCf30 MBT 11.0, 0xFc99 MBT 0, position closed). V&L found the tx (newest MBT
    V->0xCf30) and verified at EVENT level, not from the end-state: tx
    0x7416de92cc4d2742743d43abf4aea22a0302511aceef9d3b2ebdb82cca4d175b (block 138902742, status 1, repay sel 0xd8aed145);
    signer 0xFc99 (NON-borrower); MBT collateral Transfer by topic position topic1(from)=vault 0xbdA5e22c,
    topic2(to)=0xCf30 (BORROWER) 1.0, NOT the caller; USDT 0xFc99->vault 6.700002; PositionRepaid(id 3, borrower 0xCf30,
    colRet 1.0); vault codesize 3979 (patched). The prediction was stated BEFORE the run (falsifiable) and matched. So the
    #517 fix is proven LIVE on the deployed BNB vault with a non-borrower caller TWICE (positions 1 and 3), decoded by
    topic position; 0xFc99 gained nothing either time. BNB lane's last item closed.
    >> INDEPENDENT CORROBORATION (S8, 2026-10-23): S8 ran the same event-level read on 0xff31dde9.. and it AGREES -
    MBT Transfer logIndex 2, topic1 = vault 0xbdA5e22c (FROM), topic2 = 0xCf30 (TO = borrower), data 1e18; caller
    0xFc99 nonce 153. TWO independent instruments, same reading, parties distinct. SECOND in-tx naming (V&L): the
    vault's OWN PositionRepaid event (distinct contract/event from the ERC20 Transfer) names borrower 0xCf30 in the
    same tx - recipient named twice across two contracts' events in one tx.
    METHOD NOTE (S8's, worth carrying): the vault address appears in BOTH the borrow and the repay Transfer events -
    as topic1 (FROM) in the repay-out, topic2 (TO) in the borrow-in. A grep that matches the right VALUE in the wrong
    POSITION is a false hit; TOPIC ORDER is the discriminator. Read the full decoded log (topics[1]=from, topics[2]=to),
    never a value-only filter. [[fb_transfer_topic_position_is_direction]]
    CLAIM LANGUAGE AUTHORISED (S8, the claims authority) - the ONLY sayable form: "On the deployed BNB LendingVault
    0xbdA5e22c, a non-borrower repayment returned the collateral to the borrower, observed on chain 2026-10-23 (tx
    0xff31dde9..). The #517 permissionless-repay defect is not present in the deployed bytecode." NOT sayable: anything
    implying the vault is AUDITED; "KYC'd"/"verified" (use "admitted as an operational holder"); anything about
    jurisdiction or accreditation.
  VAULT V ADMISSION (corrects BnB's 22:17Z "NOT admitted" - admission COMPLETED 22:29Z, V&L own reads): V is now a
  FULLY ADMITTED holder on stack A. Three txs from 0xFc99, all status 1, clustered 2026-10-22:
    allowAddress(V)      0xf2639bac7752d0b3fa3740f51a8abbc40c2d40c96dc04dbe6cc1a418a04a04b6  blk 138349905  22:29:37Z  (sel 0x08af4d88, arg=V)
    registerIdentity(V)  0x5e14dfa750669bb7f9e041ba5f9f0d0d942e2860aad0651c07be5df62b424fed  blk 138349910  22:29:39Z  (sel 0xf176f9a1, V in calldata; to IR 0x4EcF0cc7)
    registerCredential   0xbffac5835c5e8a72323d686574a494b2e6101c2d347adf1e2b65f241095f7493  blk 138349918  22:29:43Z  (sel 0x8ab73618, carries ccid(V) 0xd552bc75..f033 + common.kyc 0x455af80b; to CR 0xc8567316)
  LIVE STATE: addressAllowed(V)=true; getIdentity(V)=0xd552bc7516d5a8c382f82abd20dd8ea691b8f977d5b8e0037a36d0ae1cc5f033
  == my recompute keccak(ob.ccid.v1||V) == BnB's ccid(V); validate(V,0x)=true on validator 0xF7e63f6c969acDF047bb3841F856288415a9d4B4
  (policy chain for token transfer = [Allow 0x044951AB, Pause 0x388078c3..8aec, Validator 0xF7e63f6c]; validator
  getCredentialSources(common.kyc)=[(IR 0x4EcF0cc7, CR 0xc8567316, 0x0)]). V passes the full [Allow,Pause,Validator]
  chain.
  LIVE BORROW/REPAY E2E - VERIFIED at EVENT level (V&L own reads; BnB routed it but had NOT seen the repay tx, I found
  it; I read the events, not just end-state balances). Borrower/signer = 0xCf30 (demo KYC wallet). Position struct =
  (borrower, collateralAmount[18], borrowedUSDC[18 on this venue], openedAt, active).
    BORROW: approve 0x41e2b836f7cdd9f80dfc274d4c19633d3c18c69753aa93051dd67705aa60e9d2 (blk 138352668, 0xCf30->MBT,
    sel 0x095ea7b3) + borrow 0x6819abd759ae42b4d9dfb21a35a2d18c5cadf2b12b726724f52ef54dddbd3542 (blk 138352683,
    0xCf30->V, sel 0xc5ebeaec), both status 1. Events: MBT policy-engine event (0xb917335d) + MBT Transfer 0xCf30->V
    1e18 (collateral in, credential-gated) + USDT Transfer V->0xCf30 6.7e18 (loan out) + PositionOpened(0, 0xCf30,
    1e18, 6.7e18).
    REPAY: tx 0xa9d6539e7233acb27108999cc53697e1e0fb4e772d91e6e50af8cf1d17b6dc8f (blk 138469909, from 0xCf30, to V,
    sel 0xd8aed145, status 1). Events: USDT Transfer 0xCf30->V 6.700392707191780821 (repay pulled from msg.sender) +
    MBT policy-engine event (0xb917335d: the return leg is ALSO credential-gated) + MBT Transfer V->0xCf30 1e18
    (collateral returned TO THE BORROWER) + PositionRepaid(positionId 0, borrower 0xCf30, repaid 6.7003927e18,
    collateralReturned 1e18). Interest 0.000392707 USDT cross-checks rate 350bps x principal 6.7 x elapsed 52812s.
    LIVE END-STATE (my read, block 138481118, 2026-10-23T14:54:43Z): positions(0)=(0xCf30,0,0,1792762239,false)
    CLOSED; MBT bal(0xCf30)=1e18 / bal(V)=0 / supply 1e18; USDT bal(V)=100000.000392707e18 / bal(0xCf30)=0.999607e18;
    totalBorrowed 0, totalCollateral 0.
    DISCRIMINATING-EVIDENCE NOTE: the repay signer == the borrower (0xCf30), so this live run does NOT by itself
    distinguish "pays caller" from "pays pos.borrower" (both pay 0xCf30). The discriminating proof that repay() pays
    pos.borrower specifically (the #517 fix) is the BYTECODE-PROVENANCE gate above (deployed runtime == d8627c8 source
    where the collateral transfer targets pos.borrower). This E2E CONFIRMS the happy path works end-to-end through the
    live [Allow,Pause,Validator] compliance stack on both the in and return legs, with correct interest accrual and
    position close. Both gates together: repay() returns collateral to the borrower, proven by code identity and
    confirmed live.

LENDINGVAULT #517 FLEET CENSUS (chain-wide, 2026-10-23, V&L own reads): is the repay()-pays-msg.sender defect present
in the OTHER deployed vaults? S8's census found 16 vaults all at runtime size 4534 bytes (vs the FIXED BNB vault's 3979
via_ir / 4551 default). V&L ran the bytecode-provenance method against the OLD source to turn the size-fingerprint
inference into proof.
  ANCHOR: pre-#517 source = commit c2c7968~1 (parent of the #517 fix c2c7968). Confirmed buggy: LendingVault.sol:200
  `collateralToken.safeTransfer(msg.sender, collateralReturn)`. Compiled DEFAULT profile (no via_ir), solc 0.8.24,
  optimizer on, runs 200 => deployedBytecode EXACTLY 4534 bytes. So 4534 == the pre-#517 default-profile build size
  (17 below the fixed-default 4551: the CALLER opcode replacing the pos.borrower storage-read).
  NON-CIRCULAR MASK: immutable offsets from the pre-517 ARTIFACT immutableReferences (AST 659 @755/1860/3344, AST 662
  @421/1378/2005/2166/2539/3138, 32B slots) + CBOR metadata trailer length. Masks from the producer, not the diff.
  UNIVERSE = 18 (NOT 16): V&L's first pass had 16 (from defi.ts); S8 had 17; the UNION is 18. ENUMERATE FROM
  docs/public/06-contracts.md, NOT defi.ts (the UI list hides delisted-but-live vaults). The two delisted Amoy vaults
  absent from defi.ts (06-contracts.md L705-706, delisted 2026-09-06 behind the down Sepolia-Amoy lane): Atlas AVR
  0x34aE4bc3850651ad92036de01e530be943309218 (the LARGEST remaining exposure, ~4 AVR ~$1k) and Atlas TGLP
  0x21389D0c8ABAA1C555516a495d3558CE48a5221A (empty). Minato 0xfbaaf31c (commented-out in defi.ts) was in V&L's 16, S8's miss.
  RESULT - 18 of 18 CONFIRMED pre-#517, ALL residual 0 vs the pre-517 compile, every endpoint chain-id-GUARDED before the
  read (=> executable code IS the pre-#517 exploitable source; all identical to each other - each == same anchor; Meridian
  vs Atlas share even the metadata hash = one build artifact deployed fleet-wide):
    Sepolia (11, 11155111): 0x71635c5f(Meridian) 0xf9781594 0x3a412614 0x14044A1B 0x35756Be4 0xd5B3b6BB 0x9533bb01
      0x21D41012 0x566AdFa7 0x839e7F5d 0x53DE2FDf. Amoy (80002): 0xf48f2683(Atlas MBT, 4-way twin - read on Amoy),
      0x34aE4bc3(AVR), 0x21389D0c(TGLP). Shibuya 0x3F1d5141. Sonic (14601): 0x49f1aa7C(Nova). Plume (98867): 0x8f1bb564.
      Minato (1946): 0xfbaaf31c.
  => the #517 repay defect IS PRESENT in the deployed bytecode of all 18 vaults (PROVEN, not inferred). RPCs for
  Sonic/Plume/Minato = chains.ts L643-664 public defaults.
  CORRECTION - NOT "PERMISSIONLESS" (propagated, was inherited from the memory label): the bug sends the COLLATERAL (an
  ACE-gated token) to msg.sender, so the thief must be allowlisted+credentialed for that chain's collateral to receive it;
  a non-member caller reverts 0xfbdad4f4 at AllowPolicy (BnB live control 0x..bEEF; mechanism code-evident from pre-517
  L200). The attacker set is CLOSED to gated membership. This is a SIDE EFFECT of a gated collateral, NOT a vault access
  control, and must NEVER be written up as compliance protecting the vaults.
  REMEDIATION - borrower-closes-own (bug pays msg.sender, so caller==borrower is safe) empties the vault and the defect
  becomes worthless; no redeploy. HOLDS on Sepolia/Amoy/Plume. ONE EXCEPTION - SONIC FAILS (V&L verified + BnB): Nova's
  collateralToken() = wMBT v2 0x670c3a58, paused()==TRUE (V&L read, chain 14601); so repay() reverts ContractPaused
  (0xab35696f, BnB) - nothing moves for borrower OR attacker, the ~1 wMBT is LOCKED, and the vault binds collateral
  immutably, so Sonic specifically needs the token UNPAUSED or a redeploy. All testnet; nothing signed. BnB proved
  BEHAVIOUR (fork), V&L proved CODE + the 18/18 census + the Sonic pause; neither redundant.
  OPEN POSITIONS + BORROWERS (V&L reads, for Ilan's "are these ours" - repaying a genuine third party's position = WE take
  their collateral = the exploit, not a rescue): recorded in the coordination board; material holders: Meridian 6 MBT ALL
  = 0x91F5F78d; AVR ~4 (0x6Ab39C09 2 + 0x08Cf2bfB 2); Amoy Atlas MBT 3 MBT = 0xCf30 (pos 125, OURS);
  Sonic Nova 1 wMBT = 0xCf30 (OURS, but LOCKED by the pause); Plume all dust. So both 0xCf30 holdings are ours; the
  material NON-0xCf30 exposures to classify are Meridian 6 MBT (0x91F5F78d) and AVR 4 (0x6Ab39C09, 0x08Cf2bfB). S8's
  five queried addrs all appear as borrowers but only 0x91F5F78d holds MATERIAL collateral (6 MBT Meridian); the other
  four (0x6193DaFB, 0x950b521F, 0x075abCa1, 0x44DD784A) are dust-only. Full per-vault census on the coordination board;
  which borrowers are OURS is Ilan's call (repaying a genuine third party's position = taking their collateral).
  BYTECODE GATE - WHAT IT DOES NOT PROVE (S8's boundaries, recorded next to the 18/18 closure so it is not over-read):
  (1) code presence != exploitability per vault - Sonic carries the identical defect but is NOT exploitable (collateral
  paused); only BnB's fork tests prove it FIRES (4 of 5 collateral-holding vaults). (2) the 13 empty vaults are harmless
  only because totalCollateral==0; each is exposed the instant anyone borrows - "empty" is a STATE, not a remediation.
  (3) the attacker-set bound is a property of the gated COLLATERAL token (the AllowPolicy revert), not of the vault.
  CONTESTED-PROVENANCE NOTE (THE ONE HOME for 0x6Ab39C09, reconciling the earlier "OB-originated" wording up-file):
  V&L has NOT verified 0x6Ab39C09 (AVR pos 0, 2 AVR) as OB-owned. The "OB-originated" attribution is an UNVERIFIED
  S8/S2 transcript claim and is DISPUTED 2026-10-23: its sole evidence is "allowAddress tx 0xc27971f5" (a 4-byte
  fragment, one line in the tree, not lookupable), no desk's board records funding it, and it holds 0.2825 POL + 2 FREE
  AVR beyond the position. Custody unresolved / possibly unrecoverable. POSTURE: treat 0x6Ab39C09 as NON-ours /
  to-classify, NOT settled-ours; likewise 0x91F5F78d (Meridian 6 MBT) and 0x08Cf2bfB (AVR 2). Only the 0xCf30 holdings
  are verified ours.
  RECOVERY WARNING (live tonight): a "conditional go" to have 0xFc99 REPAY the AVR positions and TAKE the collateral
  reached V&L only via BnB relay, NOT direct from Ilan - a peer relay is NOT Ilan's authorization. That recovery tx is
  ON-CHAIN INDISTINGUISHABLE FROM THE EXPLOIT this ledger documented. V&L will NOT certify 0x6Ab39C09 provenance as
  ours, and records: any such recovery needs (a) Ilan's DIRECT, named authorization and (b) provenance that the
  position is genuinely ours, both RECORDED HERE BEFORE the tx (position-96 shape), else afterwards it reads as insider
  exploitation of a defect we documented 11 days earlier. S8 has refused to supply the pos-0 confirming condition on the
  same grounds; V&L concurs. Nothing authorized, staged, or signed.
  >> FUNDING TRACE 2026-10-23 (V&L own reads, Amoy, alchemy_getAssetTransfers native+erc20 - the discriminating
  instrument BnB/S8 asked for; a getLogs sweep would MISS the native POL = false negative). This CORRECTS the posture
  above and several desks:
    0x6Ab39C09 (pos 0) INCOMING: 4 AVR from 0xFc99 (tx 0x3b3dd7bd, blk 44840889) + 0.3 MATIC from 0xFc99 (native, tx
      0xe20c02ed) + 325 USDC from the AVR vault 0x34aE4bc3 (the borrow payout). Its 2 AVR collateral TRACES TO 0xFc99.
    0x08Cf2bfB (pos 5) INCOMING: 2 AVR from 0xFc99 (tx 0xd2b8088e) + 2 MATIC from 0xFc99 (tx 0xcfe1a73b) + 325 USDC from
      the vault. Matches ES's cited funding txs exactly.
  => ASSET CUSTODY: BOTH positions hold AVR sent from 0xFc99 (ours). CORRECTIONS: (1) the "absent funding record" for
  0x6Ab39C09 (ES zero-record / no board entry) was a FALSE ABSENCE - the funding happened on-chain from 0xFc99, just
  unrecorded on any board; the chain is authoritative. (2) S8's point stands that Ilan's "not in my MetaMask / not sure
  how to see these accounts" is NON-DISCRIMINATING (a session-generated BIP-39 wallet would never be in MetaMask whoever
  owns it) - recorded as SILENT, not a negative. (3) pos 5 is OURS (0xFc99-funded, ES-recorded, chain-reconciled),
  contradicting an earlier "not ours" read. SO: on asset custody both positions are ours to recover; the OPEN question
  is now KEY CUSTODY / METHOD, not ownership: if the borrower key is retrievable (S8: pos 5 key is host-side on the
  ob-demo-exec box; pos 0 key uncertain, Ilan "not sure how to see"), the wallet SELF-CLOSES (caller==borrower, NO defect
  invoked) - the clean route; only if the key is truly unrecoverable does 0xFc99-on-behalf (exploit-shaped) arise.
  STILL: nothing authorized against the reconciled facts, nothing staged, nothing signed - Ilan is being re-asked with
  the corrected trace because his quick answers conflicted with the chain.
  >> KEY-CUSTODY AMENDMENT 2026-10-23 (S8 correction + Ilan direct; do NOT record "keys gone" as established): the pos-0
  key custody is UNRESOLVED, with TWO live hypotheses, NOT the settled "cast wallet / key lost" an earlier draft implied.
  (a) Ilan, direct to V&L: "0x6Ab39C09 not in my MetaMask... possibly a cast wallet created in the terminal while
  building... if so I don't have the key." (b) S8 correction: "NOT IN MY METAMASK" IS NON-DISCRIMINATING - MetaMask has
  HIDE, not archive/delete; HIDDEN accounts are absent from the account list entirely and a glance cannot see them; an
  SRP-derived account can never be deleted and always RE-DERIVES at its fixed index. So the null came from an instrument
  that could not see the thing (same false-absence family as the Sonic protocol-35/36 and the \b-grep-returns-0 cases).
  Ilan is now checking the HIDDEN list + running the deterministic sequential-derivation check. OUTCOME-GATING: if either
  AVR wallet surfaces as a hidden/derivable SRP account, BOTH positions SELF-CLOSE (caller==borrower, no defect) and the
  0xFc99-on-behalf path is never needed; only a genuinely non-derivable, non-host-side key forces the exploit-shaped
  fallback. (The pre-tx ledger entry was STAGED pending Ilan's method decision - SUPERSEDED, see FINAL DISPOSITION
  below: Ilan wrote the recovery off; entry RETIRED.) CEO DIRECTIVE 2026-10-23 (Ilan verbatim, recorded):
  "No one is allowed to cast terminal wallets. All wallets for testing or anything else must come from my Metamask" -
  `cast wallet new` BANNED, no testnet exception; GATE: no row in SESSION-COORDINATION/WALLET-REGISTRY.md (naming the
  derivation index) => no funding/allowlist/credential/borrow. The AVR pair and the 11 unknown-custody borrowers are
  the legacy pre-directive wallets this cleanup addresses.
  >> FINAL DISPOSITION 2026-10-23 - AVR RECOVERY CLOSED / WRITTEN OFF (Ilan's decision, after confirming the wallets are
  not in his MetaMask and the clean self-close path did not open). Ilan verbatim: "Leave it. I do not want to spend
  another minute on it if we don't have to." and "We should learn from this chaos and NEVER let it happen again." STATE:
  both Amoy AVR positions (pos 0 borrower 0x6Ab39C09, pos 5 borrower 0x08Cf2bfB, ~2 AVR each) stay OPEN; the ~4 AVR
  remains in vault 0x34aE4bc3, DELIBERATELY ABANDONED (not an oversight). No tx was staged or signed; the exploit-shaped
  0xFc99-on-behalf path is NOT taken. REVERSIBLE BY CONSTRUCTION: collateral does not move, positions stay open, so
  nothing is foreclosed. Any future session that rediscovers this collateral must READ THIS DISPOSITION AND STOP - do not
  re-open. Value at stake is zero (testnet AVR); the deciding factors were that the key search closed negative (removing
  the clean self-close) leaving only the defect path, against a permanent on-chain artifact for zero value. S8 recorded a
  REVERSAL (recommended recovery AM, against it PM) on the same facts. The pre-tx entry above is RETIRED.
  SESSION-COORDINATION/WALLET-REGISTRY.md carries the same disposition.

JURISDICTION CLAIMS GATE (V&L-owned, opened 2026-10-23): ES has deployed SEVEN jurisdiction validators, one per EVM
chain, reported INERT (deployed, NOT attached to any engine, enforcing nothing; runtime keccak byte-identical across all
seven, owner 0xFc99, restrictedCount 4). STANDING GATE: NO jurisdiction-enforcement claim may be made in ANY register
(docs, deck, investor materials, UI, chat) until V&L verifies the DISCRIMINATING PAIR on a WIRED stack:
  (A) a VIOLATING transfer (sender/recipient in a restricted jurisdiction) REVERTS at the jurisdiction validator, AND
  (B) a SAME-SHAPE COMPLIANT transfer (differing ONLY in the jurisdiction attribute) PASSES.
BOTH legs, same stack, same tx shape. One leg alone is NON-DISCRIMINATING: a revert can come from any other policy in the
chain (Allow/Pause/credential), and a PASS is what an INERT validator gives EVERYONE (see [[fb_inert_instrument_pass_means_nothing]],
[[fb_bare_mustfail_nondiscriminating]], [[pol_never_claim_jurisdiction]]). The revert leg must decode to the jurisdiction
validator's OWN revert, not a neighbour policy's ([[fb_instrument_must_name_its_subject]]). Until attached+wired on at
least one chain and this pair passes with V&L's own live reads, the posture is: validators EXIST but enforce NOTHING, and
"jurisdiction-gated/enforced" is UNSAYABLE.
  >> INERT-BASELINE VERIFIED 2026-10-23 (V&L own reads, all 7 chains, each endpoint chain-id-GUARDED before the read).
  The 7 validators (S8-routed, re-read by me): Sepolia 0x8BB6ba01, Amoy 0x7452dAbF, Minato 0xB1a91f49, Plume 0xB124990d,
  Shibuya 0x6DBCC340, Base Sepolia 0xb48b0Ed1, BNB 0x67903F72. ALL SEVEN: codesize 1446, owner()=0xFc99, runtime keccak
  IDENTICAL = 0xe43f9978601d3c27360abf7bad97180617c330b3e0afcef6952733b1727fef5a (same inert bytecode fleet-wide).
  TWIN LANDMINE: Base Sepolia 0xb48b0Ed1 is a 3-way cross-chain twin - on Shibuya the same hex is PolicyEngine 1.0.0
  (owner() reverts), on BNB a stack-B TransferExtractor; read ONLY on 84532 (there typeAndVersion() reverts). Read on
  the wrong chain => false "registry wrong" conclusion.
  NOT-ATTACHED proven by V&L on 4 chains (transfer selector 0xa9059cbb; validator ABSENT from the policy chain, array
  NON-EMPTY = positive control the getter works so absence is real): BNB (engine 0xB917335d/token 0x3953a90A -> [Allow
  0x044951AB, Pause 0x388078c3, CREDENTIAL-validator 0xF7e63f6c]; jurisdiction 0x67903F72 absent); Sepolia (engine
  0xd4b9F980/token 0x044951AB -> 0x8BB6ba01 absent); Base Sepolia (engine 0xa1a58d40/token 0x2aedDb48 -> 0xb48b0Ed1
  absent); Amoy (engine 0xfefA774d/token 0x71A8e443 -> 0x7452dAbF absent). S8 separately proved BNB+Sepolia across all 3
  selectors (transfer/transferFrom/mint). NOT-ATTACHED on Minato/Plume/Shibuya NOT yet read (need each chain's
  engine+token; derivable via vault.collateralToken()->getPolicyEngine()).
  STATUS: inert-baseline VERIFIED (7/7 identical, owner 0xFc99, deployed); NOT-ATTACHED verified 4/7 (+ S8's 2 across 3
  selectors). The DISCRIMINATING PAIR still CANNOT run - NOTHING is wired (no jurisdiction validator attached to any
  engine on any chain). "jurisdiction-gated/enforced" remains UNSAYABLE. Opens only when a validator is attached on some
  chain AND the pair (violating REVERTS at the validator's own revert + same-shape compliant PASSES) is read live by V&L.
  >> OBJT ISOLATED TEST STACK VERIFIED 2026-10-25 (V&L own Sepolia reads, block 11993880, CCID Jurisdiction Phase A step
  1, ES-staged / Ilan-signed 0xFc99, deploy block 11993828). ISOLATED stack, all addresses distinct from live and live
  MBT UNTOUCHED (live Sepolia engine 0xd4b9F980 route for token 0x044951AB still 6 policies, jurisdiction validator
  ABSENT; live MBT totalSupply 1053.5 per S8). OBJT: token 0xD0c3e3d1 (symbol OBJT, 18 dec, supply 0), engine 0x0208ABb8,
  idReg 0x4C9244e7, credReg 0xC337e48d, allow clone 0x86b640B4, pause clone 0xD0c03C9b, validator clone 0xC8d57b25.
  CLONE TRAP handled (S8 flag): the policy "instances" are EIP-1967 proxies (runtime SLOADs impl slot 0x360894a1..382bbc),
  NOT EIP-1167 and NOT the logic - so codesize (130) / keccak of the CLONE prove nothing; resolve the IMPL from storage
  and verify THAT (verify the thing, not the pointer). Resolved impls: allow clone -> 0x62f76de4 "AllowPolicy 1.0.0";
  pause clone -> 0x9a95f849 "PausePolicy 1.0.0"; validator clone 0xC8d57b25 -> impl 0x8fd97502
  "CredentialRegistryIdentityValidatorPolicy" (codesize 8601, keccak 0x1784ae2b) - NOT a standalone JurisdictionDataValidator.
  WIRED: OBJT engine getPolicies(OBJT, transfer/transferFrom/mint) = [allow 0x86b640B4, pause 0xD0c03C9b, validator
  0xC8d57b25] on all three - so unlike the 7 inert JurisdictionDataValidators, this validator IS attached in its stack.
  MECHANISM: jurisdiction here is a CREDENTIAL check, not the standalone validator. The OBJT validator's credential
  sources (both on the isolated registries): common.kyc 0x455af80b -> (idReg 0x4C9244e7, credReg 0xC337e48d, 0x0);
  ob.jurisdiction 0x25a3fab5 -> (idReg 0x4C9244e7, credReg 0xC337e48d, EXTRA 0x8BB6ba01) - and 0x8BB6ba01 IS the Sepolia
  JurisdictionDataValidator from the inert baseline (keccak 0xe43f9978), wired as the ob.jurisdiction source's logic slot.
  So my 7-validator baseline DOES connect: 0x8BB6ba01 is the jurisdiction-value logic, reached via the credential path.
  DISCRIMINATING-PAIR REQUIREMENT SHARPENED (why one revert is non-discriminating here): because jurisdiction flows
  through the one CredentialRegistryIdentityValidatorPolicy, a "restricted jurisdiction" revert and a "credential absent"
  revert share a reason string. SOURCE-VERIFIED BY V&L 2026-10-25 (upgraded from S8's relay): JurisdictionDataValidator
  .validateCredentialData is a non-reverting VIEW returning bool (false=block only on a decoded restricted ISO-3166 code
  [testnet denylist 643/408/364/192], true otherwise incl. absent/garbled) - it carries NO reason; and the policy that
  DOES revert, CredentialRegistryIdentityValidatorPolicy.run(), has exactly ONE failure reason for all causes:
  PolicyRejected("account identity validation failed"). So a rejection cannot name its cause (jurisdiction vs missing/
  expired credential). So the pair MUST be TWO holders BOTH holding a valid common.kyc AND a valid
  ob.jurisdiction credential, differing ONLY in the jurisdiction VALUE: allowed-jurisdiction mint/transfer PASSES,
  restricted-jurisdiction mint/transfer REVERTS. Credentialed-vs-uncredentialed does NOT discriminate jurisdiction. The
  revert must decode to this validator's own revert, and the two fixtures must differ in nothing but the jurisdiction
  attribute. V&L PAIR-VERIFICATION REQUIREMENT (CCID, 2026-10-29): the BLOCK fixture's ob.jurisdiction credential must
  DECODE to a CLEAN restricted ISO code (643 etc.) - a garbled/oversized/absent value fails OPEN (JurisdictionDataValidator
  returns true on undeterminable, by design, ACE views must not revert), so a malformed BLOCK credential would wrongly
  PASS and the negative leg would prove nothing. At fire time V&L reads the BLOCK holder's credential DATA and confirms it
  decodes to a clean restricted code, not merely that the tx reverted. STATUS: stack VERIFIED + WIRED + ISOLATED; the
  discriminating pair has NOT run; "jurisdiction-enforced"
  stays UNSAYABLE until V&L reads that both-credentialed, jurisdiction-only-differs pair live (Ilan-signed, naming
  0xC8d57b25). Design-prohibits attaching to live MBT until the no-ob.jurisdiction holder count reads zero live.
  >> ob.jurisdiction IS A REQUIRED SOURCE on this instance - V&L own reads 2026-10-25 (block 11993939), independent of
  S8's deduction: wallet 0xedB92987 (NOCRED) has getIdentity==its ccid (identity registered), getCredentialTypes ==
  [common.kyc 0x455af80b] ONLY (ob.jurisdiction 0x25a3fab5 ABSENT), common.kyc not expired - yet validate(0xedB92987)
  = FALSE (0xdEaD control also false). Since JurisdictionDataValidator returns TRUE on an absent ob.jurisdiction value,
  the FALSE cannot be the jurisdiction layer; it is the credential-EXISTENCE gate => ob.jurisdiction is REQUIRED.
  TWO-LAYER MODEL (what makes the one reason string inevitable): (1) credential-existence layer (the policy): requires
  BOTH common.kyc AND ob.jurisdiction present+valid; ABSENT either -> revert PolicyRejected("account identity validation
  failed"). (2) jurisdiction-VALUE layer (JurisdictionDataValidator, 0x8BB6ba01 third slot): given a PRESENT credential,
  blocks only when the data decodes to a restricted ISO-3166 code; ALLOWS on undeterminable/garbled (fail-open). So with
  ob.jurisdiction required, an ABSENT credential is blocked by layer 1 (NOT fail-open); only a PRESENT-but-undeterminable
  VALUE sails through layer 2 (an issuer-side concern). Both block-causes emit the single reason string, so only the
  both-credentialed, value-only-differs pair discriminates.
  CLAIMS BOUND (V&L view, pol_never_claim_jurisdiction + proxy!=property): even a clean pair proves only that THIS
  isolated OBJT test token on Sepolia testnet rejects a holder whose ob.jurisdiction CREDENTIAL decodes to one of four
  TESTNET-PLACEHOLDER (not counsel-vetted) restricted codes, and admits an otherwise-identical allowed-code holder. It
  does NOT prove residency screening (enforces the credential's stated value, not verified residence; issuer-dependent),
  does NOT cover a present-but-garbled value, and does NOT apply to live MBT (no jurisdiction validator on the live route).
  Sayable only after the pair lands green, and only in those bounds.

OBBorrowHelperV3 (Amoy 0xB6F376F14cC84611aCD013F7F9D2B2BD1Da17AC2, chain 80002): DEPLOYED == FIXED SOURCE, V&L own
reads 2026-10-28 (my gate becomes a CONFIRMATION; fix is already live + wired, no deploy to gate). BYTE-IDENTITY: deployed
runtime (block 51420942) = 1605 bytes, whole-runtime keccak 0xa865c1675619017238a456ef452b8c861fd14d85a564a68d33a29eb61a040f6e
== the keccak of a FRESH compile of ob-agent-network/contracts/src/OBBorrowHelperV3.sol at the repo default profile (solc
0.8.24, optimizer on, runs 200, via_ir=FALSE, evm cancun). NO immutables, so the whole-runtime keccak IS the identity test
(metadata CBOR ipfs hash included in the match => identical source bytes + compiler + settings; stronger than metadata-strip).
So the scoped-allowance fix IS present on chain. WIRING (byte-identity != wiring): S8 read getCredential on CredentialRegistry
0x94d6257c, actionSchemas[0].expectedTarget = 0xB6F376F1 (the helper), selectorHash 0x4dba62ff, expectedAsset 0x71a8e443,
maxPerOp 5e18/maxPerWindow 1.5e19, with controls (revocationRegistry present, 0xdead absent, bogus typeId reverts
CredentialNotFound 0x6eff7b4e, stale 0x903b8667 absent). WIRING LEG NOW V&L-REPRODUCED 2026-10-28 (block 51421716):
getCredential(ccid 0xc27694aa.., typeId 0x28812a44..) on CredReg 0x94d6257c returns 704-byte/22-word credentialData =
abi.encode(DelegationDataV3), DYNAMIC so w00==0x20 offset (decode-trap: fields shift one word; sanity gates w05
spendWindowPeriod==3600 and w06 revocationRegistry==0x1efa0af5.. both hold). expectedTarget is at WORD 12 (positional,
not merely present in the blob) == 0xb6f376f1..17ac2 = the deployed helper; w13 selectorHash 0x4dba62ff, w14 expectedAsset
0x71a8e443, w19 maxPerOp 5e18, w20 maxPerWindow 1.5e19. Controls fired by V&L: 0xdead..beef ABSENT, stale 0x903b8667
ABSENT, revReg PRESENT, bogus typeId REVERTS 0x6eff7b4e. So BOTH legs (byte-identity + wiring) are independently
V&L-verified; byte-identity is three-way (ES/S8/V&L, keccak+sha256, 0 immutables).
  VOIDED FINDING: S8's earlier "two error selectors absent from the deployed bytecode => scoped-allowance fix missing" is
  WRONG and withdrawn - it rested on an invalid absence test. TWO METHOD LESSONS ADOPTED into the V&L procedure (S8 is
  cataloguing them; the shared MEMORY.md false-absence catalog covers it):
  (1) A 4-BYTE SELECTOR GREP IS AN INVALID ABSENCE TEST. In this one contract/build, CollateralTransferFromFailed 0xf7f59100
      and VaultCallFailed 0x375bdfbc are ABSENT at 4 bytes though both are in the ABI and revert in source: a selector ending
      in a ZERO byte is emitted as PUSH3+shift (the 4-byte form never appears, ~1 in 256, applies to FUNCTION selectors too),
      and an error with a dynamic param is built off a non-literal path. Use whole-bytecode + metadata hash for IDENTITY, and
      the ABI or an actual revert for PRESENCE of behaviour - never a 4-byte grep.
  (2) A BYTECODE SIZE DELTA IS NOT A SOURCE DELTA. 1605 (via_ir=false) vs 1457 (via_ir=true) is the SAME source under
      different settings. Read metadata.settings.viaIR + optimizer out of each artifact JSON before treating a size
      difference as a code difference (joins [[ref_determine_compile]]).
  >> REOPENED 2026-10-29 - GATE NOT CLOSED, a THIRD leg none of us checked is RED (S8 caught it; V&L reproduced). The two
  legs above (byte-identity + credential-wiring) are about the CODE; the MBT TOKEN-ADMISSION of the helper is a separate
  leg and it FAILS. borrow() step 1 is transferFrom(msg.sender, address(this), collateralAmount) (src L133) so the helper
  RECEIVES the MBT collateral (credential expectedAsset 0x71a8e443 = Amoy MBT confirms collateral IS MBT) => the helper is
  a transfer party and MBT admission applies to it. V&L OWN READS, chain 80002 block 51422090, MBT AllowPolicy 0x5fe9d900
  / validator 0xCAEe7962: v3 helper 0xB6F376F1 addressAllowed=FALSE, validate=FALSE. CONTROLS BOTH DIRECTIONS (so RED is a
  SUBJECT fault, not instrument): v1 helper 0x903b8667 true/true, v3 wallet 0x6d297af0 true/true, 0xCf30 true/true, 0xdEaD
  false/false. COUNTERFACTUAL: a v3 borrow reverts PolicyRunRejected inside wallet.execute(); execute() swallows it into
  OpExecuted.returnData, the OUTER tx reads status 1, ZERO funds move (the Sept v1 failure reproduced in v3 - fixed then
  for the V1 helper 0x903b8667, never for v3). CORRECTION (S8, accepted): MBT.balanceOf(v3 helper)=0 is NON-DISCRIMINATING
  and is NOT evidence that no borrow moved collateral - borrow() steps 2-4 (approve vault, vault pulls collateral, forward
  only debt back) empty the helper within the SAME tx, so a FULLY SUCCESSFUL borrow also ends at balance 0; the zero only
  rules out collateral STRANDED mid-flight. "was a v3 borrow ever attempted/succeeded" needs HISTORY, not a point read
  (see OPEN QUESTION below).
  WHY ALL THREE MISSED IT: three independent reproductions of the SAME TWO legs is not coverage (verified-neighbour at
  channel scale); the v3 pre-flight file recorded leg 3 GREEN against the V1 helper 0x903b8667 (wrong-target instrument,
  fb_instrument_must_name_its_subject). This is fb_wiring_is_not_mechanics (reviewed yet unreachable) + fb_own_fixture_not
  (ES's 6/6 suite uses its OWN registries so never reaches live token admission). V&L LESSON: enumerate EVERY leg a claim
  needs and name the subject of each before declaring "verified"; I overclaimed "fully verified" on two legs.
  REMEDIATION (the Sept three, retargeted to the v3 helper, 0xFc99-signed): (A) registerIdentity(v3HelperCcid, 0xB6F376F1)
  on idReg 0xf53c87CB; (B) registerCredential(v3HelperCcid, common.kyc, 0, 0x, 0x) on credReg 0x94d6257c; (C)
  allowAddress(0xB6F376F1) via hatch 0x5ae59D40 [TC lane]. v3HelperCcid must be DERIVED + READ BACK by CCID (NOT the v1
  value 0xfbec9c5e.. which belongs to 0x903b8667). V&L verifies all three read-backs after Ilan clears + signs. OPEN
  QUESTION (CCID/V&L): was a v3 borrow ever ATTEMPTED on chain (status 1, moved nothing)? Three DISCRIMINATING instruments
  (balance=0 does NOT answer it): (1) AllowPolicy AddressAllowed HISTORY for 0xB6F376F1 with MANDATORY positive control =
  the v1 admission tx 0xccb7774b block 47109234 for 0x903b8667 (if the control query is empty the filter is wrong; watch
  the getLogs range cap - page bounded or use a keyed endpoint, an empty capped query is a false empty); (2) vault
  positions with borrower-of-record == 0xB6F376F1 (positive evidence; v1 left positions(96)=0x903b8667); (3) OpExecuted
  enumeration on wallet 0x6d297af0 decoding OpExecuted.success+returnData, NOT the outer tx status. LEG 3 = OPEN; the
  attempted-on-chain sub-question OPEN pending those instruments.
  >> INSTRUMENTS (1)+(2) RUN 2026-10-29 (V&L own reads) - FUNDS QUESTION SETTLED: no v3 borrow has EVER moved collateral.
  (1) AddressAllowed HISTORY sweep of AllowPolicy 0x5fe9d900 for 0xB6F376F1, window [49,750,000 .. 51,422,500] (from
  below the helper's deploy block ~49.9M to head) in 10k-block chunks on publicnode (10k cap; Alchemy's 10-block cap was
  unusable): found=0, chunk-errors=0 (no capped/false-empty gap). POSITIVE CONTROL fired: the identical query for v1 helper
  0x903b8667 returned its admission tx 0xccb7774b.. at block 47109234 exactly, so the filter + endpoint depth are proven.
  => the v3 helper was NEVER admitted while it had code; step 1 transferFrom(->helper) always reverted; no borrow moved
  collateral. (2) VAULT POSITIONS on 0xf48f2683, all 142 scanned: NO borrower-of-record == 0xB6F376F1; control fired
  (positions(96) == v1 helper 0x903b8667). => no completed v3 borrow. Both controls fired; the empties are real, not
  false-absence. (3) ATTEMPT enumeration on wallet 0x6d297af0 - CLOSED by txlist (cheaper than the OpExecuted event, and
  CCID reached the same answer independently). Etherscan V2 account txlist is free on Amoy (chainid 80002). The wallet is a
  CONTRACT (7770 B, nonce 1 = EIP-161 initial), CREATE2-deployed at block 49987034 by factory 0xea4d351f. Normal txlist =
  0 (INBOUND coverage proven by control: vault 0xf48f2683 txlist = 298, 297 with to=vault, a keyless contract so all
  inbound - an attempt arrives inbound principal->wallet, so this is the right direction; getAssetTransfers would MISS a
  reverted attempt that moved no asset, which is why txlist not transfers). Internal txlist = 1, and that one is the
  create2 DEPLOYMENT (type create2, contractAddress==wallet), NOT a call; endpoint-returns-internal proven by that
  non-empty result. => the wallet was CREATED and NEVER CALLED: no v3 borrow was ever even ATTEMPTED. So the hygiene
  question resolves the same way as funds - nothing attempted, no status-1-moved-nothing record exists anywhere.
  LEG 3 remains OPEN on the ADMISSION only (helper still addressAllowed=FALSE/validate=FALSE; the Sept-three remediation +
  a live borrow WITNESSED at fire time close it). LEG 4 (the c.iii scoped-allowance discriminator) is SEPARATELY OPEN - it
  has only ever run in ES's fixture (fb_own_fixture_not); the same fire-time live borrow is the only thing that closes it.
  Do NOT fold leg 4 into leg 3: a fixture pass is exactly what made this stack look finished when it was not.
  >> CEREMONY STATE 2026-10-29 (per S8): Legal CLEARED the admission ceremony (AGENTS-15 trigger + its lite check
  discharged in the same answer; basis: keyless/immutable/passive contract verified 3 ways incl V&L, already the SOLE
  expectedTarget in the live credential so admission makes a previously-cleared authority OPERABLE rather than granting new
  authority, caps untouched, c.iii already exercised this helper path on chain, no public claim / no live exposure). ES has
  STAGED all three legs to the signature line (leg C text from TC); STAGED != fired - ILAN'S DIRECT WORD fires it. CLAIM
  CONDITIONS to hold at verification/wording time: operational credential for a CONTRACT, never "KYC'd"/"verified"; passive-
  mover scope only; standing escalations RE-OPEN the whole check on mainnet / real value / third-party consumers / any
  public claim; R2 untouched. V&L CLOSE CONDITION UNCHANGED (not softened): leg 3 stays OPEN until all four read-backs flip,
  leg 4 stays SEPARATELY open until a real borrow is witnessed live at fire time. Nothing to verify until the writes land.
  >> ADMISSION CONFIRMED EFFECTIVE 2026-10-29 (V&L own reads, block 51456535 @ 13:05:01Z) - the retargeted Sept-three
  landed overnight and the helper is now admitted on the LIVE MBT stack: addressAllowed(0xB6F376F1)=true (AllowPolicy
  0x5fe9d900), validate(0xB6F376F1)=true (validator 0xCAEe7962), getIdentity(0xB6F376F1)=0xbd8cd94e..de26f == the
  CCID-derived v3HelperCcid (== my own keccak(ob.ccid.v1||helper)) on LIVE idReg 0xf53c87CB934C2f0c6fb0cEdFf3841a6d427f7C35,
  getCredentialTypes(ccid)=[common.kyc] not expired (credReg 0x94d6257c); control validate(0xdEaD)=false. CROSS-STACK CATCH:
  my first getIdentity used the OBJT ISOLATED idReg 0x4C9244e7 by mistake and ERRORED "no code" (instrument fault, not a
  subject fault) - the LIVE idReg is 0xf53c87CB, resolved from the live validator's getCredentialSources; corrected before
  trusting. PRE-BORROW BASELINE for deltas: wallet 0x6d297af0 MBT 30e18, helper MBT 0 (pass-through), vault 0xf48f2683
  nextPositionId = 142 (the borrow opens pos 142). The 3 admission tx hashes (registerIdentity/registerCredential/
  allowAddress, from 0xFc99) requested from ES for the provenance line; state effect already confirmed. LEG 3 precondition
  (admission) MET; leg 3 CLOSES only on the borrow MOVING funds; LEG 4 (second transferFrom fails allowance-insufficient,
  named reason) still separate. Instruments armed (decode OpExecuted.success+returnData not outer status; Transfer by topic
  position; leg-4 revert must be allowance-insufficient specifically, not a bare revert).
  ADMISSION TX PROVENANCE (V&L-verified, Amoy, all status 1 from 0xFc99): A registerIdentity 0xa9391c365fe2081b46a92feffb8ce1af03e4a991b935a75dabfdfe2d53205ac6
  (blk 51426576, to idReg 0xf53c87CB, sel 0xf176f9a1, calldata names helper+ccid); B registerCredential
  0x8859e0c80c9b316f0f1d0c0f41f24897e5117375e26ed3dfae9432a7a26a2b13 (blk 51426665, to credReg 0x94d6257c, sel 0x8ab73618,
  calldata names ccid); C execOnAllowPolicy(allowAddress(helper)) via hatch 0x4d792b142b2b645de4968a256665708e89a525f9a925931511eb4aaa9c8cebed
  (blk 51426783, to hatch 0x5ae59D40, sel 0x2c28acf1, calldata names helper). S8 bracketed the validate flip: false @51426621
  (after A) -> true @51426707 (after B).
  IDREG TWIN HAZARD (record for the next desk; resolution method): there are TWO IdentityRegistries - LIVE MBT
  0xf53c87CB934C2f0c6fb0cEdFf3841a6d427f7C35 and OBJT-ISOLATED 0x4C9244e7cDF5A960B0b98fe846e49929d2d53bcf. ASYMMETRIC
  false-absence: querying the wrong one errored "no code" today (loud, caught), but if that isolated registry ever HAS code a
  getIdentity there returns a clean 0x0 = indistinguishable from "never registered" = would read as "admission did not land".
  RESOLVE the registry from the LIVE validator's own getCredentialSources(common.kyc) (a pointer from its owner), NEVER from
  memory or deployment order.
  LEG-4 RECIPIENT CHOICE (V&L flagged before the op, 2026-10-29): 0xCf30 is ADMITTED (addressAllowed+validate true), principal
  0xd63352 is NOT (both false). Use 0xCf30 as the attack destination: fix-works reverts allowance-insufficient regardless
  (allowance checked before transfer), but a BROKEN fix with 0xCf30 completes a visible theft (to-side admitted) whereas with
  0xd63352 it would revert at the token's to-side ALLOWLIST - a wrong-reason revert that MASKS a broken fix as a pass. If
  0xd63352 is used anyway, V&L accepts leg 4 ONLY on a decoded allowance-insufficient revert and records an allowlist/policy
  revert as NOT-demonstrated.
  LEG-4 ACCEPT CRITERION IS NOW BYTE-LEVEL (V&L-confirmed by derivation + live probe, not inherited): expected innermost
  revert = InsufficientAllowance() = 0x13be252b (the RWATokenV2 allowance guard; distinct from ERC20InsufficientAllowance
  (address,uint256,uint256) 0xfb8f41b2 and InsufficientAllowance(address,uint256,uint256) 0x192b9e4e - no collision). V&L
  PRE-IMAGE PROBE (block ~51458xxx): allowance(wallet->helper)=0, and MBT.transferFrom(wallet, 0xCf30, 1e18) with --from
  helper reverts data exactly 0x13be252b - identical to the attack's inner call (step1 consumes the scoped allowance to 0,
  step3 re-pulls against 0). [[SELF-CORRECTION 2026-10-29, S8 concurrence]] An EARLIER draft of this line read "this
  EMPIRICALLY confirms the allowance guard runs BEFORE the to-side compliance hooks" - that was WRONG and is RETRACTED. The
  single-recipient probe (0xCf30 only -> 0x13be252b) is NON-DISCRIMINATING for ordering: an ADMITTED recipient passes the
  to-side compliance check under EITHER ordering, so you observe the allowance revert regardless of which guard runs first. It
  proves the byte value 0x13be252b for an admitted destination; it proves NOTHING about guard order. The ordering was only
  established by the two-recipient comparison in the PRE-FIRE CATCH below (admitted 0xCf30 -> 0x13be252b vs UNADMITTED
  0xdEaD/0xd63352 -> 0xfbdad4f4), and it is the OPPOSITE of the retracted claim: the TO-SIDE COMPLIANCE CHECK RUNS BEFORE THE
  ALLOWANCE GUARD. Instrument lesson (fb_bare_mustfail_nondiscriminating / fb_concurrence_is_not_coverage): the discriminator is
  the SECOND, unadmitted recipient; a probe over one admitted address can never order two guards that both pass it. S8
  independently reproduced all three recipients (block 51458857) and flagged the same non-discrimination; recorded here so the
  stale ordering claim cannot outlive its correction. ACCEPT (ES rule,
  V&L ratifies): (a) execute() reverts, tx rolls back, nonce unchanged; (b) THE PAIR - innermost 4 bytes == 0x13be252b AND the
  VaultCallFailed address arg decodes to MBT 0x71A8e443..DCe2 - full frame InnerCallFailed(0x72866feb) > VaultCallFailed(0x375bdfbc,
  addr==MBT) > 0x13be252b (per T2; bytes alone are shared across OB tokens and do NOT name MBT); (c) 0xCf30 MBT delta 0 (unchanged
  ~107e18) AND wallet MBT unchanged.
  NOT-DEMONSTRATED (not a pass): gate 6/7/8 mismatch, gate 11 cap, CollateralTransferFromFailed, VaultCallFailed with
  vault!=MBT, or any to-side compliance error - specifically 0xfbdad4f4 "address is not on allow list" (AllowPolicy
  0x5fe9d900, the to-side compliance revert) is NOT a pass for c.iii: it fires BEFORE the allowance guard is reached, so it
  says nothing about whether the scoped-allowance guard works. Each is a DIFFERENT guard and says nothing about c.iii.
  >> TWO TIGHTENINGS TO THE ACCEPT CRITERION 2026-10-29 (CCID raised, S8 relayed, V&L RE-DERIVED independently with cast, not
  inherited - all five selectors reproduced exactly):
  (T1) ATTRIBUTION FIX - the pass value belongs to OB, not OpenZeppelin. 0x13be252b = OB RWATokenV2.InsufficientAllowance()
  (NO ARGUMENTS, RWATokenV2.sol:366). It is NOT an OpenZeppelin error. OZ's is ERC20InsufficientAllowance(address,uint256,
  uint256) = 0xfb8f41b2, which is a REJECT value here; a third variant InsufficientAllowance(address,uint256,uint256) =
  0x192b9e4e is also a reject. 2B's op-shape file (AGENTS15-STEP14) LABELS 0x13be252b as "OZ ERC20 InsufficientAllowance()" -
  that NAME is wrong (the number is right). A reviewer re-deriving the pass selector from the words "OZ ERC20" computes
  0xfb8f41b2, the exact value (iii) says to REJECT. This is the restatement-is-a-copy hazard (a correct number with a wrong name
  survives review); the authoritative attribution is OB RWATokenV2.InsufficientAllowance() (no-arg). S8 has told 2B to fix the
  file.
  (T2) THE INNERMOST 4 BYTES DO NOT NAME THE REVERTING CONTRACT - bind (iii) to the PAIR, not the bytes alone.
  InsufficientAllowance() is no-arg and defined IDENTICALLY across several OB tokens, so 0x13be252b alone establishes only "SOME
  OB token rejected for insufficient allowance", NOT "MBT did". What pins it to MBT is the wrapper's address argument. Full
  three-layer decode (V&L-derived): InnerCallFailed(bytes) = 0x72866feb; VaultCallFailed(address,bytes) = 0x375bdfbc, whose
  address arg MUST decode to MBT 0x71A8e443f223cfe59498968758dB49f2d114DCe2; innermost = 0x13be252b. LEG 4 PASSES ONLY on the
  PAIR: innermost == 0x13be252b AND VaultCallFailed address arg == MBT. A 0x13be252b from a different OB token satisfies the
  letter of the old condition but is NOT-DEMONSTRATED.
  >> PRE-FIRE CATCH 2026-10-29 - STAGED LEG-4 OP MIS-TARGETED (V&L live probe; held before fire). Rail 2B's staged op shape
  (AGENTS15-STEP14-V3-BORROW-OP-SHAPE.md line 137) set the leg-4 attack recipient to 0x..dEaD. PROVEN non-discriminating:
  transferFrom(wallet, 0xdEaD, 5e18) from=helper at allowance 0 reverts 0xfbdad4f4 "address is not on allow list" (AllowPolicy
  0x5fe9d900), NOT 0x13be252b; the SAME call to 0xCf30 reverts 0x13be252b. So the token runs the TO-SIDE compliance check
  BEFORE the allowance guard - an UNADMITTED recipient reverts at the allowlist and NEVER reaches scoped-allowance step 3, so
  a WORKING fix and a BROKEN fix BOTH revert 0xfbdad4f4 (non-discriminating; also contradicts the op's own expected innermost
  0x13be252b). Both 0xdEaD AND 0xd63352 are unadmitted (false/false); the ONLY admitted Ilan-controlled recipient is 0xCf30.
  This SUPERSEDES the earlier "0xd63352 would mask a broken fix" note - the masking is stronger than that: with any unadmitted
  recipient the allowance guard is never reached at all. REQUIRED FIX: leg-4 vaultCalldata recipient = 0xCf30. Relayed same-turn
  to 2B (op owner), S8 (coordinator), ES (stager) as separate messages. Leg 3 unaffected. V&L still accepts leg 4 ONLY on
  innermost == 0x13be252b; a 0xfbdad4f4 innermost = NOT-DEMONSTRATED.
  >> LEGAL CLEARED + CONDITIONS 2026-10-29 (S8 relay): AGENTS-15 STEP-14 authorization = MATTER 21 (distinct from the Tuesday
  admission-ceremony clearance and from matter 9's Sept lite check which covers the operation class; no re-check trigger fires
  on these facts - testnet, test-token value, caps NOT widened to fit the test, no public claim, R2 held). CONDITIONS binding
  on this ledger entry:
  (iii) THE BYTE CRITERION IS NOW A LEGAL CONDITION, not just V&L discipline: leg 4 PASSES ONLY on THE PAIR - innermost ==
  0x13be252b (OB RWATokenV2.InsufficientAllowance(), no-arg; NOT OZ's 0xfb8f41b2) AND the VaultCallFailed(address,bytes) address
  arg decodes to MBT 0x71A8e443f223cfe59498968758dB49f2d114DCe2 (per T2 above - the no-arg selector is shared across OB tokens, so
  the bytes alone do not name MBT). ANY other revert, OR 0x13be252b from a non-MBT vault, is NOT a pass, it is a FINDING
  (NOT-DEMONSTRATED is the required disposition).
  (vi) HALT CONDITION: if leg 4 MOVES FUNDS (MBT actually lands at 0xCf30, outer status 1), that is a FULL STOP for the whole
  agent lane - no further agent ops until Legal rules; V&L says it IMMEDIATELY and plainly, before completing a tidy entry.
  (iv) results report as "testnet discriminating proof", NEVER "on-chain enforced"; R2 held until P.5.
  ORDERING now load-bearing: leg 3 fires + is witnessed BEFORE leg 4 (a broken guard = no second attempt).
  OP RE-SHAPED (2B, 0xCf30 + BIG tightened): leg-4 recipient hard-coded 0xCf30; attack amount BIG = 4e18 (NOT 5e18) because
  5e18 == maxAmountPerOp would let gate 14 OutflowExceedsCap preempt and MASK the allowance (a) failure; 4e18 < cap so gate 14
  passes and a BROKEN fix shows as either 4e18 landing at 0xCf30 (CRITICAL FAIL, status 1) or the PASS revert. 2B DISCRIMINATOR
  TABLE = V&L witness rubric: PASS = innermost 0x13be252b; CRITICAL FAIL = status 1 + Transfer 4e18 to 0xCf30 (both (a)+(b)
  broken -> (vi) halt); REBUILD/NOT-DEMONSTRATED = OutflowExceedsCap, 0xfbdad4f4, any wallet gate 5/6/7/8/11/13 revert, OR an
  EMPTY inner returnData (InnerCallFailed wrapping zero-length bytes = OUT-OF-GAS inside the inner call; distinct from a
  wrong-selector revert - see the (b) leg-4 first-fire entry below. Must decode the revert frame AT THE REAL on-chain gas limit).
  >> ADMISSION END-TO-END CLOSED, TWO INDEPENDENT DESKS (concurrence!=coverage, each enumerated own legs): V&L (block 51456535)
  + CCID (block 51458494, 13:37:40Z): getIdentity(helper)=0xbd8cd94e..de26f (CCID-derived == V&L keccak(ob.ccid.v1||helper)),
  credReg.validate(ccid,common.kyc)=true, validator.validate(helper)=true, addressAllowed(helper)=true - all four flipped from
  the 03:27Z RED. CCID also witnesses the leg-4 revert-frame + before/after window-state (gate-14 must NOT accrue on the
  reverting attack op); V&L witnesses independently. The step-14 borrow is the NEXT, separate event.
  >> WHY BIG=4e18 - THE LOAD-BEARING REASON IS ARITHMETIC, NOT GATE-14 MASKING (2B found it, S8 relayed, V&L VERIFIED
  FIRST-HAND against source + independent arithmetic 2026-10-29). The gate-14 masking note above (line ~3459) is the SECONDARY
  reason; it is weaker because at 5e18 gate 14 produces a DIFFERENT selector (OutflowExceedsCap 0xde2a2e07) that the taxonomy can
  catch. The PRIMARY reason survives even with gate 14 removed: the c.iii bug grants the helper a scoped allowance of
  maxAmountPerOp (5e18) instead of decodedAmount (collateralAmount = 1 wei). After step-1 transferFrom consumes 1 wei, the
  remaining allowance available to the attack's step-3 transferFrom is: FIXED world = 0 (grant was 1 wei); BROKEN world = 5e18 - 1
  = 4999999999999999999. The attack asks BIG. Discriminating iff FIXED reverts AND BROKEN succeeds <=> 0 < BIG <= 5e18-1, i.e.
  BIG in [1, 5e18-1]. At BIG=5e18: 5e18 > 5e18-1, so BROKEN ALSO reverts -> BOTH worlds return the IDENTICAL 0x13be252b ->
  NON-DISCRIMINATING. At BIG=4e18: BROKEN transfers 4e18 (visible theft), FIXED reverts -> discriminating. V&L reproduced this
  arithmetic independently (both 5e18 and 4e18) and confirmed the model against SOURCE, first-hand (not inherited):
  OBAgentWalletV3.sol:344 _grantScopedAllowance(expectedAsset, target, decodedAmount) grants DECODED amount not maxAmountPerOp
  (fix); :371 if(outflow>maxAmountPerOp) revert OutflowExceedsCap (gate 14, OutflowExceedsCap=0xde2a2e07 my derivation);
  OBBorrowHelperV3.sol:133 step-1 transferFrom(wallet->helper, collateralAmount) consumes the scoped allowance; :127 step-3
  vault.call(vaultCalldata) wrapped VaultCallFailed on revert. Live maxAmountPerOp = 5e18 (S8 read).
  >> INSTRUMENT-BLINDNESS LESSON (the sharpest version, record it): at BIG=5e18 a WIDE-OPEN c.iii vulnerability would have
  satisfied, SIMULTANEOUSLY, (1) V&L's byte criterion 0x13be252b, (2) CCID's three-layer InnerCallFailed>VaultCallFailed(MBT)>
  0x13be252b decode, and (3) ES's pass rule - because the revert bytes are BIT-IDENTICAL between the fixed and broken worlds. No
  downstream instrument could recover the difference. The discriminating power lives ENTIRELY in the choice of the TEST INPUT
  (BIG relative to maxAmountPerOp - collateralAmount), NOT in any of the three reading instruments. Three independent desks
  agreeing on the revert frame is concurrence, not coverage (fb_concurrence_is_not_coverage); a must-fail at the wrong amount is
  non-discriminating (fb_bare_mustfail_nondiscriminating / fb_measure_constraint_dont). CONSEQUENCE for the witness: the 4e18
  value is itself a PASS PRECONDITION - if the FINAL staged op does not carry BIG=4e18 (or any BIG outside [1,5e18-1]), the test
  is void regardless of the revert bytes. V&L re-reads BIG in the final calldata before witnessing.
  >> OUTSTANDING LIST CLOSED 2026-10-29 (S8 verified calldata field-by-field himself, V&L re-derived selectors): leg-4 op selector
  borrow(address,uint256,address,bytes,address)=0x4dba62ff; collateralAmount=1 wei; vault=MBT (attack shape); inner selector
  transferFrom=0x23b872dd, from=wallet, TO=0xCf309940276b1e1396aed22ac4e1e7d2e3dd27d6, amount=4e18; 0xdEaD ABSENT from calldata.
  Leg 3: 3e18 (< cap), vault=Atlas 0xf48f2683 (NOT MBT), debt asset USDC 0xce8c237c, inner amount matches collateral. Gate-14
  bound confirmed from source (above). NOTHING pending before fire except Ilan's signature; block is with Ilan. Leg 3 fires +
  S8 reads its events FIRST, then leg 4 released; V&L receives (a) then (b) and witnesses each event-level.
  >> (a) LEG 3 WITNESSED AND PASSED - V&L INDEPENDENT event-level decode 2026-10-29 (NOT inherited; S8 self-flagged a decode
  error on this tx, see lesson below). TX 0x7ef19a599688c2830b636fcd644cae736ea244b3300f725782f52750d72116b6, Amoy block 51459713,
  status 1, to = agent wallet 0x6D297af08432462b161023ADCe813E1Ae1a24aFA, signed by principal 0xd63352Cc..446a (outer nonce 28).
  EVENT TRAIL (decoded by me, Transfer direction by TOPIC POSITION per fb_transfer_topic_position_is_direction):
    - ScopedAllowanceGranted(MBT, helper, 3e18) [wallet evt 0xe60bf113] = grant == DECODED amount 3e18, NOT the 5e18 cap (the
      c.iii fix's CORRECT-grant behaviour, observed live for the first time; benign case).
    - MBT Transfer wallet->helper 3e18 (step1); MBT Approval helper->Atlas 3e18 (step2); MBT Transfer helper->Atlas 0xf48f2683
      3e18 (step3); USDC 0xce8c237c Transfer Atlas->helper 24e6; USDC Transfer helper->wallet 24e6 (step4 debt forward).
    - Atlas Borrow evt [0x448a8741] position id 0x8e=142, borrower indexed = HELPER 0xB6F376F1; BorrowExecuted [helper evt
      0x711a037b] wallet/MBT/Atlas 3e18, USDC 24e6.
    - NetOutflowObserved(MBT, 3e18, 5e18) [0x4b194db8, sig V&L-derived as a positive control = matches] -> gate 14 did NOT bind.
    - ScopedAllowanceRevoked / MBT Approval wallet->helper 0 -> allowance back to 0.
  OpExecuted [wallet evt, sig 0x45d22959 = keccak(OpExecuted(address,uint256,string,uint256,bool,bytes)), V&L-derived]: I decoded
  the DATA TUPLE from the SOURCE ABI (OBAgentWalletV3.sol:113), not from any summary. Non-indexed order = (uint256 value, string
  actionName, bool success, bytes returnData). Decoded: value=0; actionName="helper.borrow" (len 13); SUCCESS = word 2 = 1 = TRUE;
  returnData=24000000 (24e6 USDC). nonce (indexed topic2) = 0 (pre-increment).
  POST-STATE (V&L live, AS-OF block 51460181, 2026-10-29T14:05:47Z): wallet MBT 27e18 (was 30e18, -3e18); wallet USDC 24e6 (was 0);
  helper MBT 0; allowance(wallet->helper) MBT 0; Atlas positions(142) borrower-of-record = HELPER 0xB6F376F1 (live read);
  nextPositionId 142 -> 143. All consistent with the event trail. LEG 3 = PASS (first autonomous v3 agent borrow; funds moved
  wallet->vault, debt returned, position keyed to the helper as predicted).
  INSTRUMENT LESSON (S8 self-caught, V&L did NOT inherit): S8's first read reported OpExecuted.success = FALSE by taking the FIRST
  data word (value=0) as the success flag. The source ABI puts success as the THIRD non-indexed field (word 2). A positional guess
  on a dynamic event tuple is a FALSE reading; the authority is the event DECLARATION (decode offsets + field order from it, and
  keep a cross-check against post-state as S8 did - that cross-check is what caught the error before it shipped as "first borrow
  FAILED"). V&L re-derived the layout from source and reads success=TRUE. Benign-case c.iii details (grant at decoded 3e18 not cap;
  allowance revoked to 0) do NOT close leg 4 - only the adversarial pull proves the guard fires. Leg 3's immutable on-chain
  ordering requirement is satisfied; leg 4 released to Ilan. Awaiting (b).
  >> (b) LEG 4 (FIRST FIRE) = NOT DEMONSTRATED - V&L INDEPENDENT gas-variance diagnosis 2026-10-29 (the gas-variance requirement
  caught it; did NOT inherit S8 - S8 made a SECOND decode error on this leg, see lesson). TX
  0x2abab78d10152cabe32c70c2b302d94c2138c596148c92eb775d98e988bd4a61, Amoy block 51459909, from principal 0xd63352Cc (outer nonce
  29), to wallet 0x6D297af0, OUTER STATUS 0 (failed), gasUsed 770208 of gas-limit 800000 (96.3% - consistent with OOG). Only log =
  system MATIC-fee evt 0x1010 (a reverted tx rolls back app logs, so "no app logs" is consistent with ANY revert, NOT specifically
  OOG - the discriminator is the REVERT RETURNDATA + gas-variance, below). OP CORRECTLY SHAPED (V&L decoded the input): execute()
  0x0c2b034e, target=helper, inner borrow() 0x4dba62ff, vault=MBT, collateralAmount=1 wei, vaultCalldata=transferFrom(wallet,
  0xCf30, 0x3782dace9d900000) and 0x3782dace9d900000 == 4e18 EXACTLY -> BIG=4e18 precondition HOLDS, recipient 0xCf30, 0xdEaD absent.
  GAS-VARIANCE REPLAY (V&L, cast call at latest; leg-4 reverted and changed nothing so current state == pre-tx conditions: allowance
  0, nonce 1 - verified below). Revert returndata by gas limit:
    - 800000 (the REAL tx limit): 0x72866feb InnerCallFailed(bytes) wrapping EMPTY BYTES (length 0). Empty inner reason = the
      OUT-OF-GAS signature (a call that exhausts gas reverts with no returndata). NO 0x13be252b, NO VaultCallFailed.
    - 1200000: 0x72866feb InnerCallFailed( 0x375bdfbc VaultCallFailed( addr==MBT 0x71a8e443..dce2, 0x13be252b ) ) - full frame.
    - 2000000: identical full frame.
  The REVERT REASON CHANGES WITH THE GAS LIMIT (empty -> full 0x13be252b frame). A DETERMINISTIC guard revert is gas-invariant;
  only OOG changes with gas (fb_oog_vs_deterministic_vary). Therefore the WITNESSED on-chain tx (800k, gasUsed 770208) reverted from
  OUT-OF-GAS inside the inner call, BEFORE the token produced InsufficientAllowance. It reverted for the WRONG REASON and FAILS
  condition (iii). DISPOSITION = NOT DEMONSTRATED (neither a pass nor a (vi) critical fail).
  CONDITION (vi) DOES NOT FIRE - NO FUNDS MOVED (V&L live, AS-OF block 51460683, 2026-10-29T14:14:10Z): 0xCf30 MBT 107e18 UNCHANGED;
  wallet MBT 27e18 UNCHANGED (== post-leg-3); helper MBT 0; allowance(wallet->helper) 0; wallet nonce() == 1 UNCHANGED (the reverted
  tx consumed no op-nonce). Lane NOT halted.
  WHAT IS / IS NOT ESTABLISHED: the replay at >=1.2M gas shows the correct guard fires with the correct frame
  (VaultCallFailed(MBT)+0x13be252b), so the evidence POINTS TO the c.iii fix working - BUT a replay is NOT a witnessed on-chain
  transaction (fb_cast_run_resim_not / fb_only_live_read_safe). Leg 4 is NOT closed. A live re-fire at sufficient gas is required.
  RUBRIC ROW ADDED (per S8 request, V&L concurs): an EMPTY inner returnData = OOG = NOT a pass, DISTINCT from a wrong-SELECTOR
  revert. Both are NOT-DEMONSTRATED but for different reasons; an OOG masquerades as "it reverted" without exercising the guard.
  INSTRUMENT LESSON (S8's SECOND self-caught error this ceremony, V&L did NOT inherit): S8 first replayed WITHOUT a gas-limit
  override, got the full three-layer frame (incl. MBT addr arg), and reported it to Ilan as "the exact pre-registered frame" = a
  PASS. A no-limit replay gives the inner call far MORE gas (and 63/64 of a large budget) than the real 800k tx had, so it produces
  the deterministic guard revert that the real tx never reached. RULE: when diagnosing a reverted tx, REPLAY AT THE ACTUAL ON-CHAIN
  GAS LIMIT FIRST, then vary upward; a frame obtained at a different gas budget is not evidence about what the real tx did
  (fb_fork_gas_not_chain). This is the 2nd decode error S8 self-flagged on this ceremony (1st: OpExecuted.success on leg 3); both
  were caught by cross-checks (post-state; gas variance) before entering the ledger. The gas-variance requirement is vindicated:
  without it, a NOT-DEMONSTRATED OOG would have been recorded as a c.iii PASS.
  >> (b2) RE-FIRE issued to Ilan at --gas-limit 2500000, same op, nonce still 1, BIG unchanged 4e18 (precondition holds). Awaiting
  (b2) hash; on arrival V&L re-reads BIG in the FINAL calldata, witnesses outer status + decodes the revert frame AT THE REAL GAS
  LIMIT, and accepts ONLY on the PAIR (innermost 0x13be252b AND VaultCallFailed addr==MBT) with a NON-EMPTY inner frame; empty
  inner = OOG = NOT-DEMONSTRATED again; 4e18 landing at 0xCf30 status 1 = (vi) FULL STOP said immediately.
  >> (b2) LEG 4 RE-FIRE = PASS - c.iii SCOPED-ALLOWANCE GUARD DEMONSTRATED ON A LIVE ADVERSARIAL ATTEMPT. V&L INDEPENDENT witness
  2026-10-29 (did NOT inherit; S8 self-flagged a THIRD instrument fault on this leg, see lesson). TX
  0x7574345a49cdf586ca921cc55172082a88f79334e2fe25042b0fa514ff63d6f5, Amoy block 51460388, from principal 0xd63352Cc, to wallet
  0x6D297af0, gas-limit 2500000. BIG RE-CONFIRMED in the FINAL calldata by V&L: inner transferFrom(wallet, 0xCf30, 0x3782dace9d900000)
  and 0x3782dace9d900000 == 4e18 EXACTLY (precondition holds). SIX CRITERIA, each verified by V&L first-hand:
    (a) outer STATUS = 0x0 (reverted, rolled back) - read from the JSON status FIELD, positive control leg-3 = 0x1. PASS.
    (b) revert frame decoded by V&L via gas-variance replay (cast call; the reverted tx changed nothing so state == tx conditions):
        0x72866feb InnerCallFailed > 0x375bdfbc VaultCallFailed(addr==MBT 0x71a8e443..dce2) > innermost 0x13be252b
        InsufficientAllowance. THE PAIR HOLDS (innermost 0x13be252b AND VaultCallFailed addr == MBT). PASS.
        NON-EMPTY + GAS-INVARIANT: full 228-byte frame IDENTICAL at 1500000 / 2500000 / 3000000 gas => deterministic GUARD revert,
        NOT OOG. gasUsed 1061802 of 2500000 (42%, nowhere near exhaustion). CONTRAST the first fire (800k): 68-byte
        InnerCallFailed(EMPTY) = OOG. This gas pair is the whole reason the gas-variance rule exists.
    (c) 0xCf30 MBT = 107e18, gained EXACTLY 0 (V&L live, AS-OF block 51461368, 2026-10-29T14:25:35Z). PASS.
    (d) wallet MBT = 27e18, UNCHANGED (== post-leg-3). PASS.
    (e) wallet nonce() == 1, op NOT consumed (reverted tx). PASS.
    (f) ZERO non-gas logs (1 log = system MATIC-fee 0x1010 only; no OpExecuted, no Transfer). PASS. allowance(wallet->helper)=0.
  WINDOW-STATE LEG (V&L owns this read per the CCID division of labour; V&L verified FIRST-HAND, not inherited): economic-window
  gate-14 did NOT accrue the 4e18 attack. actionHash = keccak("helper.borrow") = 0xbb6507f5..0672 (V&L-derived == leg-3 event log0
  topic1 == CCID). previewEconomicWindow(actionHash, MBT, 3600) read live AS-OF block 51463233 / 2026-10-29T14:56:39Z =
  (windowStart 1793282279, windowSpent 3e18). The 3e18 is LEG-3's observed-outflow accrual ONLY; the leg-4 attempt reverted
  (status 0) so the whole execute() rolled back and the gate-14/window commit (OBAgentWalletV3.sol:381 ew2.spent = newSpent) never
  ran -> leg-4 window DELTA = 0 by construction (a reverted tx commits nothing). Delta-form, roll-immune. Confirms the reverting
  attack accrued nothing against the economic window. (CCID routed the same reads; byte-level raw returndata cross-check available in
  CCID-V3-ADMISSION-PREFLIGHT.md if a diff is ever needed - two-desk on the frame, each decoded independently.)
  DISPOSITION: LEG 4 CLOSED = PASS. The c.iii scoped-allowance guard FIRED on a LIVE adversarial second-pull of 4e18 (sized inside
  the discriminating window [1, 5e18-1] so a BROKEN fix WOULD have transferred), against the LIVE admission, rejected at the
  allowance with the CORRECT token named - FIRST TIME OUTSIDE A FIXTURE. Combined with leg 3's benign half (grant at decoded 3e18
  not the 5e18 cap, revoked to 0), the per-op scoped-allowance control (AGENTS-15 sec 4.11 (a) / c.iii) is DEMONSTRATED end-to-end.
  CONDITION (vi) DOES NOT FIRE (no funds moved). LANGUAGE BINDING per condition (iv): report strictly as a "TESTNET DISCRIMINATING
  PROOF" - NEVER "on-chain enforced"; R2 STAYS CLOSED until P.5. Scope: isolated/admitted v3 agent stack on Amoy testnet, test-token
  value, Matter 21 step-14 authorization.
  INSTRUMENT LESSON (S8's THIRD self-caught fault this ceremony, V&L did NOT inherit): S8's scripted check reported "(a) FAIL"
  because it compared `cast receipt ... status` (human output = "false") against the string "0". SUBJECT was fine; the INSTRUMENT
  (string compare) was wrong (fb_instrument_fault_vs_subject_fault). RULE: read outer status from the JSON `status` field (0x0/0x1),
  with a positive control (leg-3 0x1). Three S8 faults this ceremony (leg-3 OpExecuted.success positional misread; leg-4 no-gas-limit
  replay; (b2) status string-compare) - ALL three were SUBJECT-correct / INSTRUMENT-wrong, and ALL three were caught by cross-checks
  (post-state, gas variance, JSON field + control) BEFORE entering the ledger. The ceremony's result is sound; the discipline that
  made it sound was independent re-derivation + controls, not any single desk's first reading. CEREMONY COMPLETE: leg 3 PASS + leg 4
  PASS, two-desk witnessed (V&L + S8; CCID routed the attack-frame decode). Cite "Matter 21 step-14 authorization".

---
_Last updated 2026-07-16 (Multi-Asset Signing Session 1: AVR + TGLP, 38 contracts Sepolia+Amoy, Etherscan V2 verified + Slither audited, 0 Crit/High/Med in OB code) by RWA Bridge Session 8 (coordinator). All live-network canonical protocol contracts
verified (Astar Shibuya 5/5); source-based audit (3 analyzers, 0 Crit/High/Med) covers all EVM
chains + Solana._

---

## BASE SEPOLIA ACE STACK - PUBLIC-EXPOSURE VERIFICATION (V&L, 2026-11-14) - PASS

Routed by S8 ahead of a PUBLIC EXPOSURE decision (flip VITE_BASE_ENABLED + move the "N EVM chains
credential-gated" wording -> publishes Base addresses, so V&L-gated). Addresses from deployments.json
`base_sepolia` (S8-supplied, authoritative). ALL reads CHAIN-QUALIFIED to Base Sepolia chainId 84532
(confirmed on 3 RPCs) - this is the control for the recorded TWIN HAZARD (the prior block was an Apr-2026
partial-deploy whose addresses were BNB-stack address-twins; a wrong-chain read returns the twin and looks
healthy). Identity taken from each contract's own typeAndVersion, read on Base.

VERDICT: PASS - the Base Sepolia ACE stack is correctly deployed, WIRED, and credential-DISCRIMINATING on
84532. Publishing these addresses is not misleading PROVIDED the wording constraints below hold. The FLIP
itself stays with Ilan/S8; this clears only the address-exposure/credential-gated-claim gate.

READINGS (AS-OF Base Sepolia block 58526932..58528894, 2026-11-14T03:34-03:40Z; cast, publicnode RPC):
- MBT token 0x2aedDb48a3b6028244ed31C0fdDEB2B72Fce3bE1: name "Miami Beach Tower", symbol MBT, decimals 18,
  code 6720B. No typeAndVersion (older ERC20 build; identified by name/symbol). WIRING: getPolicyEngine()
  == 0xa1a58d40490CeB9a36bA2c8e0Bf457aba591452c == the engine read below (so the chain below is the one MBT
  ACTUALLY routes to, not a neighbor - closes wiring!=mechanics).
- PolicyEngine 1.0.0  0xa1a58d40490CeB9a36bA2c8e0Bf457aba591452c
- AllowPolicy 1.0.0   0x2b7B6f835D9C416e84ce85F982D37b5a78fbe6D8
- PausePolicy 1.0.0   0x55d0Ed9c1232646ECeabAaA95bc74892B6A2DEEC
- CredentialRegistryIdentityValidatorPolicy 1.0.0  0x6dea0c2323b90784b143E6011D2F003aF61BD67F
- pool BurnMintTokenPool 1.5.1  0x49f1aa7C252183E79D4944c54698a335Ed65C150 = CCIP, NOT V&L-gated here (Lane
  Wiring owns "Base going-live" + lane-vs-pool registration; S8 routed it there separately).
ENFORCED POLICY CHAIN (getPolicies(MBT, selector) on engine 0xa1a5), re-establishes S8's 2026-10-13 blk
48066670 reading:
- transfer 0xa9059cbb / transferFrom 0x23b872dd / mint 0x40c10f19 -> [AllowPolicy, PausePolicy, Validator].
- CONTROLS all [] (discriminating, rules out false all-green): burn 0x9dc29fac []; nonsense 0xdeadbeef [];
  wrong target (pool addr) + transfer [].
CREDENTIAL GATE discriminates (validator 0x6dea, validate(address,bytes)(bool) - signature taken from the
compiled ABI, NOT guessed; a first guess validate(address) reverted = instrument fault, corrected):
- validate(0xFc9933C8..Fd33c, 0x) = TRUE (credentialed); validate(0x..dEaD,0x)=FALSE; validate(0x..0001,0x)=FALSE.

THREE HAZARD DISPOSITIONS:
(1) TWIN: handled - chainId 84532 confirmed; identity by typeAndVersion on Base; these are Base contracts.
(2) reserveFeed=0x0: CONFIRMED BY MY OWN LIVE READ MBT.reserveFeed()==0x0 (not just deployments.json).
    By-design hub-only reserves (dec_por_feed_every_rwa); NOT a missing dependency. (The disabling tx
    0x7c2a220e.. is S8-provided context, NOT re-verified by me; the live 0x0 is my read.)
(3) THREE policies not six: CONFIRMED - the enforced chain is exactly [Allow, Pause, Validator]. NO
    MaxBalance, NO TransferRestrict, NO GlobalSanctions. The dormant immutable sanctionsRegistry that BNB's
    build carries is an UNREAD on Base: common getters (sanctionsRegistry()/getSanctionsRegistry()/
    sanctionsList()) REVERT - and a reverting getter is NOT proof of absence (fb_error_selector_absent...).
    It does not move the wording gate regardless: enforcement runs through the policy chain, which has no
    sanctions policy (attached!=enforcing cuts the other way too - a dormant registry enforces nothing).

WORDING BINDING ON THIS PASS (and on anything Content writes off it):
- SAYABLE: Base Sepolia MBT is credential-gated (common.kyc via the identity validator) + allowlist + pause,
  PROVEN by the discriminating reads above.
- UNSAYABLE: "Base is sanctions-screened" (no sanctions policy); any MaxBalance / TransferRestrict claim.
  Per pol_never_claim_jurisdiction + pol_no_wallet_screen_claims.
- TESTNET ONLY (Base SEPOLIA 84532). ACE on testnet = NON-PRODUCTION under BUSL (dec_testnet_ace_busl_
  nonproduction); no mainnet / F-001 claim.
- COUNT: the "N EVM chains credential-gated" number must be RECOMPUTED FROM THE PER-CHAIN TABLE, never
  incremented by reflex (pol_allowlist_six_credential pins it at SEVEN today = READ THE TABLE;
  dec_chain_count_counts). Whether enabling Base changes N depends on whether Base was already counted -
  S8/Content owns that recount; V&L flags it, does not assert the new integer.
AllowPolicy/PausePolicy per-contract validate(address,bytes) reverted on my call (likely different ABI/
context); NOT investigated to resolution and NOT load-bearing - getPolicies wiring + identity-validator
discrimination already establish the gate.

### ADDENDUM 2026-11-14 (S8 accepted the PASS; two refinements + one OPEN decisive read)

ENFORCEMENT vs AVAILABILITY - the gate now SPLITS these (S8 refinement, V&L concurs):
- "Base ENFORCES credentials on chain" = TRUE of Base today, on my read (validator live + discriminating).
  NOT contingent on the VITE_BASE_ENABLED flip.
- "Base is a chain users can TRANSACT on" = NOT true while nobody can obtain the credential. The flip
  changes AVAILABILITY, not enforcement.
ADDED WORDING LINE (binding, on top of the PASS's wording block): Base must NOT appear in any external
material as an AVAILABLE or SUPPORTED chain until the ISSUANCE PATH exists, regardless of its enforcement
status. A chain that enforces a credential nobody can get is maximally gated and unusable; presenting it as
supported would mislead in the direction that matters. (Sanctions-screened/MaxBalance/TransferRestrict stay
UNSAYABLE; testnet-only stays; Legal: testnet ACE NON-PRODUCTION under BUSL incl. its fundraising-demo
character, desk-grade, counsel confirms at filing; F-001 = the ACE Additional Use Grant file is EMPTY so NO
production use is granted = real mainnet gate.)

OPEN - DECISIVE CREDENTIAL-PROVENANCE READ (S8 requested; V&L owns; HELD until after Sat per Ilan's word):
WHY: validate(0xFc9933C8..,0x)=true means a credential EXISTS on Base Sepolia for 0xFc99 - BUT the KYC
issuer box CHAINS env (Ilan read ~90min before S8's msg) issues ONLY to sepolia/amoy/plume/minato/shibuya,
NO Base. So the Base credential came off the normal issuance loop. Three NON-equivalent paths: (a) manual
testnet backfill (policy-bounded; flip blocker stands as written); (b) OUR credential MIRROR writes to Base
(then Base HAS a non-CHAINS issuance path; blocker wrong/incomplete; the mirror is ours per
ref_no_standard_provides_crosschain_identity_propagation); (c) something else wrote it = its own finding.
This decides whether the flip has TWO preconditions or THREE. Do NOT infer it - READ it.
READ (on the Base Sepolia CredentialRegistry, chain-qualified to 84532, resolve the registry from the
validator's getCredentialSources(typeId) on Base, not from memory):
  1. 0xFc99's credential RECORD: issuer, issuedAt, expiresAt. expiresAt=0 = NEVER-EXPIRES/STANDING (global
     TTL env defaulting 0; already confirmed on-chain BNB+Sepolia per prj_kyc_credential_expiresat_zero_
     standing) = no TTL safety net behind a missed revocation. Check whether Base's record is also 0.
  2. HOW MANY credentials on Base + WHICH addresses = THE discriminator: a handful of ops addresses reads
     as backfill; a set tracking the KYC'd user population means something MIRRORS and the blocker changes
     shape.
  3. ISSUER OF RECORD on Base vs the issuer identity the service uses on its five issuing chains.
METHOD (locked, per S8 + false-absence discipline): per-event sweeps, ONE event signature per cast logs
call, unioned in post - NEVER two signatures in one call (they become topic0 AND topic1, never OR = a clean
empty that mimics "no credentials"). If no issuance event is found that is NO RESULT, not zero, UNLESS a
POSITIVE CONTROL of the same kind fires (fb_false_absence_catalog). validate()=true is itself an
exercise-from-store (fb_credential_not_stored_until_exercised), so the credential IS effectively present;
the sweep establishes PROVENANCE + POPULATION, not existence. RE-READ TRIGGER: run when Ilan clears the
post-Saturday Base work (a reason-scoped hold is not an expiry - fb_a_reason_is_not_an_expiry).
CONTEXT UPDATE 2026-11-14 (PEER-REPORTED via S8; NOT my read - to verify myself on the sweep): Base AND BNB
are DELIBERATELY outside the service onboarding loop. The .env.example reason for excluding Base ("no CCID
validator") is STALE - Base got validator 0x6dea0c23 on 2026-08-14 (I independently read 0x6dea LIVE as
CredentialRegistryIdentityValidatorPolicy 1.0.0 on Base; the DATE is peer-reported) while the exclusion is
current = a stale reason on a current decision (fb_stale_pointer_outlives_corrected_body). CONSEQUENCE: 0xFc99
is the disallow authority on all 7 chains, so validate(0xFc99)=true on Base is ALMOST CERTAINLY the ADMIN EOA,
NOT a user credential - it tells us about ops, not users. THE WHOLE QUESTION is therefore item 2: if the Base
credential set is {0xFc99} ALONE, Base has NO user issuance path and S8's third precondition stands; a broader
set tracking the KYC'd population means something MIRRORS. I verify the population myself; I do not adopt the
peer facts as mine until the sweep confirms them.

RESOLUTION 2026-11-14 - PROVENANCE READ RUN (V&L, read-only, Base Sepolia 84532; S8 released it from the
Canton hold, and a read-only chain query is my own autonomous gate function, not an Ilan-gated action). Reads
stamped: validate controls block 58535238 @ 04:01:56Z; enumeration + records block 58539834 @ 04:17:15Z.
RESOLVES item 2 (the discriminator) and items 1/3.
- STACK (resolved from the validator on-chain, not memory): validator 0x6dea (owner 0xFc99); one requirement
  id 0x04a5970d..., credentialTypeIds=[0x455af80b0708d4...], minValidations=1, invert=false; the source for
  that type = identityRegistry 0x53d88a3Fe9480767bE210CbC2e2b3E14145D087d (IdentityRegistry 1.0.0),
  credentialRegistry 0xbf9206B3F6B335875A242899C472C2C440FBa02F (CredentialRegistry 1.0.0, creator 0xFc99 per
  Blockscout), dataValidator 0x0 (credential existence alone suffices).
  TWIN HAZARD HANDLED: 0x53d8 is a BurnMintTokenPool under "sepolia" in deployments.json, but reads as
  IdentityRegistry 1.0.0 on 84532 - resolved by typeAndVersion on the correct chain (fb_cross_chain_address_twins).
- INSTRUMENT FAULT CAUGHT + CONTROLLED: getCredentialSources is keyed by the credential TYPE id, NOT the
  requirement id. Querying with the requirement id returned [] (a clean false-empty). The discrimination control
  (validate true for 0xFc99/0xCf30, false for principal signer 0xd633 + dead + zero) PROVED sources exist, so
  the empty was an instrument fault; re-query with the type id returned the real source. Control pair on the
  record (fb_false_absence_catalog, fb_selftest_matcher_not_scope_filter).
- ARCHIVAL-FLOOR GOTCHA: publicnode serves state only to ~block 50M; the three registrations sit at ~45.456M,
  BELOW the floor - a publicnode log scan from the floor would have returned a false empty. Full history taken
  from Blockscout v2 address-logs (no 50k range cap, single page, next_page_params null). Controls (the two
  ccids I already knew held the credential) both appear in the pull = range covers them.
- COMPLETE PASSING SET (validate(account,0x)=true), exactly THREE ccids holding credential 0x455af80b, each
  valid-now=true, isCredentialExpired=false, expiresAt=0 (STANDING/never-expires, per prj_kyc_credential_
  expiresat_zero_standing), credentialData=0x; all three CredentialRegistered in one tight window
  (blocks 45456097 / 45456099 / 45456650) by the admin = a bootstrap batch, not organic onboarding over time:
  1. ccid 0x9fa0f6b1.. -> 0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c (EOA) = OPS ADMIN (validator+registry
     owner, credential-registry creator, disallow authority on all 7 chains).
  2. ccid 0x9fa7af6a.. -> 0x49f1aa7C252183E79D4944c54698a335Ed65C150 (CONTRACT, BurnMintTokenPool 1.5.1) =
     PROTOCOL INFRASTRUCTURE (the Base CCIP token pool, credentialled so it is a valid compliance counterparty).
  3. ccid 0x16c9d968.. -> 0xCf309940276b1e1396AED22aC4e1E7D2E3dd27d6 (EOA) = INTERNAL TEST / DEMO wallet
     ("RWA-KYC-Approved": RUNBOOK L489 "KYC-approved investor wallet | Internal test, allowlisted";
     PROJECT_STATE Stellar demo pair; this ledger L2945 "demo KYC wallet").
- VERDICT: the peer's simplifying premise "{0xFc99} ALONE" is MEASURED FALSE - the set is 3 accounts. BUT S8's
  THIRD PRECONDITION (Base has NO external/user issuance path) STANDS, on a corrected and stronger basis:
  every credentialled account is OB-INTERNAL (ops admin + protocol pool + internal test wallet), all
  admin-bootstrapped in one block window; there is NO external KYC journey issuing credentials to third parties
  on Base. So the flip still carries the issuance-path precondition.
  DURABLE WORDING (binding): do NOT record the basis as "{0xFc99} alone" - a future validate() read of 0xCf30 or
  the pool would refute that phrasing and spuriously re-open the question (fb_stale_pointer_outlives_corrected_
  body). Record it as "all Base credentials are OB-internal and admin-bootstrapped; no external issuance path."
- STANDING-CREDENTIAL NOTE: expiresAt=0 on all three = no TTL safety net, but all OB-internal and Base is off
  the onboarding loop, so this is bootstrap state, not live-user exposure. Pre-mainnet carry: revocation
  authority on this surface is 0xFc99 (prj_evm_revocation_authority_fc99_all_seven).
- UNCHANGED BY THIS READ: the PASS (credential-gated + allowlist + pause) and the AVAILABILITY-vs-ENFORCEMENT
  split above both stand; this read fills the provenance/population gap, nothing in the PASS moves.

### ADDENDUM 2 2026-11-14 - POLICY-ORDER SHORT-CIRCUIT HYPOTHESIS (S8 raised off my own chain-order read; UNMEASURED)

CLAIM (hypothesis, NOT a fact - held for test): I measured the enforced chain as [Allow, Pause, Validator]
with Allow FIRST. Standing ACE semantics: an `Allowed` result SHORT-CIRCUITS remaining policies
(fb_ace_allowed_result_short). PAUSE and VALIDATOR are both DOWNSTREAM of Allow. So IF our deployed
AllowPolicy returns `Allowed` for an allowlisted address, an allowlisted holder bypasses BOTH the credential
validator AND the pause policy => "pausing the token does not stop allowlisted holders" => which would
undercut "allowlist + pause", wording THIS PASS cleared as sayable.
CRITICAL UNKNOWN (decides it): what does OUR AllowPolicy return for a NON-allowlisted address?
`Allowed`(allowlisted) + fall-through(non-allowlisted) = PERMISSIVE BYPASS (credential OR allowlist
suffices; allowlisted short-circuits everything incl. pause). `Continue`/`PolicyRejected` = RESTRICTIVE
GATE (both required, nothing bypassed). Documented ACE semantics and OUR bytecode could differ - do NOT
infer from the standard, MEASURE (fb_exercised_code_outranks_a_citation).
THE READ (composition, NOT validate() in isolation; read-only via eth_call of the REAL transfer path from
addresses in each state; batched with ADDENDUM-1, HELD until after Sat):
  allowlisted + credentialled      -> expect allowed   (OUTER - tells us nothing new)
  allowlisted, NO credential       -> DISCRIMINATOR: allowed = PERMISSIVE bypass; rejected = RESTRICTIVE
  NOT allowlisted + credentialled  -> mirror discriminator
  neither                          -> expect rejected   (OUTER; my 0xdEaD/0x0001 already suggest this)
The TWO MIDDLE ROWS are the whole experiment; a test of only the outer (expected-pass/expected-fail) rows
CANNOT separate permissive from restrictive (fb_gate_must_discriminate_two_worlds / fb_adversarial_test...).
If no address exists in a middle state, say NO RESULT - do NOT infer from the outer rows.
PAUSE-BYPASS proof path (two parts, so it is PROVEN not inferred): (i) composition read shows
allowlisted-no-credential = allowed => Allow short-circuits the validator (observed); (ii) read the
PolicyEngine evaluation logic (source/bytecode) to confirm first-`Allowed`-wins short-circuit => pause,
being downstream, is never evaluated for an allowlisted holder. (i)+(ii) prove the pause bypass WITHOUT
pausing. The DEFINITIVE live confirmation (allowlisted holder transfers while token actually PAUSED) is a
STATE CHANGE = testbed item = Ilan's word + burn-guard precondition (pol_mbt_testbed_burn_guard_precondition);
NOT mine to trigger and S8 is NOT asking for it now.
INTERIM WORDING BIND [SUPERSEDED / LIFTED 2026-11-14, see RESOLUTION below - do not read as active]: (was)
the "+ pause" leg of this PASS is PROVISIONAL/AT-RISK until the composition read resolves the middle rows.

RESOLUTION 2026-11-14 - HYPOTHESIS REFUTED IN THE SAFE DIRECTION; BIND LIFTED; PASS WORDING STANDS.
S8 self-refuted the pause-bypass hypothesis on a live read that landed as S8 wrote it. Basis:
- PEER read (another channel, Sepolia, 2026-11-14T03:48Z, keyless eth_call - PEER-REPORTED, not my read):
  getPolicies(MBT,transfer) index 0 typeAndVersion = "AllowPolicy 1.0.0" (NOT a BypassPolicy); AllowPolicy
  1.0.0 AND PausePolicy BOTH return `Continue`; ONLY BypassPolicy returns `Allowed`. => the validator at
  index 2 ALWAYS runs for an allowlisted holder; pause is NOT bypassed; the allowlist and credential
  surfaces do NOT collapse.
- MY corroborating read (already in this entry, twin-safe): Base index 0 = "AllowPolicy 1.0.0" 0x2b7B, same
  contract CLASS as Sepolia's, identity taken from the Base contract itself.
BIND LIFTED: "credential-gated + allowlist + pause" STANDS exactly as the PASS cleared it. The pause leg is
no longer at-risk. The pause TESTBED item (allowlisted-transfer-while-paused) is WITHDRAWN by S8 - do NOT
ask Ilan for a pause ceremony; there is nothing to prove.
CAVEAT (keeps the read alive, non-urgent): the refutation's BEHAVIOURAL half is a PEER read on SEPOLIA +
a POLICY-CLASS inference to Base; it is better-grounded than the original hypothesis but still an inference
about what a type returns (fb_exercised_code_outranks_a_citation). The Base middle-row read below CONVERTS
that to a Base-measured fact. Expected answers now FLIPPED:
  allowlisted + credentialled      -> allowed   (outer)
  allowlisted, NO credential       -> REJECTED  <- now a genuine MUST-FAIL: a PASS here = bypass IS real
                                                   after all = re-open the wording gate immediately
  NOT allowlisted + credentialled  -> allowed   (surfaces independent)
  neither                          -> rejected  (outer)
RE-OPEN TRIGGER: row 2 PASSES. Until the read runs, wording stands on the peer-read + class-identity basis.

### ADDENDUM 3 2026-11-14 - BASE ISSUER-OF-RECORD (load-bearing) + ADMISSION INSTRUMENT (measured) (V&L, read-only)

Context: provenance set resolved (ADDENDUM 1 RESOLUTION) = {0xFc99 ops admin, 0x49f1 pool, 0xCf30 internal
test wallet}, three OB-internal accounts. This closes the two REMAINING questions S8 + KYC flagged. CONVERGENCE
NOTE: my independently-measured set (2026-11-14) EXACTLY equals the 2026-08-14 bringup record in
prj_base_not_credential ("superset restored {0xFc99, pool, 0xCf309940}") AND S8's code-side prediction
(backfill, no mirror; the only "mirror" in the service/ccid tree is a VerificationBadgeSBT display badge never
read for admission). Three independent sources agree on the same 3-set, stable over 3 months = backfill
confirmed from the chain side, no credential-propagation mirror, no external user population. S8's third
precondition STANDS.

A. ISSUER-OF-RECORD ON BASE (was item 3; became the load-bearing precondition for adding Base to the KYC
   service CHAINS). Mechanism: CredentialRegistry.registerCredential is gated by the `runPolicy` modifier, NOT
   a bare owner - writes pass the registry's attached PolicyEngine. Live reads (block 58543218 @ 04:28:32Z):
   - Both registries (CredentialRegistry 0xbf92, IdentityRegistry 0x53d8) share PolicyEngine
     0x90250A98538159fA7A2306FAeA9D84e9deC24C77 (PolicyEngine 1.0.0, the RegistryAdminEngine).
   - getPolicies(0xbf92, registerCredential 0x8ab73618) = [0xf88882929AA3ABcDf6A9B976AA33B4158C918c1C] =
     the IssuerGate, typeAndVersion "OnlyAuthorizedSenderPolicy 1.0.0", owner 0xFc99.
   - senderAuthorized(addr) on the IssuerGate (instrument discriminates, 2 true / 3 false):
       0x3055698887538cc6cF0d408e744B04F0806CB6E1 (service hot key) -> TRUE
       0xFc9933C8896715c1f3ADF9b8250ac051a95Fd33c (admin / gate owner)-> TRUE
       0xCf30.. -> false; 0xd633 principal signer -> false; 0xdEaD -> false
   VERDICT: on the CHAIN side, 0x3055 is ALREADY an authorized issuer on Base. IF the service CCID_ISSUER_KEY
   is 0x3055 (as on its five issuing chains), adding Base to CHAINS is a CONFIG CHANGE, not a key-authorization
   ceremony. The service-key identity itself is OFF-CHAIN (service-config fact, KYC's lane); I certify only the
   chain side, which is satisfied. The "rejected-write returns ok:true + green assertion" risk (KYC's finding)
   is MOOT for 0x3055 on Base since it is authorized. If the service key turns out NOT to be 0x3055, that key
   needs a senderAuthorized=true grant (0xFc99-owned IssuerGate) = a ceremony; so the off-chain key check is
   the one remaining branch.

B. ADMISSION INSTRUMENT (the composition instrument KYC asked to reuse; now MEASURED, not merely designed).
   Admission entrypoint = MBT token 0x2aedDb48 transfer/transferFrom; getPolicies(MBT, transfer 0xa9059cbb) =
   [AllowPolicy 0x2b7B, PausePolicy 0x55d0, Validator 0x6dea]. The token carries the `runPolicy` MODIFIER on
   transfer/transferFrom/mint (RWATokenCCT.sol :243/:249/:270), which runs the policy chain BEFORE the body's
   balance check (InsufficientBalance() 0xf4d678b8 at :485/:493). CONSEQUENCE: a zero-balance COMPLIANT wallet
   reverts on InsufficientBalance AFTER passing compliance - distinguishable from a policy reject - so the
   zero-balance false-negative is handled by REVERT-SHAPE classification, NOT amount=0 (risks hook skip) or a
   balance stateOverride. Measured eth_calls (block 58544060 @ 04:31:20Z, to=0xFc99 allowlisted+cred to avoid
   recipient-side confound, amount=1):
   - from=0xdEaD (not allowlisted) -> revert 0xfbdad4f4 (PolicyRunRejected; decodes AllowPolicy 0x2b7B +
     "address is not on allow list") = NOT ADMITTED (allowlist gate).
   - from=0xCf30 (allowlisted+cred, zero balance) -> revert 0xf4d678b8 InsufficientBalance = ADMITTED (passed
     all three policies; balance the only remaining stop). This EMPIRICALLY proves the policy-before-balance
     order on Base.
   CLASSIFIER (locked): policy-reject shape (0xfbdad4f4 allowlist, or the validator-reject shape) = NOT ADMITTED;
   success OR 0xf4d678b8 InsufficientBalance = ADMITTED. Subject = `from` (candidate sender); hold `to` to a
   known allowlisted+credentialled addr.
   ROW COVERAGE on Base: natural set is all-allowlisted-AND-all-credentialled (allowlist set == credential set),
   so the two MIXED/adversarial rows have NO natural holder:
   - allowlisted + NO credential (validator-reject, the critical must-fail): needs stateOverride synthesis
     (allowlist a fresh uncredentialled addr; positive control = same addr WITHOUT override reverts 0xfbdad4f4,
     WITH override must revert the VALIDATOR shape, not pass). NOT yet re-measured live today. Note: this row IS
     Base-measured, dated 2026-08-14, in prj_base_not_credential ("state-override isolation:
     allowlisted-but-uncredentialed rejected by the VALIDATOR") - so the middle-row read is a RE-VERIFICATION of
     a 3-month-old Base measurement, not a first measurement, and nothing is class-inferred across chains.
   - NOT allowlisted + credentialled: 0xfbdad4f4 above measures the not-allowlisted direction (sender gate fires
     regardless of credential); isolating "credentialled but not allowlisted" also needs synthesis.
   This advances the ADDENDUM-2 caveat: the ADMITTED and allowlist-REJECT directions are now BASE-MEASURED
   (not class-inferred); only the validator-reject middle row remains for live re-verification (held per S8,
   post-Saturday; brought forward only as KYC's shared instrument, read-only, no push).
   FOR KYC's admission-aware post-write assertion: assert the real composite path (eth_call MBT.transfer from
   the just-credentialled holder; ADMITTED-shape = fully admitted, 0xfbdad4f4 = credentialled-but-NOT-allowlisted
   = exactly the gap the current validate()-only assertion misses), rather than two independent getter checks.

   CORRECTION 2026-11-14 (block 58553953 @ 05:04:19Z) - my "amount=0 risks a hook short-circuit; avoid it"
   guidance above is REFUTED, and I converged on KYC's shape (verify criticism both ways). KYC (PR #591) ships a
   ZERO-AMOUNT SELF-transfer: eth_call transfer(wallet, 0) with from=wallet. Because runPolicy is a MODIFIER it
   runs the full chain BEFORE the body, and AllowPolicy/Pause/validator key on addresses+state, not amount, so
   amount=0 and from==to do NOT skip the policy. Measured on Base, KYC's exact shape:
   - 0xdEaD self-transfer-0 -> revert 0xfbdad4f4 "address is not on allow list" (policy RAN; NOT a false-admit).
   - fresh 0x..DeadBeef self-transfer-0 -> same revert (not address-specific).
   - 0xCf30 (allowlisted+cred) self-transfer-0 -> returns `true` (clean ADMITTED signal, no InsufficientBalance).
   KYC's shape is CLEANER than my amount=1 (admitted = clean true vs my InsufficientBalance) and its classifier
   is safe in both directions: only a clean success = ADMITTED; PolicyRunRejected reason-string "address is not
   on allow list" = not-allowlisted; any other reason = other-policy-reject verbatim; undecodable = unknown,
   NEVER admitted. The self-transfer exercises BOTH gates (allowlist via string-match, credential via
   other-reason-not-admitted), so it catches credentialled-but-unallowlisted AND allowlisted-but-uncredentialled.
   This also converts KYC's Sepolia validation to a BASE-measured must-fail. CONVERGED: one instrument, KYC's
   zero-amount self-transfer, is the adjudicated shape; my amount=1 variant is superseded.

   FINAL SHIPPED SHAPE (KYC PR #592, Base-confirmed block 58561894 @ 05:30:47Z): SELF-transfer (from=to=
   candidate), amount=1, classify success OR 0xf4d678b8 InsufficientBalance = ADMITTED, 0xfbdad4f4 = NOT
   admitted (reason-string). Measured on Base: 0xCf30 (balance 0 on Base) self amount=1 -> InsufficientBalance
   (admitted branch genuinely exercised); 0xdead self amount=1 -> 0xfbdad4f4 (not admitted). SELF-transfer
   ENDORSED over to=fixed-allowlisted: it checks the candidate as BOTH legs (sender AND recipient = the real
   admission question for a freshly-KYC'd holder) and drops the per-chain known-good-recipient dependency. The
   classification is ROBUST to per-chain token-internal ordering (balance-check vs from==to short-circuit)
   because runPolicy is a modifier (always first, so non-admitted ALWAYS reverts at the policy) and BOTH success
   and InsufficientBalance map to ADMITTED. amount=0 and amount=1 are classification-equivalent (differ only in
   the zero-balance admitted signal: success vs InsufficientBalance, both ADMITTED). One instrument, adjudicated.
   POLICY-FIRST PINNED ON BOTH CHAINS (the discriminator = a ZERO-balance NON-allowlisted address: balance-first
   would give 0xf4d678b8, policy-first gives 0xfbdad4f4): BASE (block 58565229) 0xdEaD + 0x..DeadBeef, balanceOf
   = 0 both, self amount=1 -> 0xfbdad4f4 (not InsufficientBalance) = policy ran FIRST. SEPOLIA (KYC, PR #593
   comment-only) 0xdEaD + 0x..CafeBabe, bal 0, -> 0xfbdad4f4. So policy-before-balance is a MEASURED fact on
   both the ship-to chain and Sepolia, not an inference. KYC #592 merged (ded781f).
