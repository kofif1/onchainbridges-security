# Onchain Bridges - Security & Verification

Public security artifacts for the [Onchain Bridges](https://rwa-testnet.onchainbridges.com)
compliance-aware cross-chain RWA tokenization protocol (Chainlink CCT / CCIP / ACE / CRE).

This repository hosts the verification and audit record for grant due-diligence and public
inspection. The protocol source itself is independently verifiable on each chain's block
explorer (every contract below is source-verified).

## Contents

- **[Security review](audits/SECURITY-REVIEW-2026-06-17.md)** - source-based static-analysis
  audit across all live networks (EVM + Solana). Three independent open-source analyzers
  (Foundry `forge lint`, Cyfrin `aderyn`, `solhint`) plus `cargo audit` for the Solana programs.
  Result: **0 Critical / High / Medium** findings in the live contracts.
- **[Verification + audit ledger](CONTRACT-VERIFICATION-AUDIT-LEDGER.md)** - every canonical
  contract, every chain, with its explorer-verified link.
- **[`audits/`](audits/)** - raw, reproducible tool output (aderyn, forge-lint, solhint,
  cargo-audit) backing the review.

## Verified networks

Sepolia, Polygon Amoy, Sonic Testnet, Soneium Minato, Plume Testnet (all canonical protocol
contracts source-verified on the respective explorers), plus the Solana Anchor programs.

## A note on automated "rug-pull" scanners

Free by-address scanners (SolidityScan QuickScan, De.Fi) are mainnet rug-pull / honeypot threat
detectors. They flag owner-controlled mint, pause, and allowlist as risk - but those are the
**by-design compliance controls** (Chainlink ACE) that make this a regulated-asset protocol, not
a vulnerability. The source-based review above is the appropriate code-security view.

---

A full manual audit by a professional firm is planned before mainnet; this review covers the
testnet surface.
