# Digital Dirham: Central Bank Digital Currency for Morocco

**Status**: Research & Development (Phase 0: Planning)
**Last Updated**: May 2025
**Owner**: Bank Al-Maghrib Research Division

## 📌 Quick Links

- **[Executive Summary](#executive-summary)** - Start here (2 minutes)
- **[Full BIS Research Paper](./docs/research/BIS-RESEARCH-PAPER.md)** - Complete analysis (1 hour)
- **[Technical Architecture](./docs/ARCHITECTURE.md)** - For engineers
- **[Implementation Roadmap](./docs/implementation/PROJECT-PLAN.md)** - For project managers
- **[Compliance Framework](./docs/compliance/)** - For legal/regulatory

## 📋 Executive Summary

Digital Dirham is a central bank digital currency designed specifically for Morocco's financial landscape. It addresses three critical imperatives:

1. **50% of Morocco is unbanked** – Living primarily in rural areas with limited connectivity
2. **\$7B annual diaspora remittances** – Lost to 5-7% fees (Western Union, banks)
3. **35% lack reliable internet** – Making conventional digital payments impractical

### Unique Design Features

| Feature | Why It Matters |
|---------|----------------|
| **Offline-first** | Works without internet (USSD, NFC, SE) |
| **Privacy-tiered** | Anonymous for <500 MAD/day; full audit for compliance |
| **Multi-layer double-spend prevention** | <0.001% fraud rate (device + cryptographic + central) |
| **mBridge integration** | Sub-10-minute diaspora transfers (<0.5% cost) |
| **Hybrid ledger** | Rust settlement engine + Hyperledger Fabric audit trail |

### Key Metrics (Year 1 Target)

- **5M+ wallets** (12% of population)
- **1M+ daily active users**
- **<0.001% fraud/double-spend rate**
- **<1 second settlement (online); T+24h (offline)**
- **\$50M+ diaspora volume captured**
- **99.99% uptime**

## 🏗️ Architecture Layers

```
Device Layer (Offline)
  ├─ Smartphone app (iOS/Android, Go SDK)
  ├─ Feature phone USSD (*997#)
  ├─ SIM card applet (Java Card, 50KB)
  └─ Hardware wallet (optional, Thales HSM)
  
  ↓ NFC, USSD, LTE
  
API Layer (Online)
  ├─ Go API Gateway (gRPC + REST)
  ├─ Rate limiting, auth, monitoring
  └─ Load balanced (Kubernetes)
  
  ↓ gRPC
  
Settlement Layer
  ├─ Rust Settlement Engine
  ├─ 50K+ TPS throughput
  ├─ <500 microsecond latency
  └─ Multi-validator consensus (5-of-7)
  
  ↓ Atomic write
  
Ledger Layer
  ├─ PostgreSQL (3-AZ, synchronous replication)
  ├─ Canonical state (balances, immutable log)
  └─ Row-level security (RLS) + encryption (pgcrypto)
  
  ↓ Async publish
  
Audit Layer
  ├─ Hyperledger Fabric (7 nodes, BFT)
  ├─ Tamper-proof verification
  └─ Independent oversight (HRF, auditors)
```

## 🔐 Privacy Architecture

### Tiered Anonymity Model

**Tier 1: Anonymous (Entry-level)**
- Limit: 500 MAD/day
- KYC: None (phone number only)
- Privacy: Blind signatures (merchant cannot link payer)
- Use case: Street vendor, laborer, refugee

**Tier 2: Semi-Anonymous (Emerging Middle)**
- Limit: 5,000 MAD/day
- KYC: Name + DOB (basic)
- Privacy: Optional blind signature; optional audit
- Use case: Small trader, SME

**Tier 3: Identified (Banked)**
- Limit: Unlimited
- KYC: Full (FATF compliance)
- Privacy: Full audit trail (required)
- Use case: Bank customer, merchant, government employee

## 📱 Offline Payments (Core Innovation)

### Problem
35% of Moroccans live in areas with no reliable internet. Existing CBDC designs assume online connectivity.

### Solution
**Offline-first** architecture:
1. **Device-level settlement**: Secure Element (SE) signs vouchers locally
2. **D2D transfer**: NFC tap between phones (instant, no network)
3. **Deferred reconciliation**: Settlement when online (eventually consistent)
4. **Multi-layer double-spend prevention**: SE balance + nonce + Bloom filter

### Transaction Flow (Offline NFC)

```
T+0min:  Payer & Payee tap phones (NFC contact)
         SE-to-SE transfer of signed voucher
         Both balances updated locally
         Status: "Pending Sync"

T+0-24h: Either party comes online (LTE, USSD, agent)

T+24h:   Merchant syncs → Rust Engine → PostgreSQL
         Settlement confirmed, Fabric records

T+72h:   Payer syncs → Already settled
         "Sync successful (settled 48h ago)"

Result:  Offline transaction with eventual settlement
         No internet required for T+0 gratification
         Audit trail immutable (Fabric)
```

## 🌍 Cross-Border Integration

### mBridge (Multi-CBDC Settlement)

Digital Dirham integrates with mBridge for instant diaspora remittances:

```
Moroccan worker (Dubai) sends 1000 MAD to family (Fez):

T+0s:   Worker app: Send 1000 MAD
T+1s:   Dubai Dirham node: Validate + check sanctions
T+2s:   mBridge consensus: All 5+ nodes agree
T+3s:   Morocco Dirham node: Credit recipient
T+4s:   Both apps show: "Confirmed ✓"

Cost: 0 basis points (direct CBDC transfer)
Time: 4 seconds
vs. Western Union: 15min-3 days, 5.5% fee
vs. Bank transfer: 1-3 days, 2-3% fee
```

## 🛠️ Technology Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Settlement | **Rust** | Memory-safe, 50K+ TPS, <500μs latency, no GC |
| API Gateway | **Go** | Cloud-native, 1M+ concurrent, simple ops |
| Ledger | **PostgreSQL** | ACID, 3-AZ replication, pgcrypto, RLS |
| Audit | **Hyperledger Fabric** | BFT consensus, tamper-proof, proven in Africa |
| SE Applet | **Java Card** | Standard for SIM, proven (20M+ cards) |
| Crypto | **Ed25519, RSA, Groth16** | NIST-approved, audited |
| Deployment | **Kubernetes** | Auto-scaling, multi-cloud, standard |

## 📈 Implementation Timeline

### Phase 1: Foundation (Q1-Q2 2025) – \$2-3M
- Rust settlement engine
- PostgreSQL 3-AZ cluster
- Go API gateway
- SE firmware
- Pilot: 10K government employees

### Phase 2: Agent Network (Q3-Q4 2025) – \$4-5M
- NFC capability
- USSD feature phone support
- 10K agents in rural areas
- 1M users

### Phase 3: Diaspora Corridor (Q1-Q2 2026) – \$3-4M
- mBridge integration
- 2M diaspora users
- 10% of annual \$7B remittance volume

### Phase 4: Retail Expansion (Q3-Q4 2026) – \$2-3M
- 50K merchants
- QR code payments
- 5M+ total users

### Phase 5: Financial Services (2027+) – \$2-3M/year
- Loans, insurance, savings
- 10M+ wallets (25% of population)

**Total 3-year**: \$13-18M
**ROI**: 15-30x (annual benefit: \$240-425M by Year 3)

## 📚 Documentation Structure

```
research/                  – Academic research (BIS paper, literature review)
technical/                 – Implementation details (code specs, pseudocode)
architecture/              – System diagrams and transaction flows
compliance/                – Legal/regulatory framework
implementation/            – Project management (roadmap, budget, hiring)
comparative-analysis/      – Lessons from eNaira, eCedi, Digital Rand
security/                  – Threat model, penetration testing
offline/                   – Offline payment mechanics
cross-border/              – mBridge, Icebreaker integration
operations/                – Runbooks, monitoring, disaster recovery
testing/                   – QA strategy, test plans
deployment/                – Infrastructure, CI/CD, rollout
training/                  – Developer onboarding, architecture primers
stakeholder-comms/         – Executive summaries, parliamentary briefs
```

## 🚀 Getting Started

### For Executives/Decision-Makers
1. Read [Executive Summary](#executive-summary) (this page)
2. Review [2-page brief](./docs/stakeholder-comms/EXECUTIVE-SUMMARY.md)
3. Schedule demo of architecture

### For Engineers
1. Start: [Architecture Overview](./docs/ARCHITECTURE.md)
2. Read: [Technology Stack](./docs/TECHNOLOGY-STACK.md)
3. Clone: [Rust settlement engine example](./docs/technical/rust/settlement-pseudocode.rs)
4. Build: [Local development setup](./docs/training/SETUP-INSTRUCTIONS.md)

### For Regulators/Compliance
1. Read: [Privacy Design](./docs/PRIVACY-DESIGN.md)
2. Review: [Threat Model](./docs/THREAT-MODEL.md)
3. Check: [AML/CFT Compliance](./docs/compliance/AML-CFT-COMPLIANCE.md)
4. Study: [CBDC Law Draft](./docs/compliance/CBDC-LAW-DRAFT.md)

### For Project Managers
1. Review: [Implementation Roadmap](./docs/implementation/PROJECT-PLAN.md)
2. Check: [Phase 1 Checklist](./docs/implementation/PHASE-1-CHECKLIST.md)
3. Study: [Budget Breakdown](./docs/implementation/BUDGET-BREAKDOWN.md)
4. Plan: [Hiring](./docs/implementation/HIRING-PLAN.md)

## 📖 Full Documentation

- **[BIS Research Paper](./docs/research/BIS-RESEARCH-PAPER.md)** (18,000 words)
- **[Technical Architecture](./docs/ARCHITECTURE.md)**
- **[Privacy Design](./docs/PRIVACY-DESIGN.md)**
- **[Offline Payments](./docs/OFFLINE-PAYMENTS.md)**
- **[Cross-Border Integration](./docs/CROSS-BORDER.md)**
- **[Comparative Analysis](./docs/comparative-analysis/)**
- **[Implementation Roadmap](./docs/implementation/PROJECT-PLAN.md)**

## 🔗 External References

- [BIS Papers on CBDC](https://www.bis.org/publ/work_wps.htm)
- [IMF CBDC Research](https://www.imf.org/)
- [Hyperledger Fabric Documentation](https://hyperledger-fabric.readthedocs.io/)
- [mBridge Initiative](https://www.imf.org/en/News/Articles/2023/08/04/mbridge-central-banks-announce-multi-cbdc-platform)
- [eNaira (Central Bank of Nigeria)](https://www.cbn.gov.ng/)
- [eCedi (Bank of Ghana)](https://www.bog.gov.gh/)

## 📞 Contact

- **Project Lead**: Bank Al-Maghrib Research Division
- **Repository**: [CBDC-Architecture-Prototype-Bank-Al-Maghrib-](https://github.com/aladin01011/CBDC-Architecture-Prototype-Bank-Al-Maghrib-)
- **Questions**: Open GitHub Issues

## 📜 License

**Confidential – Bank Al-Maghrib**

This repository and all related research is confidential intellectual property of Bank Al-Maghrib. Unauthorized distribution is prohibited.

---

**Last Updated**: May 31, 2025
**Version**: 2.0
