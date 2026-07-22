# Security Review: RWATokenV2 + YieldTokenV2 (ERC-3643 issuer primitives)

**Date:** 2026-07-20
**Reviewer:** Verify & Ledger gate (independent of the deploying channel)
**Scope:** `src/RWATokenV2.sol` (547 lines) and `src/yield/YieldTokenV2.sol` (540 lines) at `origin/main`
(RWATokenV2 last changed `f0fe771`, YieldTokenV2 `d73d761`, both 2026-07-18).
**Why fresh, not cite-prior:** these are NEW contracts. The existing reviews
([SECURITY-REVIEW-2026-06-17](SECURITY-REVIEW-2026-06-17.md), [MULTIASSET-SS1](MULTIASSET-SS1-AUDIT-2026-07-16.md))
cover v1 `RWATokenCCT` / `YieldToken` and do NOT cover this source. The new surface is the security-sensitive
kind: agent primitives that MOVE USER FUNDS without holder consent.

**Deployed instances reviewed (Sepolia 11155111):** RWATokenV2 `0xE09c01A6e308563fCd0357EC859A07CFB6ECEAdF`,
YieldTokenV2 `0x70d56FEA4bDD7E140e85687A1fa29Dae81Ba7d16`.

## Verdict: 0 Critical, 0 High, 1 Medium (configuration hazard, NOT live), remainder Informational.

**Live exposure today: NONE.** Verified on-chain at review time: `isAgent` is false for every probed
address, `mintBurnAuthorities` is false for the pool and the issuer, `totalSupply == 0`, and the v2 stack is
UNWIRED (`getPolicies(v2MBT, transfer|transferFrom|mint)` is EMPTY on the shared engine). Additionally
RWATokenV2's `admissionAllowPolicy` is unset, which makes the agent bypass paths revert
`AdmissionPolicyUnset()` outright. Every finding below is forward-looking, to be resolved BEFORE Phase A
steps 3-4 (config + policy attach).

> ### PRECISION CORRECTION: "no authority / no agents" does NOT mean "nobody can do it"
> An earlier wording of this review, and of the surrounding gate messages, described the unwired window as
> safe because there is no mint authority and there are no agents. **That is too strong, and a future reader
> could take it as a mechanical guarantee and skip the attach-before-mint ordering on that basis.** Both
> modifiers grant the OWNER an implicit bypass of their respective mappings:
> ```
> onlyMintBurn: if (!mintBurnAuthorities[msg.sender] && msg.sender != owner()) revert Unauthorized();
> onlyAgent:    if (!_agents[msg.sender]            && msg.sender != owner()) revert Unauthorized();
> ```
> So the owner (`0xFc99...`) can mint even though `mintBurnAuthorities` is false for every address, AND is an
> implicit AGENT able to call `forcedTransfer` / `recoveryAddress` / freeze even though `isAgent` returns
> false everywhere. `isAgent(owner)` returning false is therefore MISLEADING as a reachability check: it
> reports mapping membership, not authorisation.
>
> **The safety of the unwired window is PROCEDURAL (nobody mints or moves funds before the attach), not
> MECHANICAL.** In practice the risk is low, since the owner is the project's own deployer and supply is 0,
> but the ledger and runbook must state what is actually true. The attach-before-mint ordering is a rule that
> must be FOLLOWED, not a property that is enforced. (Owner-bypass on `onlyMintBurn` identified by RWA Bridge
> Session 8; the same pattern on `onlyAgent` found while verifying that claim against the source.)
>
> **PROVEN ON THE DEPLOYED BYTECODE, not inferred from source** (two distinct revert selectors; the owner
> gets FURTHER INTO the function than a non-agent, which is what demonstrates it clears the modifier):
> `forcedTransfer` from the OWNER reverts `0x24a0b974` = `AdmissionPolicyUnset()`, i.e. it cleared
> `onlyAgent` and died later at the admission gate. The same call from a non-owner/non-agent reverts
> `0x82b42900` = `Unauthorized()`, i.e. it never cleared the modifier. Meanwhile `isAgent(owner)` reads
> **false**. (Differential contributed by RWA Bridge Session 8.)
>
> ### THE BOUND: the owner bypasses the AUTHORITY check, NOT compliance. Read this before the rest.
> Without this bound the finding reads far worse than it is, and an over-read finding in a public document
> is its own defect. Measured on MAINNET Base 8453 against the live MBT `0xB40195A6...` (owner = A2), by the
> same differential technique:
>
> | Call | Result | Meaning |
> |---|---|---|
> | `mint(nonAllowlisted, 1)` FROM OWNER | revert `PolicyRunRejected` naming **AllowPolicy `0xf5BC0a32`** ("address is not on allow list") | owner CLEARS the authority gate, then is stopped BY COMPLIANCE |
> | `mint(nonAllowlisted, 1)` FROM non-owner | revert `0x82b42900 Unauthorized()` | never reaches compliance at all |
> | `mint(A2, 1)` FROM OWNER (allowlisted + credentialed) | **SUCCESS** | compliance PERMITS the legitimate case, i.e. the chain is not simply blocking everything |
>
> **So: the privileged CALLER set is understated; the COMPLIANCE GUARANTEE IS NOT WEAKENED.** For `mint`,
> `transfer` and `transferFrom` the full ACE chain still runs for the owner exactly as for anyone else, so an
> owner mint to a non-allowlisted or uncredentialed recipient REVERTS. The CLAUDE.md hard invariant
> "compliance at every transfer" is INTACT.
>
> **The one place that is NOT true is exactly F-1, and the two findings are one story at two levels.**
> `forcedTransfer` / `recoveryAddress` bypass the policy engine BY DESIGN and self-enforce through
> `_requireAdmitted`. So on those paths the owner-as-implicit-agent means the owner can move funds with only
> `_requireAdmitted` in the way, and `_requireAdmitted` SKIPPED the credential leg whenever
> `admissionValidator` was unset. That is precisely the hole PR #277 closed by making a zero validator
> revert:
> - `mint` path: owner implicit, policy chain APPLIES, fully gated.
> - `forcedTransfer` path: owner implicit, policy chain BYPASSED by design, gated ONLY by `_requireAdmitted`,
>   so credential enforcement there depended entirely on the validator being set.
>
> The agent SET being understated (this finding) and the agent PATH's compliance being optional (F-1) are the
> same story; stated separately they understate the connection, and together they explain why #277 mattered.
> (Bound identified by RWA Bridge Session 8; the permit-control row is this gate's addition.)
>
> ### The generalisable finding: these are MEMBERSHIP queries being read as AUTHORISATION queries
> `isAgent` and `mintBurnAuthorities` answer "is this address in the mapping", NOT "can this address do the
> thing". Where the two diverge (here, an implicit owner) any enumeration built on the getter, or on
> `AgentSet` / `MintBurnAuthoritySet` events, understates the privileged set **permanently** - and no amount
> of careful event scanning fixes it, because the owner never appears in such an event. Contrast the
> allowlist enumeration elsewhere in this ledger, which IS complete precisely because `allowAddress` was
> established as the only writer and always emits. Here the equivalent statement is FALSE. State it plainly:
> **effective minter set = `mintBurnAuthorities` PLUS `owner()`; effective agent set = `_agents` PLUS
> `owner()`; effective CCIP-admin set = `ccipAdmin` PLUS `owner()`.**
>
> **Sweep across ledgered contracts (source, `origin/main`). This is NOT v2-only. 8 true bypass sites:**
>
> | Contract | Implicit owner role(s) | Note |
> |---|---|---|
> | `RWATokenCCT.sol` (v1) :73, :202 | minter, ccipAdmin | **LIVE on Sepolia/Amoy/Plume/Minato/Shibuya/Base AND the mainnet testbed** |
> | `RWATokenV2.sol` :144, :149, :438 | minter, AGENT, ccipAdmin | |
> | `YieldToken.sol` (v1) :105 | minter | live wherever v1 yield is deployed |
> | `YieldTokenV2.sol` :134, :139 | minter, AGENT | uses `owner` state var, not `owner()` |
>
> Plain `onlyOwner` checks (`LendingVault` :89/:95, `KycAllowlister` :47, and the `onlyOwner` modifiers
> themselves) are NOT bypasses and are excluded: there the owner IS the role, no mapping is being bypassed.
> A ledger sweep found no existing entry asserting an agent/minter COUNT, so nothing already recorded needs
> retracting; this note exists so no future entry makes that claim from a getter or from events.
>
> **Method note against this very sweep:** an initial grep for `msg.sender != owner()` returned ZERO for
> `YieldTokenV2`, which would have been recorded as "v2 yield token diverges, no bypass". False. That
> contract uses `owner` as a public STATE VARIABLE, so the call-form pattern missed it while the property
> was present. A grep miss is not a factual negative. The corrected pattern matches both forms.

---

## F-1. MEDIUM (configuration hazard, not a code defect): credential check FAILS OPEN on the agent bypass paths

`forcedTransfer` and `recoveryAddress` deliberately bypass the PolicyEngine, so they enforce admission
themselves via `_requireAdmitted`:

```
if (address(admissionAllowPolicy) == address(0)) revert AdmissionPolicyUnset();   // fails CLOSED
if (!admissionAllowPolicy.addressAllowed(recipient)) revert RecipientNotAdmitted; // enforced
if (address(admissionValidator) != address(0) && !admissionValidator.validate(recipient, "")) revert;
                                              // ^ SKIPPED ENTIRELY when the validator is unset
```

The allowlist is mandatory (unset reverts) but the credential validator is optional (unset is silently
skipped). Once the v2 token is wired, its NORMAL path is credential-gated by the engine chain
`[AllowPolicy, PausePolicy, Validator]`. If `setAdmissionCheckers` is then called with the allowlist but a
zero validator, the result is an INVISIBLE ASYMMETRY:

- ordinary `transfer` / `transferFrom` / `mint`: allowlist AND credential enforced
- agent `forcedTransfer` / `recoveryAddress`: allowlist ONLY, credential NOT enforced

An agent could move tokens to an allowlisted-but-uncredentialed wallet, defeating the credential gate on
precisely the path that needs the most scrutiny (movement without holder consent). Nothing would look wrong:
`getPolicies` shows a perfect chain and normal transfers are correctly gated.

**Current state:** `admissionAllowPolicy == 0` and `admissionValidator == 0` on the deployed RWATokenV2, so
the bypass paths revert `AdmissionPolicyUnset()` and are inert. The hazard is the INTERMEDIATE configuration
(allowlist set, validator not).

**Recommendation.** `setAdmissionCheckers(allowPolicy, validator)` already takes both in one call, which
makes the correct action atomic. Add an explicit wiring invariant to the Phase A runbook and, ideally, an
assertion in the deploy/wiring script: **whenever a credential validator is attached to the token's policy
chain, `admissionValidator` MUST equal that validator.** Consider having the setter reject a zero validator
unless a deliberate override flag is passed, so the unsafe intermediate cannot be reached by omission.

## F-2. INFO (by-design centralization, record it): agents can move user funds without consent

`forcedTransfer` (arbitrary from -> admitted to), `recoveryAddress` (migrates an ENTIRE balance including
frozen tokens), and `freezePartialTokens` / `unfreeze` / batch variants are all `onlyAgent`. This is the
intended ERC-3643 issuer posture (court-ordered clawback, lost-wallet recovery, regulatory freeze), not a
defect, and it matches the "by-design centralization" flags already carried in this ledger. The consequence
to record: **agent key compromise is equivalent to the ability to move any holder's tokens to any allowlisted
address.** Agent key management is therefore security-critical, agents should be minimal and auditable, and
`addAgent` / `removeAgent` (both `onlyOwner`) should be monitored.

**The agent set is NOT just the `_agents` mapping.** `onlyAgent` grants the OWNER an implicit bypass
(`msg.sender != owner()`), so the effective agent set is `_agents` PLUS the owner, permanently and with no
way to observe it through `isAgent`. Enumerating agents via `isAgent` or via `AgentSet` events will therefore
UNDERSTATE who can move funds. Any future enumeration of "who can call the bypass paths" must include the
owner explicitly. The same applies to `onlyMintBurn` and `mintBurnAuthorities`.

## F-3. POSITIVE FINDING (design done right, worth recording): batch operations preserve compliance

The naive implementation of batch functions is a real compliance bypass: batch selectors carry no policy
attachments, so the engine's per-target default decides, and a default-allow engine silently skips
compliance for every batched leg. This implementation avoids that. `_runSingleOpPolicy` synthesizes the
SINGLE-OP engine run per item (same selector, same parameter encoding, original `msg.sender`), so
`batchMint` and `batchTransfer` each item passes the exact policy chain its single-op form would.
`batchMint` additionally performs ONE Proof-of-Reserve check on the batch TOTAL before minting, which is the
correct aggregation. Reviewed and confirmed correct.

## F-4. INFO: burn paths are not policy-checked (documented, matches v1)

`burn(uint256)`, `burn(address,uint256)`, `burnFrom`, and `batchBurn` run no policy check. This is explicit
in the source ("pool is infrastructure") and matches v1 behaviour, since the CCIP BurnMintTokenPool must be
able to burn on the bridge path. `burnFrom` correctly enforces allowance. Recorded as by-design, not a gap.

## F-5. LOW: external call in `recoveryAddress` (CEI correctly observed)

`recoveryAddress` calls `ccidRebinder.rebindWallet(...)` at the end. Checks-Effects-Interactions is correctly
observed: balances and freeze state are fully settled BEFORE the external call, and the source comments say
so deliberately. The rebinder is owner-set and currently unset (`0x0`) on both contracts. Residual risk is
limited to a malicious/compromised owner-configured rebinder re-entering after state is already final. No
action required; noted so the property is not lost in a future refactor that reorders the call.

## F-6. INFO: frozen-token accounting is correct

Ordinary `_transfer` moves only the UNFROZEN portion (`available = balance - frozen`, reverting
`InsufficientAvailableBalance`). `forcedTransfer` and the burn paths call `_unfreezeShortfall` first, which
is correct ERC-3643 semantics (a forced/regulatory action may reach frozen tokens; a voluntary transfer may
not). `_unfreezeShortfall` clamps the shortfall to the frozen amount, so it cannot underflow or over-unfreeze.
`recoveryAddress` migrates the freeze state onto the new wallet rather than silently clearing it, preserving
the restriction across recovery.

## F-7. INFO: reserve check fails closed

`_checkReserves` reverts on stale data (`block.timestamp - updatedAt > maxStaleness`) and on non-positive or
insufficient reserves. It is SKIPPED entirely when no feed is configured, which is by design and matches the
existing PoR posture in this ledger. A feed timestamp in the future would underflow and revert under Solidity
0.8 checked arithmetic, i.e. fails closed. Single `mint` checks the zero address and calls `_checkReserves`.

## F-8. INFO: YieldTokenV2 differences (its unset validator is CONSISTENT, not a downgrade)

YieldTokenV2 mirrors the same agent surface. Two material differences:
1. Its `allowPolicy` is IMMUTABLE and constructor-enforced non-zero (deployed: `0x7858b6e6...`, the live
   Sepolia AllowPolicy). Its agent bypass paths are therefore OPERABLE as soon as an agent is added, unlike
   RWATokenV2 which is inert while `admissionAllowPolicy` is unset. Adding an agent is the trigger to watch.
2. Its NORMAL path (`_complianceCheck`) is allowlist-only, matching v1 `YieldToken` parity. So its unset
   `admissionValidator` does NOT create the F-1 asymmetry: allowlist-only is already the posture on both its
   normal and agent paths. The optional validator is an available enhancement, not a missing control.
   Deployed state: `admissionValidator == 0`, `ccidRebinder == 0`, owner == the YieldIssuer `0x4AC18a2B`,
   zero agents, `totalSupply == 0`.

---

## ADDENDUM 2026-07-21: F-1 REMEDIATED AT THE CONTRACT LEVEL, PROVEN IN DEPLOYED BYTECODE

F-1 was fixed in the contract (PR #277, merge `2505dc4`) rather than only guarded in a deploy script, and
Ilan authorized the resulting redeploy. Because `YieldIssuer.rwaToken`/`yieldToken`, `YTMarket.ytToken`,
`LendingVault.collateralToken` and the pool's token are all `immutable` (verified at source by this gate:
no setter exists to re-point any of them), redeploying the token forced the ENTIRE 6-contract stack.

**The remediation is better than what this review recommended.** The review suggested an override flag;
the implementation instead adds a separate `setAdmissionCheckersUnsafe` that emits a distinct
`AdmissionValidatorWaived` event, so a deliberate waiver is AUDITABLE ON-CHAIN rather than inferred from a
zero field. `setAdmissionCheckers` now reverts `AdmissionValidatorRequired()` on a zero validator.

**Verified in the DEPLOYED BYTECODE, not merely in source.** "Deployed from a fixed branch" and "the fix is
on-chain" are different claims; only the second matters. Simulated FROM THE OWNER (otherwise `onlyOwner`
reverts first and the result would be mis-attributed), as a 4-way differential against the retired token:

| # | Call | Result | Meaning |
|---|---|---|---|
| 1 | NEW `setAdmissionCheckers(allow, 0)` | REVERT `0xa2bdd2c2` | `AdmissionValidatorRequired()`, selector derived independently. Fix IS on-chain |
| 2 | OLD `setAdmissionCheckers(allow, 0)` | SUCCESS | the hazard was REAL and is live on the retired token |
| 3 | NEW `setAdmissionCheckersUnsafe(allow, 0)` | SUCCESS | deliberate escape hatch present |
| 4 | OLD `setAdmissionCheckersUnsafe(allow, 0)` | REVERT (absent) | function did not exist pre-fix |

| 5 | NEW `setAdmissionCheckers(allow, VALIDATOR)` from owner | SUCCESS | POSITIVE CONTROL: guard rejects zero AND the function is still USABLE, i.e. not bricked |
| 6 | NEW `setAdmissionCheckersUnsafe` from a NON-owner | REVERT | escape hatch is correctly owner-gated |

Rows 2 and 4 (the retired-token control) are what make this a PROOF rather than a positive-only assertion:
a test that only shows the new token rejecting cannot distinguish "the fix works" from "the call failed for
an unrelated reason". Row 5 is equally load-bearing in the other direction: a fix that reverted
UNCONDITIONALLY with the same selector would satisfy rows 1-4 while leaving the function bricked. Reject-only
testing cannot see that, exactly as reject-only testing of a compliance attach cannot see an
over-restrictive validator.

**Two verification traps recorded, both empirically demonstrated rather than asserted:**
1. **The call MUST be simulated from the OWNER.** `setAdmissionCheckers` is `onlyOwner` and the modifier runs
   BEFORE the body, so a call from any other sender never reaches the guard. Measured: the same call from a
   non-owner reverts with `0x118cdaa7` = `OwnableUnauthorizedAccount(address)`, NOT `0xa2bdd2c2`. A naive
   "it reverted, therefore the fix is present" check would PASS here, and would pass identically against
   UNFIXED bytecode. It proves only that the caller is not the owner.
2. **Match the ERROR SELECTOR, never the mere fact of reverting.** Selectors computed, not recalled:
   `AdmissionValidatorRequired()` = `0xa2bdd2c2` (the only pass); `OwnableUnauthorizedAccount(address)` =
   `0x118cdaa7` and `Error(string)` = `0x08c379a0` are impostors that must be treated as FAIL.

**Remediated address set (Sepolia), on-chain leg PASS:** RWATokenV2 `0x661d63bE70A1eb9A904636F5dDcaDEeFdF6337B5`,
BurnMintTokenPool `0xA2eB25A10043C64096da1D97C1Fcc6259Bc909AC`, YieldIssuer `0xE3B2de4218C0Ab1C9149AAAe5135BF3D54d367c8`,
YieldTokenV2 `0x3043A478196b8c1F39561eB336A10823ea5b652F`, YTMarket `0x2401F3Bde7Bc6c0381B3ca030a4769DaA383938D`,
LendingVault `0xdeA110fDC0bE42d6c0832A12e5aE9ff075e92732`. Wiring verified internally consistent and pointing
ONLY at the new set (no stale reference to the retired addresses); MBT reads "Miami Beach Tower"/MBT/supply 0;
engine `0xd4b9F980` REUSED not redeployed; the new token is 11680 bytes vs 11511 pre-fix, consistent with the
added error/function/guard. New YieldTokenV2 shows the same immutable pattern (8614 bytes, allowPolicy
embedded at exactly 4 sites).

**RETIRED (pre-fix, 2026-07-20), superseded by the above:** `0xE09c01A6...`, `0xC3F2C961...`, `0x4AC18a2B...`,
`0x70d56FEA...`, `0x85cdE2C6...`, `0xE1f80f47...`. Recorded rather than deleted: F-1 is both the reason they
exist and the reason they were replaced. Row 2 above deliberately preserves evidence that the hazard was
live on the retired token.

**Findings F-2 through F-8 are UNCHANGED** and carry to the new set: the source differs only by #277, which
this addendum reviews. v1 remains UNTOUCHED (its 5-policy chain reads exactly as before) and the new stack is
UNWIRED (`getPolicies` empty), so the standing gate condition still applies: **policy-attach MUST precede any
mint, config or exposure**, and the attach must be verified with a PERMIT-proof as well as reject-proofs.

Still outstanding for the ledger entry: explorer-verification of the 6 new addresses.

## Method

Source read in full at `origin/main` (the local shared checkout was stale and did not contain these files;
reading the authoritative ref rather than the working tree matters here). Every claim about deployed state
was read from chain via keyed RPC, not taken from the deploying channel's report. Getter names were taken
from the source rather than guessed: an early call to a non-existent `validator()` reverted, and a reverted
call must never be recorded as "unset" (that is a false inference, not a data point) - the correct getter is
`admissionValidator()`.

**Not covered by this review:** economic/oracle-manipulation attacks on the PoR feed, cross-contract
interactions with the YieldIssuer/YTMarket/LendingVault instances, and formal verification. AI review does
not replace a professional audit before mainnet, per project policy.
