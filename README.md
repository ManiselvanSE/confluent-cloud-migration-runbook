# Confluent Cloud Migration: Single-AZ to Multi-AZ
## Complete Migration Guide Using Cluster Linking

A comprehensive guide for migrating from **Single-Zone Confluent Cloud Dedicated** to **Multi-Zone Enterprise** clusters using Cluster Linking, with near-zero downtime and zero data loss.

---

## 📚 Repository Contents

This repository contains **two complementary documents** designed for different audiences:

### 📘 **Technical Runbook** - For Implementation Teams
**File**: [`confluent-migration-runbook.md`](confluent-migration-runbook.md)

**Purpose**: Detailed, command-level execution guide for hands-on migration

**Target Audience**:
- 👨‍🔧 DevOps Engineers
- 🔧 Site Reliability Engineers (SREs)
- 👨‍💻 Platform Engineers
- 🚨 Implementation Consultants

**Structure**: Chronological by migration phase (10 sections)

**What It Contains**:
- Step-by-step CLI commands (Confluent CLI, Kafka CLI)
- Complete configuration files
- Validation scripts after each step
- Troubleshooting procedures with root causes
- Rollback scripts and emergency procedures
- Command reference appendices

**Use When**: Executing the migration hands-on, operational reference

---

### 📗 **Enterprise Guide** - For Planning & Architecture
**File**: [`ENTERPRISE-GUIDE.md`](ENTERPRISE-GUIDE.md)

**Purpose**: Simplified, role-based explanation for business and technical stakeholders

**Target Audience**:
- 👨‍💼 Executives & Decision-Makers
- 👨‍💻 Solution Engineers (Pre-Sales)
- 🏗️ Solution Architects
- 📊 Product Managers

**Structure**: Organized by role/perspective (7 sections)

**What It Contains**:
- Executive summary and business case
- Solution engineer view (customer demos, objection handling)
- Solution architect view (design considerations, networking)
- Implementation consultant view (high-level execution approach)
- Data flow explanations
- Troubleshooting guide (common issues)
- Best practices

**Use When**: Planning, demos, architecture design, executive briefings

---

## 🎯 Quick Selection Guide

**Which document should I read?**

```
┌─────────────────────────────────────────────────────────────┐
│           CHOOSE YOUR DOCUMENT BY ROLE                       │
└─────────────────────────────────────────────────────────────┘

IF YOU ARE...                          THEN START WITH...
────────────────────────────────────────────────────────────────
👨‍💼 Executive / Decision-Maker         ➜ Enterprise Guide (Section 1)
👨‍💻 Solution Engineer (Sales/SE)       ➜ Enterprise Guide (Section 2)
🏗️ Solution Architect                  ➜ Enterprise Guide (Section 3)
👨‍🔧 Implementation Consultant          ➜ Enterprise Guide (Section 4) + Technical Runbook
🔧 DevOps / SRE (Execution)            ➜ Technical Runbook (all sections)
🚨 Incident Responder (Rollback)       ➜ Technical Runbook (Section 8)

PLANNING PHASE                         ➜ Enterprise Guide
CUSTOMER DEMO                          ➜ Enterprise Guide (Sections 1-2)
ARCHITECTURE DESIGN                    ➜ Enterprise Guide (Section 3)
HANDS-ON EXECUTION                     ➜ Technical Runbook
TROUBLESHOOTING                        ➜ Technical Runbook (Appendix B)
```

---

## 📋 Document Comparison

| Aspect | 📗 Enterprise Guide | 📘 Technical Runbook |
|--------|---------------------|----------------------|
| **Purpose** | Simplified conceptual guide | Detailed execution playbook |
| **Focus** | WHY and WHAT (business value, architecture) | HOW (step-by-step commands) |
| **Audience** | Executives, Architects, Sales | DevOps, SREs, Implementation |
| **Structure** | By role (7 sections) | By phase (10 sections) |
| **Detail Level** | High-level, business-focused | Command-level, technical |
| **Length** | ~3600 lines (digestible sections) | ~2500 lines (comprehensive) |
| **Commands** | Conceptual examples | Complete CLI commands |
| **Configs** | Explained at high level | Full configuration files |
| **Use Case** | Planning, demos, architecture | Execution, operations |

---

## Overview

This migration guide provides detailed, step-by-step guidance for executing a near-zero downtime migration with no data loss, preserving topic configurations, consumer offsets, and ordering guarantees.

## Key Features

- **Migration Strategy**: Cluster Linking (Active-Passive pattern)
- **Target RPO**: 0 (no data loss)
- **Target RTO**: < 5 minutes
- **Complete CLI Examples**: Real Confluent CLI and Apache Kafka commands throughout
- **Production-Ready**: Validation scripts, monitoring setup, rollback procedures

## 📋 What's Included

### 📘 Technical Runbook - [View Document](confluent-migration-runbook.md)

**10 comprehensive sections** for hands-on implementation:

1. **Pre-Migration Assessment** - Inventory, capacity planning, risk assessment
2. **Architecture Design** - Multi-zone considerations, replication factor, latency impact
3. **Target Cluster Setup** - Provisioning, networking, security
4. **Cluster Linking Implementation** - Step-by-step with CLI examples
5. **Application Migration** - Producer/consumer cutover, Kafka Connect, ksqlDB
6. **Cutover Strategy** - Minute-by-minute execution plan
7. **Validation & Testing** - Data integrity, failover testing, performance benchmarks
8. **Rollback Plan** - Clear triggers and recovery procedures
9. **Post-Migration** - Optimization, monitoring, cost management
10. **Common Pitfalls & Best Practices** - Lessons learned and troubleshooting

---

### 📗 Enterprise Guide - [View Document](ENTERPRISE-GUIDE.md)

**7 role-based sections** for planning and architecture:

1. **Executive Summary** - Business drivers, ROI, target outcomes
2. **Solution Engineer View** - Customer demos, objection handling, talking points
3. **Solution Architect View** - Architecture design, networking, security, cost modeling
4. **Implementation Consultant View** - High-level execution approach, checklists
5. **Function Flow Explanation** - Data flow, replication mechanics, offset handling
6. **Troubleshooting Guide** - Common failures and resolutions
7. **Best Practices** - Zero-downtime tips, monitoring, optimization

---

## Use Cases

- Improving availability from single-zone to multi-zone HA
- Upgrading from Dedicated to Enterprise cluster tier
- Migrating to higher replication factor (RF=1/2 → RF=3)
- Implementing zone-level fault tolerance
- Meeting compliance requirements for data durability

## Prerequisites

- Confluent Cloud account with Enterprise tier access
- Confluent CLI (`confluent`) installed
- Apache Kafka CLI tools installed
- GitHub CLI (`gh`) for API operations (optional)
- Network connectivity (VPC Peering or PrivateLink)

## Key Constraints Addressed

✅ Near-zero downtime (< 5 minute consumer downtime)  
✅ No data loss (RPO = 0)  
✅ Consumer offset preservation via automatic translation  
✅ ACL and RBAC replication  
✅ Schema Registry compatibility  
✅ Kafka Connect and ksqlDB migration strategies  

## Migration Timeline

**Typical timeline for 100TB cluster, 500 topics:**
- Assessment & Planning: 2 weeks
- Staging Migration: 1 week
- Cluster Provisioning: 3 days
- Replication Sync: 5-7 days
- Production Cutover: 4 hours
- Monitoring Period: 2 weeks
- Total: ~10 weeks

## Cost Impact

Expected cost increase: **30-40%** due to:
- Higher replication factor (RF=3 vs RF=1/2)
- Cross-zone data transfer
- Enterprise tier features

## Security

⚠️ **Important**: All credentials in the runbook are **placeholder values** (e.g., `<api-key>`, `<secret>`). Replace with your actual credentials when executing commands.

Never commit actual credentials to version control.

## Contributing

This runbook is based on Confluent Cloud best practices as of April 2026. If you find issues or have improvements:

1. Open an issue describing the problem
2. Submit a pull request with proposed changes
3. Include references to official Confluent documentation where applicable

## References

- [Confluent Cluster Linking Documentation](https://docs.confluent.io/cloud/current/multi-cloud/cluster-linking/index.html)
- [Confluent Cloud Multi-Zone Architecture](https://docs.confluent.io/cloud/current/clusters/cluster-types.html)
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)

## License

MIT License - Feel free to use and adapt for your organization's needs.

## Disclaimer

This runbook is provided as-is for educational and planning purposes. Always test migration procedures in a staging environment before executing in production. Confluent Cloud features and pricing are subject to change.

---

**Version**: 1.0  
**Last Updated**: April 2026  
**Maintained By**: Platform Engineering Community
