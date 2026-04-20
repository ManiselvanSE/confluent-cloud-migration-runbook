# Confluent Cloud Migration: Single-AZ to Multi-AZ
## Enterprise Cluster Migration with Cluster Linking

[![Confluent Cloud](https://img.shields.io/badge/Confluent-Cloud-0D1620?logo=apache-kafka)](https://confluent.cloud)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)
[![Migration Strategy](https://img.shields.io/badge/Strategy-Cluster%20Linking-green)](docs/overview/migration-strategy.md)
[![Downtime](https://img.shields.io/badge/Downtime-%3C5%20min-success)](docs/guides/migration-runbook.md)

> **Transform your Kafka platform from single-zone vulnerability to multi-zone resilience with near-zero downtime and zero data loss.**

---

## 📋 Table of Contents

- [Overview](#overview)
- [Business Problem](#business-problem)
- [Solution](#solution)
- [Architecture](#architecture)
- [Key Features](#key-features)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Migration Summary](#migration-summary)
- [Repository Structure](#repository-structure)
- [Documentation](#documentation)
- [Getting Help](#getting-help)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

This repository provides **enterprise-grade guidance, automation, and reference architecture** for migrating Confluent Cloud Kafka clusters from:

**From**: Single-Availability-Zone Dedicated Cluster  
**To**: Multi-Availability-Zone Enterprise Cluster

**Migration Approach**: Confluent Cluster Linking (Active-Passive replication)

### Why This Matters

Single-zone Kafka deployments create a **single point of failure**. When an availability zone fails (power outage, network partition, cloud provider incident), your entire event streaming platform becomes unavailable. This migration eliminates that risk with **automatic cross-zone failover** and **zero data loss guarantees**.

---

## Business Problem

### Current State: Single-AZ Risk

```
┌─────────────────────────────────────┐
│   Single Availability Zone          │
│   ┌──────┐  ┌──────┐  ┌──────┐     │
│   │ B-1  │  │ B-2  │  │ B-3  │     │
│   └──────┘  └──────┘  └──────┘     │
│                                      │
│   ⚠️  Zone Failure = Complete Outage │
└─────────────────────────────────────┘
```

**Business Impact**:

| Risk Factor | Impact |
|-------------|--------|
| **Availability** | 99.5% - 99.9% uptime (4+ hours downtime/year) |
| **Zone Failure** | Complete service outage (hours to recover) |
| **Data Loss** | Possible (RF=1 or RF=2 limited durability) |
| **Compliance** | May not meet SOC 2, HIPAA, PCI-DSS requirements |
| **Revenue Risk** | Lost transactions during outages (e-commerce, payments) |
| **Customer Experience** | Application failures, degraded service |

### Real-World Scenarios

**Example 1: E-Commerce Platform**
- Single-zone Kafka cluster processes 50,000 orders/hour
- us-east-1a zone failure (3 hours) = 150,000 lost orders
- Revenue impact: $2M+ (assumes $13 average order value)

**Example 2: Financial Services**
- Payment processing platform (real-time fraud detection)
- Zone failure = cannot process payments, detect fraud
- Regulatory penalty + customer churn

**Example 3: IoT Telemetry**
- Manufacturing plant sensor data (10K sensors, 100 events/sec)
- Zone failure = blind to equipment failures, quality issues
- Operational risk + safety concerns

---

## Solution

### Target State: Multi-AZ Resilience

```
┌──────────────────────────────────────────────────────┐
│          Multi-AZ Enterprise Cluster                  │
│                                                        │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐          │
│  │ Zone A  │    │ Zone B  │    │ Zone C  │          │
│  │  B-1,4  │◄──►│  B-2,5  │◄──►│  B-3,6  │          │
│  └─────────┘    └─────────────┘    └─────────┘          │
│                                                        │
│  ✅ Zone Failure = Zero Downtime (auto-failover)      │
└──────────────────────────────────────────────────────┘
```

**Business Outcomes**:

| Improvement | Single-AZ | Multi-AZ | Benefit |
|-------------|-----------|----------|---------|
| **Uptime SLA** | 99.5% | **99.99%** | 52 min vs. 4 hours downtime/year |
| **Zone Failure Impact** | Complete outage | **Zero downtime** | Automatic failover <30 seconds |
| **Data Durability** | RF=1-2 (at risk) | **RF=3** | Zero data loss guarantee |
| **Recovery Time (RTO)** | Hours (manual) | **<30 seconds** | Automatic |
| **Recovery Point (RPO)** | Possible data loss | **Zero** | min.insync.replicas=2 |
| **Compliance** | Limited | **SOC 2, HIPAA, PCI-DSS ready** | Multi-failure domain |

### Migration Approach: Cluster Linking

**Why Cluster Linking**:

✅ **Near-Zero Downtime**: <5 minute consumer interruption (producers: zero downtime)  
✅ **Zero Data Loss**: Automatic offset translation, byte-identical replication  
✅ **Reversible**: Full rollback capability in <15 minutes  
✅ **Proven**: Confluent's enterprise replication technology (thousands of migrations)  
✅ **Automated**: Consumer offset sync, ACL replication, topic config mirroring  

---

## Architecture

### Migration Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    MIGRATION TIMELINE                        │
└─────────────────────────────────────────────────────────────┘

Week 1-2: PLANNING
- Inventory source cluster (topics, consumers, connectors)
- Design target architecture
- Network setup (VPC peering / PrivateLink)

Week 3: STAGING REHEARSAL
- Execute full migration in staging environment
- Validate approach, timing, rollback

Week 4: CLUSTER PROVISIONING
- Provision multi-AZ Enterprise cluster
- Configure networking, security, API keys

Week 4-5: REPLICATION SYNC
┌──────────┐    Cluster Link     ┌──────────┐
│ Source   │─────────────────────►│  Dest    │
│(Active)  │   (Lag: 10M → 0)    │(Standby) │
└──────────┘                      └──────────┘

Week 6: CUTOVER (4-hour window)
┌──────────┐                      ┌──────────┐
│ Source   │                      │  Dest    │
│(Draining)│                      │(Active)  │
└──────────┘                      └──────────┘
                                      ▲
                              Producers/Consumers
                              (Switched traffic)

Week 6-8: MONITORING & VALIDATION
- 24/7 monitoring
- Performance tuning
- Validation checklists

Week 10: DECOMMISSION
- Delete Cluster Link
- Delete source cluster
- Update documentation
```

### Architecture Diagrams

**Detailed architecture diagrams available**:
- [Single-AZ Current State](architecture/diagrams/single-az-architecture.png)
- [Multi-AZ Target State](architecture/diagrams/multi-az-architecture.png)
- [Cluster Linking Data Flow](architecture/diagrams/cluster-linking.png)
- [Network Topology](architecture/diagrams/network-topology.png)

---

## Key Features

### ✨ Migration Capabilities

- ✅ **Cluster Linking**: Confluent's enterprise replication (automatic offset translation)
- ✅ **Zero Data Loss**: min.insync.replicas=2, RF=3 cross-zone replication
- ✅ **Near-Zero Downtime**: <5 min consumer interruption, zero producer downtime
- ✅ **Offset Preservation**: Automatic consumer offset translation (no reprocessing)
- ✅ **ACL Sync**: Automatic ACL/RBAC replication
- ✅ **Rollback Ready**: <15 min revert to source cluster
- ✅ **Schema Registry**: Shared regional service (no migration needed)

### 📚 Comprehensive Documentation

- ✅ **Role-Based Views**: Solution Engineer, Solution Architect, Implementation Consultant perspectives
- ✅ **Step-by-Step Runbook**: Detailed execution guide with CLI commands
- ✅ **Troubleshooting Guide**: Common failures and resolutions
- ✅ **Architecture Deep-Dives**: Network, security, performance design
- ✅ **FAQ**: Customer common questions answered

### 🛠️ Automation & Scripts

- ✅ **Pre-Migration**: Topic inventory, consumer group export, ACL export
- ✅ **Migration**: Cluster Link creation, mirror topic automation, lag monitoring
- ✅ **Post-Migration**: Data integrity validation, message count comparison
- ✅ **Rollback**: Automated producer/consumer revert scripts

### 💻 Demo Applications

- ✅ **Java Producer/Consumer**: Working examples with Confluent Kafka client
- ✅ **Python Producer/Consumer**: Alternative language examples
- ✅ **Sample Schemas**: Avro, Protobuf, JSON Schema examples
- ✅ **Kubernetes Manifests**: Deployment configs for containerized apps

### 📊 Validation & Testing

- ✅ **Integration Tests**: Cluster connectivity, Schema Registry, end-to-end
- ✅ **Performance Tests**: Producer/consumer throughput benchmarking
- ✅ **Data Integrity**: Message count validation, offset comparison

---

## Prerequisites

### Confluent Cloud Requirements

- **Source Cluster**: Confluent Cloud Dedicated (Single-AZ)
- **Destination Cluster**: Confluent Cloud Enterprise (Multi-AZ)
- **Kafka Version**: 2.8+ (source and destination compatible)
- **Cluster Linking**: Available on Enterprise tier

### Tools & Access

```bash
# Required CLI tools
confluent --version      # Confluent CLI (v3.0+)
kafka-topics --version   # Apache Kafka CLI tools
kubectl version          # Kubernetes (if using K8s deployments)

# Required permissions
# - Confluent Cloud admin access (create clusters, API keys)
# - Cloud provider access (AWS/Azure/GCP for networking)
# - GitHub access (clone this repository)
```

### Network Requirements

- **VPC Peering** or **PrivateLink**: Private connectivity between application VPC and Confluent Cloud
- **Security Groups**: Egress allowed to port 9092 (Kafka), 443 (Schema Registry)
- **DNS**: Resolution for Confluent Cloud bootstrap servers

### Knowledge Prerequisites

- Kafka fundamentals (topics, partitions, offsets, consumer groups)
- Confluent Cloud console navigation
- Basic shell scripting (Bash)
- (Optional) Kubernetes basics (if deploying containerized apps)

---

## Quick Start

### 1. Clone Repository

```bash
git clone https://github.com/YourOrg/confluent-migration-singleaz-to-multiaz.git
cd confluent-migration-singleaz-to-multiaz
```

### 2. Read Documentation (15-Minute Overview)

```bash
# Executive summary
cat docs/guides/quickstart.md

# Role-specific view (choose one)
cat docs/role-based-view.md
# - Section 1: Solution Engineer View (pre-sales)
# - Section 2: Solution Architect View (design)
# - Section 3: Implementation Consultant View (execution)
```

### 3. Review Architecture

```bash
# Open architecture diagrams
open architecture/diagrams/single-az-architecture.png
open architecture/diagrams/multi-az-architecture.png
open architecture/diagrams/migration-flow.png
```

### 4. Explore Automation Scripts

```bash
# List available scripts
ls -la scripts/

# Pre-migration inventory
scripts/pre-migration/inventory-topics.sh --help

# Migration execution
scripts/migration/create-cluster-link.sh --help

# Post-migration validation
scripts/post-migration/validate-data-integrity.sh --help
```

### 5. Run Demo Application (Optional)

```bash
# Java producer example
cd examples/producer/java-producer
mvn clean package
java -jar target/producer.jar \
  --bootstrap-server <your-cluster>:9092 \
  --api-key <your-api-key> \
  --api-secret <your-api-secret> \
  --topic demo-topic

# See examples/README.md for detailed instructions
```

---

## Migration Summary

### Timeline

**Total Duration**: ~10 weeks (for 100TB cluster, 500 topics)

| Phase | Duration | Activities |
|-------|----------|------------|
| **Planning** | 2 weeks | Inventory, architecture design, network setup |
| **Staging Rehearsal** | 1 week | Full migration in non-prod environment |
| **Provisioning** | 3 days | Create multi-AZ cluster, networking, API keys |
| **Replication Sync** | 5-7 days | Cluster Link replication (wait for lag → 0) |
| **Application Testing** | 2 days | Test producers/consumers against destination |
| **Production Cutover** | 4 hours | Switch traffic (actual execution ~30 min) |
| **Monitoring** | 2 weeks | 24/7 monitoring, validation, tuning |
| **Decommission** | 1 day | Delete Cluster Link, delete source cluster |

### Downtime Impact

| Component | Downtime | Notes |
|-----------|----------|-------|
| **Producers** | 0 minutes | Rolling restart with new config |
| **Consumers** | 3-5 minutes | Graceful stop on source + start on destination |
| **Kafka Connect** | 2-5 minutes | Pause → reconfigure → resume |
| **Overall RTO** | <5 minutes | Consumer unavailability window |

### Cost Impact

**Expected Increase**: 30-40%

| Factor | Impact |
|--------|--------|
| **Replication Factor** | RF=1 → RF=3 (3x storage) |
| **Cross-Zone Bandwidth** | Data transfer costs |
| **Enterprise Tier** | Higher CKU pricing |

**Mitigation**: Right-sizing, tiered storage, compression (see [best practices](docs/reference/best-practices.md))

---

## Repository Structure

```
confluent-migration-singleaz-to-multiaz/
│
├── README.md                     # ← You are here
├── LICENSE                       # Apache 2.0 license
├── CHANGELOG.md                  # Version history
│
├── docs/                         # Documentation
│   ├── guides/
│   │   ├── quickstart.md         # 5-min executive summary
│   │   ├── migration-runbook.md  # Full step-by-step guide
│   │   ├── troubleshooting.md    # Common issues & fixes
│   │   └── faq.md                # Frequently asked questions
│   │
│   ├── architecture/
│   │   ├── current-state.md      # Single-AZ architecture
│   │   ├── target-state.md       # Multi-AZ architecture
│   │   └── comparison.md         # Single vs Multi-AZ table
│   │
│   ├── role-based-view.md        # SE/SA/Implementation perspectives
│   │
│   └── reference/
│       ├── glossary.md           # Technical terms
│       └── best-practices.md     # Migration best practices
│
├── architecture/                 # Architecture diagrams
│   ├── diagrams/                 # PNG/SVG images
│   │   ├── single-az-architecture.png
│   │   ├── multi-az-architecture.png
│   │   ├── migration-flow.png
│   │   └── cluster-linking.png
│   │
│   └── draw.io/                  # Editable sources
│       └── architecture.drawio
│
├── configs/                      # Sample configurations
│   ├── kafka/
│   │   ├── broker.properties.sample
│   │   ├── topic-configs.yaml
│   │   └── cluster-link.properties.sample
│   │
│   └── kubernetes/
│       ├── producer-deployment.yaml
│       └── consumer-deployment.yaml
│
├── scripts/                      # Automation scripts
│   ├── pre-migration/
│   │   ├── inventory-topics.sh
│   │   ├── inventory-consumer-groups.sh
│   │   └── export-acls.sh
│   │
│   ├── migration/
│   │   ├── create-cluster-link.sh
│   │   ├── create-mirror-topics.sh
│   │   └── monitor-replication-lag.sh
│   │
│   ├── post-migration/
│   │   ├── validate-data-integrity.sh
│   │   └── compare-message-counts.sh
│   │
│   └── rollback/
│       ├── rollback-producers.sh
│       └── rollback-consumers.sh
│
├── examples/                     # Demo applications
│   ├── producer/
│   │   └── java-producer/        # Java producer app
│   │
│   ├── consumer/
│   │   └── java-consumer/        # Java consumer app
│   │
│   └── schemas/
│       ├── order.avsc            # Sample Avro schema
│       └── payment.avsc
│
├── tests/                        # Validation tests
│   ├── integration/
│   │   └── test-end-to-end.sh
│   │
│   └── performance/
│       └── producer-throughput-test.sh
│
└── templates/                    # Reusable templates
    ├── cutover-checklist.md
    └── rollback-plan.md
```

---

## Documentation

### Getting Started

| Document | Audience | Purpose |
|----------|----------|---------|
| [Quick Start Guide](docs/guides/quickstart.md) | Executives, Decision-Makers | 5-min overview, business case |
| [Role-Based View](docs/role-based-view.md) | All Roles | Tailored perspectives (SE/SA/Implementation) |
| [Migration Runbook](docs/guides/migration-runbook.md) | Implementation Teams | Step-by-step execution guide |
| [FAQ](docs/guides/faq.md) | All Audiences | Common questions answered |

### Architecture

| Document | Audience | Purpose |
|----------|----------|---------|
| [Architecture Overview](docs/architecture/architecture-overview.md) | Solution Architects | High-level design |
| [Target State](docs/architecture/target-state.md) | Engineers | Multi-AZ cluster details |
| [Comparison Table](docs/architecture/comparison.md) | All | Single vs Multi-AZ feature matrix |

### Troubleshooting & Reference

| Document | Audience | Purpose |
|----------|----------|---------|
| [Troubleshooting Guide](docs/guides/troubleshooting.md) | Implementation Teams | Common failures & resolutions |
| [Best Practices](docs/reference/best-practices.md) | All | Migration tips & optimization |
| [Glossary](docs/reference/glossary.md) | All | Technical term definitions |

---

## Getting Help

### Support Channels

- **GitHub Issues**: [Report bugs or request features](https://github.com/YourOrg/confluent-migration-singleaz-to-multiaz/issues)
- **Confluent Documentation**: [Official Cluster Linking docs](https://docs.confluent.io/cloud/current/multi-cloud/cluster-linking/index.html)
- **Confluent Support**: [Contact support](https://support.confluent.io) (Enterprise customers)

### FAQs

**Q: How long does migration take?**  
A: Typical timeline is 10 weeks (2 weeks planning, 1 week staging, 5-7 days replication sync, 4-hour cutover, 2 weeks monitoring). Actual timeline varies with cluster size.

**Q: What's the actual downtime?**  
A: Producer downtime: 0 min (rolling restart). Consumer downtime: 3-5 min (restart with destination config). Kafka Connect: 2-5 min (pause/resume).

**Q: Can we rollback if issues arise?**  
A: Yes. Cluster Link remains active for 7-14 days post-cutover. Rollback can be executed in <15 minutes (see [rollback plan](templates/rollback-plan.md)).

**Q: Will consumers reprocess messages?**  
A: No. Cluster Link automatically translates consumer offsets. Consumers resume from exact offset on destination cluster (zero reprocessing).

**Q: What about Kafka Connect connectors?**  
A: Connectors require reconfiguration (update bootstrap server, API keys). Connector offsets are NOT automatically migrated; some reprocessing may occur depending on connector type.

**Q: How much will costs increase?**  
A: Typical increase: 30-40% (RF=3 storage, cross-zone bandwidth, Enterprise tier premium). See [cost analysis](docs/overview/business-case.md#cost-impact).

**More FAQs**: [docs/guides/faq.md](docs/guides/faq.md)

---

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

**Ways to contribute**:
- 🐛 Report bugs or issues
- 📚 Improve documentation
- 💡 Suggest new features or scripts
- 🎨 Enhance architecture diagrams
- ✅ Share customer success stories (with permission)

---

## License

This project is licensed under the **Apache License 2.0** - see the [LICENSE](LICENSE) file for details.

---

## Acknowledgments

- **Confluent Engineering**: For Cluster Linking technology
- **Customer Success Teams**: For migration best practices
- **Community Contributors**: For continuous improvement

---

## Next Steps

### For Solution Engineers
1. Review [Business Case](docs/overview/business-case.md)
2. Read [Solution Engineer View](docs/role-based-view.md#1-solution-engineer-view)
3. Prepare customer demo using [Demo Script](presentations/demo-script.md)

### For Solution Architects
1. Study [Target Architecture](docs/architecture/target-state.md)
2. Review [Network Design](docs/architecture/architecture-overview.md#networking)
3. Read [Solution Architect View](docs/role-based-view.md#2-solution-architect-view)

### For Implementation Teams
1. Execute [Quick Start](#quick-start) above
2. Follow [Migration Runbook](docs/guides/migration-runbook.md)
3. Read [Implementation Consultant View](docs/role-based-view.md#3-implementation-consultant-view)
4. Test scripts in staging environment

---

**Questions?** Open an [issue](https://github.com/YourOrg/confluent-migration-singleaz-to-multiaz/issues) or contact your Confluent representative.

**Ready to migrate?** Start with the [Quick Start Guide](docs/guides/quickstart.md) ⬆️
