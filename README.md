# Onchain Bridges - Security & Verification

Public security artifacts for the [Onchain Bridges](https://rwa-testnet.onchainbridges.com)
compliance-aware cross-chain RWA tokenization protocol (Chainlink CCT / CCIP / ACE / CRE).

This repository hosts the verification and audit record for grant due-diligence and public
inspection. The protocol source itself is independently verifiable on each chain's block
explorer (every contract below is source-verified).

## Contents

- **[Security review](audits/SECURITY-REVIEW-2026-06-17.md)** - source-based static-analysis
  review, **dated 2026-06-17**, covering the networks and contracts in scope on that date (see
  that document's own Scope section, and "Review coverage" below). Three independent open-source
  analyzers (Foundry `forge lint`, Cyfrin `aderyn`, `solhint`) plus `cargo audit` for the Solana
  programs. Result on that date: **0 Critical / High / Medium** findings in the contracts then in
  scope. **That is a dated result from automated tooling, not a standing assurance about the
  contracts running today**, and static analysis does not find every class of defect - see
  "What this record is not" below.
- **[Verification + audit ledger](CONTRACT-VERIFICATION-AUDIT-LEDGER.md)** - every canonical
  contract, every chain, with its explorer-verified link.
- **[`audits/`](audits/)** - raw, reproducible tool output (aderyn, forge-lint, solhint,
  cargo-audit) backing the review.

## Review coverage

The 2026-06-17 security review covers these networks: Sepolia, Polygon Amoy, Sonic Testnet,
Soneium Minato and Plume Testnet (canonical protocol contracts source-verified on the respective
explorers), plus the Solana Anchor programs.

**Contracts deployed to additional networks after 2026-06-17 are not covered by that review.**
The protocol has been deployed to further networks since then. The up-to-date list of canonical
contracts and their explorer verification, across every network, is the
[verification + audit ledger](CONTRACT-VERIFICATION-AUDIT-LEDGER.md), which is maintained
continuously; this README's review section is not a substitute for it.

## What this record is not

Read this before drawing a conclusion from the result above.

- **It is not an audit.** No firm has read this code and staked its name on it. A full manual
  audit by a professional firm is planned before mainnet.
- **It is not a proof.** Nothing here is formally verified.
- **It is a dated measurement.** The review ran on one day against the then-current source. A
  deployed contract can be changed, added or superseded after a review without the review
  changing, so check the date against what you are looking at.
- **Static analysis finds known patterns, not all defects.** A clean tool run means the tools
  matched nothing they recognise. It does not mean no defect exists; logic errors specific to an
  application are routinely missed by every analyzer and found by people reading code.
- **Source-verified is not the same as reviewed.** Explorer verification proves the published
  source matches the deployed bytecode. It says nothing about whether that source is correct.

## A note on automated "rug-pull" scanners

Free by-address scanners (SolidityScan QuickScan, De.Fi) are mainnet rug-pull / honeypot threat
detectors. They flag owner-controlled mint, pause, and allowlist as risk - but those are the
**by-design compliance controls** (Chainlink ACE) that make this a regulated-asset protocol, not
a vulnerability. The source-based review above is the appropriate code-security view.

---

A full manual audit by a professional firm is planned before mainnet; the 2026-06-17 review
covers the testnet surface in scope on that date.
