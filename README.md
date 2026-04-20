# Confluent Cloud Migration Runbook

A comprehensive, production-grade technical runbook for migrating from a **Single-Zone Confluent Cloud Dedicated** cluster to a **Multi-Zone Confluent Cloud Enterprise** cluster using **Cluster Linking**.

## Overview

This runbook provides detailed, step-by-step guidance for executing a near-zero downtime migration with no data loss, preserving topic configurations, consumer offsets, and ordering guarantees.

## Key Features

- **Migration Strategy**: Cluster Linking (Active-Passive pattern)
- **Target RPO**: 0 (no data loss)
- **Target RTO**: < 5 minutes
- **Complete CLI Examples**: Real Confluent CLI and Apache Kafka commands throughout
- **Production-Ready**: Validation scripts, monitoring setup, rollback procedures

## Contents

### 📋 [View Full Runbook](confluent-migration-runbook.md)

The runbook includes:

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
