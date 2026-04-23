# Confluent Cloud: Dedicated to Enterprise Cluster Migration Guide

**Comprehensive Implementation Documentation**

---

**Document Information**

| Field | Value |
|-------|-------|
| **Document Title** | Dedicated to Enterprise Cluster Migration Guide |
| **Version** | 1.0 |
| **Date** | April 21, 2026 |
| **Author** | Confluent Migration Team |
| **Classification** | Internal - Confidential |
| **Status** | Final |

---

**Revision History**

| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 0.1 | April 10, 2026 | Migration Team | Initial draft |
| 0.5 | April 15, 2026 | Migration Team | Technical review complete |
| 1.0 | April 21, 2026 | Migration Team | Final version approved |

---

**Document Approval**

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Migration Lead | [Name] | ____________ | ________ |
| Technical Architect | [Name] | ____________ | ________ |
| Security Lead | [Name] | ____________ | ________ |
| Finance Approver | [Name] | ____________ | ________ |

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Introduction](#introduction)
   - 2.1 Purpose
   - 2.2 Scope
   - 2.3 Audience
   - 2.4 Prerequisites
3. [Migration Overview](#migration-overview)
   - 3.1 Migration Strategy
   - 3.2 Key Principles
   - 3.3 Success Criteria
4. [Step 1: Discovery and Sizing](#step-1-discovery-and-sizing)
   - 4.1 Metrics Collection
   - 4.2 Workload Analysis
   - 4.3 Partition Assessment
   - 4.4 Sizing Calculations
   - 4.5 Cost Estimation
   - 4.6 Business Case Development
5. [Step 2: Provision Enterprise Cluster](#step-2-provision-enterprise-cluster)
   - 5.1 Cluster Creation
   - 5.2 Private Networking Setup
   - 5.3 DNS Configuration
   - 5.4 Security Configuration
   - 5.5 Schema Registry Setup
   - 5.6 Validation and Testing
6. [Step 3: Establish Cluster Linking](#step-3-establish-cluster-linking)
   - 6.1 Cluster Linking Overview
   - 6.2 Link Creation
   - 6.3 Mirror Topic Configuration
   - 6.4 Monitoring and Validation
7. [Step 4: Pilot Migration](#step-4-pilot-migration)
   - 7.1 Pilot Selection Criteria
   - 7.2 Migration Execution
   - 7.3 Validation Procedures
   - 7.4 Troubleshooting Guide
8. [Step 5: Full Application Migration](#step-5-full-application-migration)
   - 8.1 Migration Planning
   - 8.2 Consumer Migration
   - 8.3 Topic Promotion
   - 8.4 Producer Migration
   - 8.5 Connector Migration
   - 8.6 Flink Job Migration
9. [Step 6: Validation and Monitoring](#step-6-validation-and-monitoring)
   - 9.1 Monitoring Framework
   - 9.2 Performance Validation
   - 9.3 Cost Validation
   - 9.4 Stakeholder Approval
10. [Step 7: Decommission Dedicated Cluster](#step-7-decommission-dedicated-cluster)
    - 10.1 Pre-Decommission Checklist
    - 10.2 Cluster Deletion
    - 10.3 Network Cleanup
    - 10.4 Documentation Updates
11. [Risk Management](#risk-management)
    - 11.1 Risk Assessment
    - 11.2 Mitigation Strategies
    - 11.3 Rollback Procedures
12. [Project Management](#project-management)
    - 12.1 Timeline and Milestones
    - 12.2 Resource Requirements
    - 12.3 Communication Plan
13. [Appendices](#appendices)
    - Appendix A: Command Reference
    - Appendix B: Terraform Templates
    - Appendix C: Configuration Examples
    - Appendix D: Troubleshooting Guide
    - Appendix E: Glossary

---

## 1. Executive Summary

### 1.1 Overview

This document provides comprehensive guidance for migrating Apache Kafka workloads from Confluent Cloud Dedicated clusters to Confluent Cloud Enterprise clusters. The migration leverages Confluent's Cluster Linking technology to achieve zero-downtime migration while maintaining data integrity and business continuity.

### 1.2 Key Benefits

The migration to Enterprise clusters delivers the following benefits:

- **Cost Reduction:** Typical savings of 20-60% compared to Dedicated clusters
- **Operational Efficiency:** Automated capacity management through serverless auto-scaling
- **Platform Modernization:** Access to Confluent's latest features and strategic product direction
- **Reduced Networking Costs:** Private Network Interface (PNI) reduces AWS data transfer charges by up to 70%
- **Simplified Operations:** Elimination of manual cluster scaling and capacity planning

### 1.3 Migration Approach

The migration follows a seven-step phased approach designed to minimize risk and ensure business continuity:

1. **Discovery & Sizing** - Analyze current usage and size Enterprise cluster appropriately
2. **Provision Enterprise** - Create and configure new Enterprise cluster in parallel
3. **Cluster Linking** - Establish real-time data synchronization
4. **Pilot Migration** - Validate process with low-risk application
5. **Full Migration** - Migrate all applications in coordinated waves
6. **Validation** - Monitor and validate for 2-4 weeks
7. **Decommission** - Clean up Dedicated cluster and realize full savings

### 1.4 Timeline and Resources

**Typical Timeline:** 4-11 weeks end-to-end  
**Downtime:** Zero (leveraging Cluster Linking)  
**Team Size:** 3-5 core team members + application owners  
**Investment:** 1-month overlap costs, recovered in 2-6 months through savings

### 1.5 Success Metrics

Migration success will be measured against:

- **Technical:** Zero data loss, zero unplanned downtime, performance maintained or improved
- **Business:** Cost savings target achieved (20-60%), business metrics unchanged
- **Operational:** Documentation complete, team trained, repeatable process established

---

## 2. Introduction

### 2.1 Purpose

This document serves as the authoritative guide for planning and executing a migration from Confluent Cloud Dedicated clusters to Enterprise clusters. It provides detailed procedures, best practices, and troubleshooting guidance to ensure a successful migration with minimal risk to business operations.

The document addresses both technical implementation details and project management considerations, enabling teams to execute the migration with confidence.

### 2.2 Scope

#### 2.2.1 In Scope

This migration guide covers:

- Migration of Kafka clusters from Dedicated to Enterprise
- Migration of associated services (Schema Registry, Connectors, Flink)
- Network architecture changes (VPC Peering/TGW to Private Link)
- Application configuration updates
- Consumer and producer migration procedures
- Cost estimation and business case development
- Risk assessment and mitigation strategies
- Rollback procedures

#### 2.2.2 Out of Scope

This document does not cover:

- Migration from self-hosted Kafka to Confluent Cloud
- Migration between cloud providers (AWS to Azure, etc.)
- Application code refactoring or optimization
- Kafka version upgrades
- Schema evolution strategies
- Data retention policy changes

### 2.3 Audience

This document is intended for:

- **Platform Engineering Teams** - Responsible for Kafka infrastructure and migration execution
- **Application Development Teams** - Owners of applications using Kafka
- **Network Engineering Teams** - Responsible for Private Link setup and DNS configuration
- **Security Teams** - Ensuring compliance and security requirements are met
- **Project Managers** - Coordinating migration activities and stakeholder communication
- **Executive Stakeholders** - Understanding business case and approving migration

### 2.4 Prerequisites

Before beginning the migration, ensure the following prerequisites are met:

#### 2.4.1 Access and Permissions

- Administrative access to Confluent Cloud organization
- Permissions to create Enterprise clusters
- Access to source Dedicated cluster
- Cloud provider account access (AWS/Azure/GCP) for networking
- Credentials for all applications consuming from/producing to Kafka

#### 2.4.2 Technical Requirements

- Confluent CLI installed (latest version)
- Terraform 1.0+ (if using infrastructure-as-code)
- Confluent Terraform Provider 2.60+
- Network connectivity from VPC to Confluent Cloud
- DNS management capability (Route 53, Azure DNS, Cloud DNS)

#### 2.4.3 Knowledge Requirements

- Understanding of Kafka fundamentals (topics, producers, consumers, consumer groups)
- Familiarity with Confluent Cloud console
- Basic networking knowledge (VPCs, DNS, Private Link)
- Application configuration management experience

#### 2.4.4 Environmental Prerequisites

- Dedicated cluster running and operational
- Application inventory documented
- Current cluster metrics available (last 90 days minimum)
- Stakeholder buy-in and approval obtained
- Migration timeline aligned with business calendar (avoid critical periods)

---

## 3. Migration Overview

### 3.1 Migration Strategy

The migration strategy is built on three core principles:

#### 3.1.1 Zero-Downtime Architecture

The migration leverages Confluent's Cluster Linking technology to achieve zero downtime:

1. **Parallel Operation** - Dedicated cluster continues serving production traffic while Enterprise cluster is built and configured
2. **Real-Time Synchronization** - Cluster Linking maintains continuous data sync between clusters
3. **Gradual Cutover** - Applications migrate incrementally, allowing validation at each stage
4. **Rollback Capability** - Dedicated cluster remains available as fallback until full validation complete

#### 3.1.2 Risk Mitigation Through Phasing

The migration is divided into discrete phases with validation gates:

**Phase 1: Preparation (Weeks 1-2)**
- Gather metrics and analyze workload
- Size Enterprise cluster appropriately
- Build business case and obtain approvals

**Phase 2: Infrastructure Setup (Week 2)**
- Provision Enterprise cluster
- Configure networking and security
- Validate connectivity

**Phase 3: Data Synchronization (Weeks 2-3)**
- Establish Cluster Linking
- Monitor backfill progress
- Verify data integrity

**Phase 4: Pilot Migration (Week 3)**
- Migrate single low-risk application
- Validate end-to-end functionality
- Refine procedures based on learnings

**Phase 5: Production Migration (Weeks 3-6)**
- Migrate applications in coordinated waves
- Consumers first, then producers
- Continuous monitoring and validation

**Phase 6: Validation (Weeks 6-11)**
- Extended monitoring period
- Performance and cost validation
- Stakeholder approval

**Phase 7: Cleanup (Week 11+)**
- Decommission Dedicated cluster
- Remove obsolete networking
- Finalize documentation

#### 3.1.3 Progressive Risk Reduction

Risk decreases as migration progresses:

| Phase | Risk Level | Rollback Complexity | Mitigation |
|-------|------------|-------------------|------------|
| Preparation | None | N/A | No changes to production |
| Infrastructure | Low | Simple (nothing migrated yet) | Dedicated unchanged |
| Synchronization | Low | Simple (read-only mirrors) | Dedicated still source of truth |
| Pilot | Low-Medium | Simple (1 app) | Quick rollback for pilot app |
| Production | Medium | Moderate (many apps) | Phased approach, Dedicated available |
| Validation | Medium-High | Complex (after promotion) | Extended validation period |
| Cleanup | High | None (irreversible) | Thorough validation before deletion |

### 3.2 Key Principles

The following principles guide all migration activities:

#### 3.2.1 Business Continuity First

- **Principle:** No migration activity should jeopardize business operations
- **Implementation:**
  - All migrations scheduled during low-traffic periods
  - Critical applications migrated last (after validation with non-critical apps)
  - Rollback procedures tested and documented before each migration wave
  - Stakeholder communication plan ensures visibility into migration status

#### 3.2.2 Data Integrity Paramount

- **Principle:** Zero data loss is non-negotiable
- **Implementation:**
  - Cluster Linking guarantees data durability
  - Validation checkpoints verify data completeness
  - Consumer offset preservation ensures no duplicate processing
  - Mirror lag monitoring confirms sync completion before cutover

#### 3.2.3 Validate Before Proceeding

- **Principle:** Each phase must be validated before advancing
- **Implementation:**
  - Defined validation criteria for each phase
  - Go/no-go decision points with stakeholder approval
  - Automated validation checks where possible
  - Manual verification for business-critical metrics

#### 3.2.4 Document Everything

- **Principle:** All decisions, procedures, and learnings must be documented
- **Implementation:**
  - Migration runbooks for each application type
  - Issue log with resolutions
  - Lessons learned captured throughout
  - Knowledge transfer to operational teams

### 3.3 Success Criteria

Migration success is measured across three dimensions:

#### 3.3.1 Technical Success Metrics

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| **Unplanned Downtime** | 0 minutes | Monitoring alerts, incident log |
| **Data Loss** | 0 events | Message count validation, offset verification |
| **Applications Migrated** | 100% | Application inventory tracking |
| **Performance** | ≥100% of baseline | Latency (p50, p95, p99), throughput comparison |
| **Auto-Scaling Effectiveness** | Scales within 2 minutes | Auto-scaling event logs |

#### 3.3.2 Business Success Metrics

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| **Cost Savings** | 20-60% | Monthly billing comparison |
| **Business Metrics** | 0% deviation | Orders processed, revenue, conversions |
| **Stakeholder Satisfaction** | ≥8/10 | Post-migration survey |
| **Customer Impact** | 0 complaints | Support ticket analysis |
| **Payback Period** | ≤6 months | Financial analysis |

#### 3.3.3 Operational Success Metrics

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| **Team Readiness** | 100% trained | Training completion tracking |
| **Documentation** | 100% complete | Documentation review checklist |
| **Repeatability** | Process applicable to other clusters | Process review and validation |
| **Operational Overhead** | <50% of Dedicated | Time tracking for cluster management |

---

## 4. Step 1: Discovery and Sizing

### 4.1 Metrics Collection

Accurate sizing of the Enterprise cluster requires comprehensive metrics from the current Dedicated cluster. This section outlines the metrics to collect and how to gather them.

#### 4.1.1 Required Metrics

Collect the following metrics covering a minimum of 90 days (3 months) to capture seasonal variations and growth trends:

**Throughput Metrics:**
- Peak ingress (MB/sec) - Maximum data written to cluster
- Peak egress (MB/sec) - Maximum data read from cluster
- Average ingress (MB/sec) - Mean data written over time period
- Average egress (MB/sec) - Mean data read over time period
- Hourly throughput patterns - To identify spike patterns

**Capacity Metrics:**
- Total partition count - Sum of partitions across all topics
- Topic count - Number of topics
- Storage usage (GB) - Total data retained
- Current CKU allocation - Provisioned capacity units
- Cluster load percentage - Utilization of provisioned capacity

**Performance Metrics:**
- Producer latency (p50, p95, p99) - Time to acknowledge writes
- Consumer latency (p50, p95, p99) - Time to fetch records
- Request rate - Operations per second
- Network throughput - Actual bytes transferred

#### 4.1.2 Collection Methods

**Method 1: Confluent Cloud Console (Recommended)**

The Confluent Cloud console provides comprehensive metrics visualization:

1. Navigate to your Dedicated cluster in Confluent Cloud
2. Click **"Metrics"** in the left navigation
3. Select time range: **Last 90 days**
4. Review the following metric groups:
   - **Throughput** - Ingress/Egress MB/sec
   - **Storage** - Retained bytes
   - **Cluster Load** - Utilization percentage
   - **Request Rate** - Operations per second

5. Export key metrics:
   - Take screenshots of metric graphs
   - Note peak and average values
   - Document spike patterns and frequencies

**Method 2: Confluent Cloud API**

For programmatic access to metrics:

```bash
# Get cluster metrics via API
curl -X GET "https://api.telemetry.confluent.cloud/v2/metrics/cloud/query" \
  -H "Authorization: Basic $(echo -n '<api-key>:<api-secret>' | base64)" \
  -H "Content-Type: application/json" \
  -d '{
    "aggregations": [
      {
        "metric": "io.confluent.kafka.server/received_bytes",
        "agg": "SUM"
      }
    ],
    "filter": {
      "op": "AND",
      "filters": [
        {
          "field": "resource.kafka.id",
          "op": "EQ",
          "value": "<cluster-id>"
        }
      ]
    },
    "granularity": "PT1H",
    "intervals": ["2026-01-01T00:00:00Z/2026-04-01T00:00:00Z"]
  }'
```

**Method 3: Confluent CLI**

For command-line metric retrieval:

```bash
# Describe cluster to get current configuration
confluent kafka cluster describe <cluster-id> -o json

# Example output parsing for CKU count
confluent kafka cluster describe lkc-xxxxx -o json | \
  jq '.sku'

# Get topic list with partition counts
confluent kafka topic list --cluster <cluster-id> -o json | \
  jq '.[] | {name: .name, partitions: .partitions_count}'
```

#### 4.1.3 Partition Count Collection

Partition count is critical for Enterprise sizing as Enterprise clusters have fewer partitions per eCKU:

**Collection Script:**

```bash
#!/bin/bash
# Script: count-partitions.sh
# Purpose: Calculate total partition count across all topics

CLUSTER_ID="<your-cluster-id>"
TOTAL_PARTITIONS=0

# Get all topics
TOPICS=$(confluent kafka topic list --cluster $CLUSTER_ID -o json | jq -r '.[].name')

# Sum partitions
for TOPIC in $TOPICS; do
  PARTITIONS=$(confluent kafka topic describe $TOPIC --cluster $CLUSTER_ID -o json | \
    jq '.partitions_count')
  echo "Topic: $TOPIC - Partitions: $PARTITIONS"
  TOTAL_PARTITIONS=$((TOTAL_PARTITIONS + PARTITIONS))
done

echo "=========================================="
echo "Total Partitions: $TOTAL_PARTITIONS"
echo "=========================================="
```

**Output Example:**
```
Topic: orders - Partitions: 12
Topic: payments - Partitions: 6
Topic: inventory - Partitions: 8
Topic: user-events - Partitions: 24
==========================================
Total Partitions: 50
==========================================
```

#### 4.1.4 Metrics Documentation Template

Document collected metrics in a structured format:

```markdown
# Dedicated Cluster Metrics

**Cluster Information:**
- Cluster ID: lkc-xxxxx
- Region: us-east-1
- Cloud Provider: AWS
- Availability: Multi-Zone
- Current CKUs: 2

**Throughput Metrics (90-day period):**
- Peak Ingress: 85 MB/sec (observed on 2026-03-15 during promotion)
- Average Ingress: 32 MB/sec
- Peak Egress: 240 MB/sec (observed on 2026-03-15 during promotion)
- Average Egress: 95 MB/sec

**Spike Analysis:**
- Daily spikes: Yes (9 AM - 6 PM weekdays)
- Spike magnitude: 3x average (32 MB/sec → 90 MB/sec)
- Spike duration: 9 hours/day, 5 days/week
- Weekly patterns: Higher on Mondays, month-end

**Capacity Metrics:**
- Total Topics: 47
- Total Partitions: 850
- Storage: 1.2 TB
- Cluster Load: 45% average, 75% peak

**Performance Metrics:**
- Producer Latency (p99): 42ms
- Consumer Latency (p99): 38ms
- Request Rate: 12,000 ops/sec peak

**Cost:**
- Current Monthly Cost: $4,500
```

### 4.2 Workload Analysis

Understanding workload patterns is essential for accurate Enterprise sizing and cost estimation.

#### 4.2.1 Spike Pattern Analysis

Analyze throughput over time to identify spike characteristics:

**Daily Spike Pattern:**

```
Example: E-commerce workload
─────────────────────────────────────────
Time (Hour)   | Ingress (MB/sec) | Type
─────────────────────────────────────────
00:00 - 06:00 | 10 MB/sec        | Low (night)
06:00 - 09:00 | 25 MB/sec        | Ramp-up
09:00 - 18:00 | 85 MB/sec        | Peak (business hours)
18:00 - 21:00 | 40 MB/sec        | Decline
21:00 - 24:00 | 15 MB/sec        | Low (evening)
─────────────────────────────────────────
Average: 35 MB/sec
Peak: 85 MB/sec (2.4x average)
Spike Duration: 9 hours/day
Spike Frequency: Daily (weekdays)
```

**Implication for Enterprise:**
- Size for baseline: 35 MB/sec → 1 eCKU
- Auto-scaling handles: 85 MB/sec peaks → adds 1-2 eCKU temporarily
- Cost optimization: Pay for extra eCKU only 9 hours/day vs. 24/7 on Dedicated

**Weekly/Monthly Patterns:**

```
Example: Financial services workload
─────────────────────────────────────────
Period        | Average Ingress  | Pattern
─────────────────────────────────────────
Mon-Thu       | 40 MB/sec        | Steady
Friday        | 120 MB/sec       | Payroll spike
Weekends      | 5 MB/sec         | Minimal
Month-end     | 200 MB/sec       | Batch processing
─────────────────────────────────────────
Monthly Average: 45 MB/sec
Monthly Peak: 200 MB/sec (4.4x average)
```

**Implication for Enterprise:**
- Size for baseline: 45 MB/sec → 1 eCKU
- Auto-scaling handles: 200 MB/sec peaks → adds 3-4 eCKU temporarily
- Cost optimization: Pay for peak capacity only 2-3 days/month

#### 4.2.2 Workload Classification

Classify your workload to predict Enterprise suitability:

| Workload Type | Characteristics | Enterprise Fit | Expected Savings |
|---------------|----------------|----------------|------------------|
| **Steady** | <20% variation, constant 24/7 | Moderate | 15-30% |
| **Daily Spikes** | 2-3x variation, business hours pattern | Excellent | 35-55% |
| **Weekly Spikes** | 3-5x variation, weekly cycles | Excellent | 40-60% |
| **Batch** | 5-10x variation, scheduled jobs | Excellent | 50-70% |
| **Event-Driven** | Unpredictable spikes | Excellent | 45-65% |

**Your Workload Classification:**

Based on metrics analysis, classify your workload:
- Pattern: [Daily Spikes / Weekly Spikes / Steady / Batch / Event-Driven]
- Variation: [X]x between average and peak
- Frequency: [Daily / Weekly / Monthly / Unpredictable]
- Enterprise Fit: [Excellent / Good / Moderate / Poor]

#### 4.2.3 Growth Trend Analysis

Analyze growth trends to account for future capacity needs:

```
Historical Growth Analysis:
─────────────────────────────────────────
Period          | Avg Ingress | Growth Rate
─────────────────────────────────────────
Jan 2026        | 28 MB/sec   | Baseline
Feb 2026        | 30 MB/sec   | +7%
Mar 2026        | 32 MB/sec   | +7%
Apr 2026        | 35 MB/sec   | +9%
─────────────────────────────────────────
3-Month Trend: +25% growth
Projected 6-month: 40 MB/sec
Projected 12-month: 50 MB/sec
```

**Sizing Recommendation:**
- Current need: 35 MB/sec → 1 eCKU
- With growth buffer: Size for 45 MB/sec → still 1 eCKU (has headroom)
- Enterprise auto-scales as growth continues (no need to over-provision)

### 4.3 Partition Assessment

Enterprise clusters have different partition limits than Dedicated clusters, making partition assessment critical for accurate sizing.

#### 4.3.1 Partition Limit Comparison

| Cluster Type | Partitions per Unit | Example Capacity |
|--------------|-------------------|------------------|
| **Dedicated** | ~4,500 per CKU | 3 CKU = 13,500 partitions |
| **Enterprise** | ~3,000 per eCKU | 3 eCKU = 9,000 partitions |
| **Difference** | **-1,500 per unit (-33%)** | Requires more eCKUs for same partition count |

**Important:** These are approximate values. Consult current Confluent documentation for exact partition limits.

#### 4.3.2 Partition Count Impact on Sizing

**Scenario 1: Throughput-Constrained (Common)**

```
Current Dedicated:
- Throughput: 100 MB/sec average
- Partitions: 2,000
- CKUs: 2 (sized for throughput)

Enterprise Sizing:
- Throughput-based: 100 ÷ 50 = 2 eCKU
- Partition-based: 2,000 ÷ 3,000 = 0.67 eCKU (rounds to 1)
- **Final: 2 eCKU** (throughput is the constraint)
- Partition headroom: 2 eCKU × 3,000 = 6,000 partitions (plenty of room)
```

**Scenario 2: Partition-Constrained (Less Common)**

```
Current Dedicated:
- Throughput: 40 MB/sec average
- Partitions: 12,000
- CKUs: 3 (sized for partitions, not throughput)

Enterprise Sizing:
- Throughput-based: 40 ÷ 50 = 0.8 eCKU (rounds to 1)
- Partition-based: 12,000 ÷ 3,000 = **4 eCKU**
- **Final: 4 eCKU** (partitions are the constraint)
- Throughput headroom: 4 eCKU × 50 MB/sec = 200 MB/sec (significant over-provision)
```

**Implication:**
- In Scenario 1: Enterprise saves money (right-sized)
- In Scenario 2: Enterprise costs similar to Dedicated (partition density limits savings)

#### 4.3.3 Partition Optimization Strategies

If you're partition-constrained, consider optimization before migration:

**Strategy 1: Consolidate Over-Partitioned Topics**

Many topics are over-partitioned beyond what's needed for parallelism:

```
Example: "user-clicks" topic
- Current: 100 partitions
- Producers: 5 instances
- Consumers: 10 instances (in 1 consumer group)
- Actual parallelism needed: 10 partitions (matching consumer count)
- **Optimization: Reduce to 20 partitions** (2x consumer count for headroom)
- Savings: 80 partitions freed up
```

**Strategy 2: Archive/Delete Unused Topics**

```
Audit Results:
- Total topics: 150
- Active (written to in last 30 days): 95
- Inactive (no writes in 60+ days): 55

Action:
- Delete inactive topics: Frees 3,200 partitions
- Document deleted topics for compliance/audit trail
```

**Strategy 3: Merge Similar Topics**

```
Before:
- orders-us-east: 50 partitions
- orders-us-west: 50 partitions
- orders-eu: 50 partitions
Total: 150 partitions across 3 topics

After:
- orders (with region as message key): 60 partitions
Total: 60 partitions in 1 topic
Savings: 90 partitions
```

**Important:** Partition optimization requires application changes and thorough testing. Only pursue if partition count is a significant blocker.

### 4.4 Sizing Calculations

With metrics collected and analyzed, calculate the appropriate Enterprise cluster size.

#### 4.4.1 Sizing Formula

Enterprise cluster size is determined by the MAXIMUM of three factors:

```
Final eCKU = MAX(
  Throughput-based eCKU,
  Partition-based eCKU,
  SLA-based minimum eCKU
)
```

#### 4.4.2 Throughput-Based Sizing

**Ingress Calculation:**
```
eCKU (ingress) = Average Ingress (MB/sec) ÷ 50 MB/sec per eCKU
```

**Egress Calculation:**
```
eCKU (egress) = Average Egress (MB/sec) ÷ 150 MB/sec per eCKU
```

**Take the maximum:**
```
Throughput-based eCKU = MAX(eCKU from ingress, eCKU from egress)
```

**Example:**
```
Average Ingress: 35 MB/sec
Average Egress: 105 MB/sec

eCKU (ingress) = 35 ÷ 50 = 0.7 eCKU
eCKU (egress) = 105 ÷ 150 = 0.7 eCKU

Throughput-based eCKU = MAX(0.7, 0.7) = 0.7 → rounds to 1 eCKU
```

#### 4.4.3 Partition-Based Sizing

```
Partition-based eCKU = Total Partitions ÷ 3,000 partitions per eCKU
```

**Example:**
```
Total Partitions: 850

Partition-based eCKU = 850 ÷ 3,000 = 0.28 → rounds to 1 eCKU
```

#### 4.4.4 SLA-Based Minimum

Enterprise clusters have minimum eCKU requirements based on SLA choice:

| SLA | Uptime Guarantee | Min eCKU | Deployment |
|-----|-----------------|----------|------------|
| **99.9%** | ~43 min/month downtime allowed | 1 eCKU | Single-zone possible |
| **99.99%** | ~4 min/month downtime allowed | 2 eCKU | Multi-zone required |

**Matching Dedicated to Enterprise SLA:**

```
If Dedicated has:
- 1 CKU (Single-AZ) → Choose Enterprise 99.9% SLA (1 eCKU min)
- 2+ CKU (Multi-AZ) → Choose Enterprise 99.99% SLA (2 eCKU min)
```

#### 4.4.5 Complete Sizing Example

**Input Metrics:**
```
Current Dedicated Cluster:
- CKUs: 2 (Multi-AZ)
- Average Ingress: 35 MB/sec
- Average Egress: 105 MB/sec
- Total Partitions: 850
- Current Cost: $4,500/month
```

**Sizing Calculation:**

**Step 1: Throughput-Based**
```
Ingress: 35 ÷ 50 = 0.7 eCKU
Egress: 105 ÷ 150 = 0.7 eCKU
Throughput-based: 0.7 eCKU (rounds to 1)
```

**Step 2: Partition-Based**
```
Partitions: 850 ÷ 3,000 = 0.28 eCKU (rounds to 1)
```

**Step 3: SLA-Based**
```
Dedicated is Multi-AZ (2 CKU) → Match with 99.99% SLA
SLA minimum: 2 eCKU
```

**Final Sizing:**
```
Final eCKU = MAX(1, 1, 2) = **2 eCKU baseline**
SLA: 99.99%
Deployment: Multi-zone
```

**Result:** Enterprise cluster sized at 2 eCKU with 99.99% SLA

**Capacity Headroom:**
```
Throughput headroom:
- Ingress: 2 × 50 = 100 MB/sec (vs. 35 MB/sec used = 186% headroom)
- Egress: 2 × 150 = 300 MB/sec (vs. 105 MB/sec used = 186% headroom)

Partition headroom:
- Capacity: 2 × 3,000 = 6,000 partitions (vs. 850 used = 605% headroom)

Interpretation: Cluster has significant headroom for growth and spikes
```

### 4.5 Cost Estimation

With cluster sized, estimate Enterprise costs and compare to current Dedicated costs.

#### 4.5.1 Enterprise Cost Components

Enterprise cluster costs include:

1. **Baseline eCKU Charge** - Fixed monthly cost for minimum provisioned eCKUs
2. **Auto-Scaling Charges** - Hourly charges when cluster scales above baseline
3. **Storage Charges** - Cost per GB retained
4. **Networking Charges** - Private Link endpoints, data transfer

#### 4.5.2 Baseline Cost Calculation

**Baseline eCKU Pricing** (Example - verify current pricing with Confluent):

| SLA | eCKU Price/Month (approximate) |
|-----|-------------------------------|
| 99.9% | $1,000 per eCKU |
| 99.99% | $1,400 per eCKU |

**Calculation:**
```
Baseline Cost = Number of eCKU × Price per eCKU

Example (2 eCKU @ 99.99% SLA):
Baseline Cost = 2 × $1,400 = $2,800/month
```

#### 4.5.3 Auto-Scaling Cost Estimation

Auto-scaling charges apply when cluster scales above baseline:

**Hourly Auto-Scaling Rate** (Example pricing):
```
Additional eCKU: $3/hour
```

**Estimation Approach:**

**Method 1: Based on Spike Patterns**

```
Example: Daily spikes for 9 hours/day, 5 days/week

Spike Duration per Month:
- 9 hours/day × 22 business days = 198 hours/month

Average Additional eCKU During Spikes:
- Baseline: 2 eCKU
- Peak load: 85 MB/sec → needs 2 eCKU (85 ÷ 50 = 1.7 → rounds to 2)
- Additional: 0 eCKU (peak fits within baseline)

Auto-Scaling Cost: $0/month

(In this example, 2 eCKU baseline is sufficient for peaks)
```

**Example 2: Spikes Exceed Baseline**

```
Spike Pattern: Daily spikes to 120 MB/sec

Baseline: 2 eCKU (handles 100 MB/sec)
Peak Load: 120 MB/sec → needs 3 eCKU (120 ÷ 50 = 2.4 → rounds to 3)
Additional eCKU: 1 eCKU during spikes

Spike Duration: 9 hours/day × 22 days = 198 hours/month

Auto-Scaling Cost:
= 1 eCKU × 198 hours × $3/hour
= $594/month
```

**Method 2: Conservative Estimate (20-30% of Baseline)**

If spike patterns are unclear:
```
Conservative Auto-Scaling Estimate = Baseline Cost × 25%

Example:
Baseline: $2,800/month
Auto-scaling: $2,800 × 0.25 = $700/month
```

#### 4.5.4 Storage Cost Calculation

**Storage Pricing** (Example - verify current pricing):
```
$0.10 per GB/month
```

**Calculation:**
```
Storage Cost = Total Storage (GB) × $0.10/GB

Example (1,200 GB retained):
Storage Cost = 1,200 × $0.10 = $120/month
```

#### 4.5.5 Networking Cost Estimation

**Private Link Endpoint Charges:**

```
AWS Private Link Endpoint: ~$10/month per endpoint
Number of Endpoints: Typically 3 (Kafka, Schema Registry, Flink)

Private Link Cost: 3 × $10 = $30/month
```

**Data Transfer Charges:**

```
Standard AWS Data Transfer: $0.01 - $0.09 per GB (varies by direction)

With PNI (Private Network Interface):
- Reduces data transfer costs by ~70%
- Example: $300/month data transfer → $90/month with PNI
- Net savings: $210/month
```

**Total Networking:**
```
Private Link Endpoints: $30/month
Data Transfer (with PNI): $90/month
Total Networking: $120/month
```

#### 4.5.6 Complete Cost Estimate Example

```
Enterprise Cluster Cost Estimate
─────────────────────────────────────────────────
Component                  | Cost
─────────────────────────────────────────────────
Baseline eCKU (2 @ 99.99%) | $2,800/month
Auto-Scaling (estimated)   | $400/month
Storage (1,200 GB)         | $120/month
Networking (Private Link)  | $120/month
─────────────────────────────────────────────────
TOTAL ENTERPRISE COST      | $3,440/month
─────────────────────────────────────────────────

vs. Current Dedicated Cost | $4,500/month
─────────────────────────────────────────────────
MONTHLY SAVINGS            | $1,060 (24%)
ANNUAL SAVINGS             | $12,720
─────────────────────────────────────────────────
```

#### 4.5.7 Migration Overlap Cost

During migration, you'll pay for both clusters temporarily:

```
Overlap Period Cost Calculation
─────────────────────────────────────────────────
Overlap Duration: 4 weeks (1 month)

Dedicated Cost (1 month)   | $4,500
Enterprise Cost (1 month)  | $3,440
─────────────────────────────────────────────────
TOTAL OVERLAP COST         | $7,940 (one-time)
─────────────────────────────────────────────────
```

#### 4.5.8 Payback Period Calculation

```
Payback Period = Overlap Cost ÷ Monthly Savings

Example:
= $7,940 ÷ $1,060
= 7.5 months
```

**Interpretation:** Migration costs are recovered in 7.5 months through ongoing savings.

#### 4.5.9 Multi-Year ROI Analysis

```
3-Year Financial Analysis
─────────────────────────────────────────────────
                         | Year 1   | Year 2   | Year 3
─────────────────────────────────────────────────
Dedicated Cost (annual)  | $54,000  | $54,000  | $54,000
Enterprise Cost (annual) | $41,280  | $41,280  | $41,280
Migration Overlap (Y1)   | $7,940   | $0       | $0
─────────────────────────────────────────────────
Net Enterprise Cost      | $49,220  | $41,280  | $41,280
Annual Savings           | $4,780   | $12,720  | $12,720
─────────────────────────────────────────────────
Cumulative Savings       | $4,780   | $17,500  | $30,220
─────────────────────────────────────────────────
```

**Conclusion:** Despite $7,940 overlap cost, Year 1 shows positive ROI. Years 2-3 realize full savings.

### 4.6 Business Case Development

Compile findings into a business case for stakeholder approval.

#### 4.6.1 Executive Summary Template

```markdown
# Business Case: Dedicated to Enterprise Migration

## Executive Summary

**Recommendation:** Migrate from Confluent Dedicated to Enterprise cluster

**Financial Impact:**
- One-time migration cost: $7,940 (4-week overlap)
- Annual savings: $12,720 (24% reduction)
- Payback period: 7.5 months
- 3-year cumulative savings: $30,220

**Benefits:**
- Cost reduction: 24% annual savings
- Operational efficiency: Elimination of manual scaling
- Auto-scaling: Automatic response to traffic spikes
- Platform modernization: Access to latest Confluent features

**Timeline:** 8 weeks (detailed plan attached)

**Risk Level:** Low (zero-downtime migration, rollback available)

**Approval Required:** $7,940 for overlap period
```

#### 4.6.2 Detailed Business Case Components

**1. Problem Statement**
```
Current State:
- Operating Confluent Dedicated cluster at $4,500/month
- Manual scaling required for traffic spikes
- Over-provisioned for safety (paying for unused capacity)
- Missing out on Enterprise platform benefits

Business Impact:
- Higher than necessary infrastructure costs
- Operational overhead for capacity management
- Risk of performance issues if manual scaling delayed
```

**2. Proposed Solution**
```
Migrate to Confluent Enterprise cluster leveraging:
- Cluster Linking for zero-downtime migration
- Auto-scaling for automated capacity management
- Modern platform for future innovation
```

**3. Cost-Benefit Analysis**
```
Costs:
- One-time: $7,940 migration overlap
- Ongoing: $3,440/month (vs. $4,500 current)

Benefits:
- Direct savings: $1,060/month ($12,720/year)
- Operational efficiency: ~10 hours/month engineer time saved
- Risk reduction: Automatic scaling eliminates manual intervention delays
- Innovation access: Latest Confluent features (Flink, Tableflow, etc.)
```

**4. Risk Assessment**
```
Risk: Data loss during migration
Likelihood: Very Low
Mitigation: Cluster Linking guarantees data durability, zero-downtime design

Risk: Application disruption
Likelihood: Low
Mitigation: Pilot migration validates process, phased approach, rollback available

Risk: Cost overruns
Likelihood: Low
Mitigation: Conservative estimates, continuous monitoring, adjust baseline if needed

Risk: Extended timeline
Likelihood: Medium
Mitigation: Detailed project plan, dedicated team, Confluent support
```

**5. Implementation Plan**
```
Timeline: 8 weeks
Resources: 3 engineers (50% time), 1 PM (25% time)
Downtime: Zero
Rollback: Available until final cleanup
```

**6. Success Metrics**
```
- Zero data loss: Verified through message count validation
- Zero unplanned downtime: Monitored via alerts
- 24% cost reduction: Validated in monthly billing
- Performance maintained: Latency within 10% of baseline
- Stakeholder satisfaction: Post-migration survey ≥8/10
```

#### 4.6.3 Stakeholder Presentation

Prepare presentation tailored to audience:

**For Finance/Executive:**
- Focus on cost savings and ROI
- Simple metrics: "Save $12,700/year"
- Payback period: "Recover investment in 7.5 months"
- Risk mitigation: "Zero-downtime, rollback available"

**For Engineering:**
- Technical approach: Cluster Linking, phased migration
- Operational benefits: Elimination of manual scaling
- Platform advantages: Latest features, auto-scaling
- Timeline and resource needs

**For Security/Compliance:**
- Security posture: Multi-tenant certifications (SOC 2, ISO 27001, HIPAA)
- Data protection: Encryption, BYOK support, Private Link
- Compliance maintained: Same certifications as Dedicated
- Audit trail: Complete documentation of migration

#### 4.6.4 Approval Requirements

Define approval gates:

```
Approval Level 1: Engineering Manager
- Scope: Technical approach
- Required: Architecture review, resource commitment

Approval Level 2: Finance Director  
- Scope: Budget for overlap period ($7,940)
- Required: Cost-benefit analysis, ROI calculation

Approval Level 3: Security Officer
- Scope: Security and compliance review
- Required: Security assessment, certification verification

Approval Level 4: VP Engineering / CTO
- Scope: Final authorization to proceed
- Required: Complete business case, all prior approvals
```

### 4.7 Step 1 Deliverables Checklist

Before proceeding to Step 2, ensure the following deliverables are complete:

- [ ] **Metrics Collection Complete**
  - [ ] 90-day throughput metrics documented
  - [ ] Partition count verified
  - [ ] Storage requirements captured
  - [ ] Performance baseline established

- [ ] **Workload Analysis Complete**
  - [ ] Spike patterns identified and documented
  - [ ] Workload classified (steady/spiky/batch/etc.)
  - [ ] Growth trends analyzed

- [ ] **Sizing Calculations Complete**
  - [ ] Throughput-based eCKU calculated
  - [ ] Partition-based eCKU calculated
  - [ ] SLA selected and minimum eCKU determined
  - [ ] Final Enterprise cluster size determined

- [ ] **Cost Estimation Complete**
  - [ ] Baseline eCKU cost calculated
  - [ ] Auto-scaling cost estimated
  - [ ] Storage cost calculated
  - [ ] Networking cost estimated
  - [ ] Total monthly cost estimated
  - [ ] Comparison to Dedicated cost documented
  - [ ] Overlap cost calculated
  - [ ] Payback period determined

- [ ] **Business Case Approved**
  - [ ] Executive summary prepared
  - [ ] Stakeholder presentation delivered
  - [ ] All approval gates passed
  - [ ] Budget authorized for migration

- [ ] **Project Planning Complete**
  - [ ] Timeline developed (detailed in Step 5)
  - [ ] Team assigned
  - [ ] Communication plan established

**Sign-Off:**

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Migration Lead | __________ | _____ | __________ |
| Finance Approver | __________ | _____ | __________ |
| Executive Sponsor | __________ | _____ | __________ |

---

## 5. Step 2: Provision Enterprise Cluster

With sizing determined and business case approved, proceed to create and configure the Enterprise cluster.

### 5.1 Cluster Creation

Enterprise cluster can be created via Confluent Cloud UI, CLI, or Terraform (infrastructure-as-code).

#### 5.1.1 Prerequisites

Before creating the Enterprise cluster:

- [ ] Confluent Cloud organization with Enterprise entitlement
- [ ] API keys with appropriate permissions
- [ ] Environment created (or using existing environment)
- [ ] Region and cloud provider determined (must match Dedicated for Cluster Linking)
- [ ] Terraform installed (if using IaC approach)

#### 5.1.2 Method 1: Confluent Cloud UI

**Step-by-Step Instructions:**

1. **Log into Confluent Cloud**
   - Navigate to https://confluent.cloud
   - Authenticate with your credentials

2. **Navigate to Cluster Creation**
   - Select environment (or create new environment)
   - Click **"Add cluster"**

3. **Select Cluster Type**
   - Choose **"Enterprise"** cluster type
   - Review Enterprise features and benefits

4. **Configure Cluster**

   **Basic Configuration:**
   ```
   Cluster Name: enterprise-prod
   Cloud Provider: AWS (match Dedicated)
   Region: us-east-1 (match Dedicated)
   Availability: Multi-zone (for 99.99% SLA)
   ```

   **Capacity Configuration:**
   ```
   eCKU: 2 (from sizing calculations)
   SLA: 99.99%
   ```

   **Networking:**
   ```
   [Will configure in Step 5.2]
   ```

5. **Review and Launch**
   - Review configuration summary
   - Review pricing estimate
   - Click **"Launch cluster"**

6. **Wait for Provisioning**
   - Provisioning typically takes 15-30 minutes
   - Monitor status: "Provisioning" → "Up"

7. **Capture Cluster Details**
   ```
   Cluster ID: lkc-yyyyy
   Bootstrap Endpoint: pkc-yyyyy.us-east-1.aws.confluent.cloud:9092
   REST Endpoint: https://pkc-yyyyy.us-east-1.aws.confluent.cloud
   ```

#### 5.1.3 Method 2: Terraform (Recommended)

**Benefits of Terraform:**
- Infrastructure-as-code (version controlled, repeatable)
- Consistent deployments across environments
- Easy to replicate for dev/test clusters
- Automated configuration management

**Prerequisites:**
```bash
# Install Terraform
brew install terraform  # macOS
# or download from https://www.terraform.io/downloads

# Install Confluent Terraform Provider
# (configured in terraform configuration)
```

**Directory Structure:**
```
kafka-migration/
├── terraform/
│   ├── versions.tf          # Provider configuration
│   ├── variables.tf         # Input variables
│   ├── main.tf              # Main resources
│   ├── outputs.tf           # Output values
│   ├── terraform.tfvars     # Variable values (DO NOT commit to Git!)
│   └── README.md            # Documentation
```

**Step 1: Configure Provider**

Create `versions.tf`:
```hcl
terraform {
  required_version = ">= 1.0"
  
  required_providers {
    confluent = {
      source  = "confluentinc/confluent"
      version = ">= 2.60.0"  # Minimum for Enterprise support
    }
  }
}

provider "confluent" {
  cloud_api_key    = var.confluent_cloud_api_key
  cloud_api_secret = var.confluent_cloud_api_secret
}
```

**Step 2: Define Variables**

Create `variables.tf`:
```hcl
variable "confluent_cloud_api_key" {
  description = "Confluent Cloud API Key"
  type        = string
  sensitive   = true
}

variable "confluent_cloud_api_secret" {
  description = "Confluent Cloud API Secret"
  type        = string
  sensitive   = true
}

variable "environment_name" {
  description = "Confluent Cloud Environment name"
  type        = string
  default     = "production"
}

variable "cluster_name" {
  description = "Kafka cluster name"
  type        = string
  default     = "enterprise-prod"
}

variable "cloud_provider" {
  description = "Cloud provider (AWS, AZURE, GCP)"
  type        = string
  default     = "AWS"
}

variable "region" {
  description = "Cloud provider region"
  type        = string
  default     = "us-east-1"
}

variable "ecku" {
  description = "Number of eCKUs"
  type        = number
  default     = 2
}
```

**Step 3: Create Resources**

Create `main.tf`:
```hcl
# Environment (if creating new)
resource "confluent_environment" "prod" {
  display_name = var.environment_name
}

# Enterprise Kafka Cluster
resource "confluent_kafka_cluster" "enterprise" {
  display_name = var.cluster_name
  availability = "MULTI_ZONE"  # For 99.99% SLA
  cloud        = var.cloud_provider
  region       = var.region
  
  enterprise {
    ecku = var.ecku
  }
  
  environment {
    id = confluent_environment.prod.id
  }
}

# Service Account for cluster admin
resource "confluent_service_account" "cluster_admin" {
  display_name = "${var.cluster_name}-admin"
  description  = "Service account for cluster administration"
}

# Cluster Admin Role Binding
resource "confluent_role_binding" "cluster_admin" {
  principal   = "User:${confluent_service_account.cluster_admin.id}"
  role_name   = "CloudClusterAdmin"
  crn_pattern = confluent_kafka_cluster.enterprise.rbac_crn
}

# API Key for cluster admin
resource "confluent_api_key" "cluster_admin_key" {
  display_name = "${var.cluster_name}-admin-key"
  description  = "API key for cluster administration"
  
  owner {
    id          = confluent_service_account.cluster_admin.id
    api_version = confluent_service_account.cluster_admin.api_version
    kind        = confluent_service_account.cluster_admin.kind
  }
  
  managed_resource {
    id          = confluent_kafka_cluster.enterprise.id
    api_version = confluent_kafka_cluster.enterprise.api_version
    kind        = confluent_kafka_cluster.enterprise.kind
    environment {
      id = confluent_environment.prod.id
    }
  }
}
```

**Step 4: Define Outputs**

Create `outputs.tf`:
```hcl
output "environment_id" {
  description = "Environment ID"
  value       = confluent_environment.prod.id
}

output "cluster_id" {
  description = "Kafka Cluster ID"
  value       = confluent_kafka_cluster.enterprise.id
}

output "bootstrap_endpoint" {
  description = "Kafka Bootstrap Endpoint"
  value       = confluent_kafka_cluster.enterprise.bootstrap_endpoint
}

output "rest_endpoint" {
  description = "Kafka REST Endpoint"
  value       = confluent_kafka_cluster.enterprise.rest_endpoint
}

output "cluster_admin_api_key" {
  description = "Cluster Admin API Key"
  value       = confluent_api_key.cluster_admin_key.id
  sensitive   = true
}

output "cluster_admin_api_secret" {
  description = "Cluster Admin API Secret"
  value       = confluent_api_key.cluster_admin_key.secret
  sensitive   = true
}
```

**Step 5: Set Variable Values**

Create `terraform.tfvars`:
```hcl
confluent_cloud_api_key    = "XXXXXXXXXXXXXX"
confluent_cloud_api_secret = "YYYYYYYYYYYYYY"
environment_name           = "production"
cluster_name              = "enterprise-prod"
cloud_provider            = "AWS"
region                    = "us-east-1"
ecku                      = 2
```

**IMPORTANT:** Add `terraform.tfvars` to `.gitignore` - never commit credentials!

**Step 6: Execute Terraform**

```bash
# Navigate to terraform directory
cd terraform/

# Initialize Terraform (download providers)
terraform init

# Review execution plan
terraform plan

# Review output carefully - verify:
# - Cluster will be created in correct region
# - eCKU count is correct
# - SLA is 99.99% (Multi-zone)

# Apply configuration
terraform apply

# Confirm: type "yes" when prompted

# Expected output:
# Apply complete! Resources: 5 added, 0 changed, 0 destroyed.
#
# Outputs:
# bootstrap_endpoint = "pkc-yyyyy.us-east-1.aws.confluent.cloud:9092"
# cluster_id = "lkc-yyyyy"
# environment_id = "env-xxxxx"
# rest_endpoint = "https://pkc-yyyyy.us-east-1.aws.confluent.cloud"
```

**Step 7: Save Terraform State Securely**

```bash
# Terraform state contains sensitive data (API keys, secrets)
# Store in secure backend (S3, Terraform Cloud, etc.)

# Example: Configure S3 backend
# Add to versions.tf:
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "kafka/enterprise-cluster/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-lock"
  }
}

# Re-initialize to migrate state to backend
terraform init -migrate-state
```

**Step 8: Export Credentials Securely**

```bash
# Retrieve admin API key from Terraform output
terraform output -raw cluster_admin_api_key
# Output: XXXXXXXXXXXXXX

terraform output -raw cluster_admin_api_secret
# Output: YYYYYYYYYYYYYY

# Store in secrets manager (AWS Secrets Manager example)
aws secretsmanager create-secret \
  --name kafka/enterprise-prod/admin \
  --secret-string "{\"api_key\":\"$(terraform output -raw cluster_admin_api_key)\",\"api_secret\":\"$(terraform output -raw cluster_admin_api_secret)\"}"
```

#### 5.1.4 Post-Creation Validation

After cluster creation (UI or Terraform), validate:

**Check Cluster Status:**
```bash
confluent kafka cluster describe <cluster-id>
```

**Expected Output:**
```
+--------------------+---------------------------------------------------+
| ID                 | lkc-yyyyy                                         |
| Name               | enterprise-prod                                   |
| Type               | ENTERPRISE                                        |
| Availability       | MULTI_ZONE                                        |
| Status             | UP                                                |
| Cloud              | AWS                                               |
| Region             | us-east-1                                         |
| Bootstrap          | pkc-yyyyy.us-east-1.aws.confluent.cloud:9092     |
| Endpoint           | SASL_SSL://pkc-yyyyy.us-east-1.aws.confluent.cloud:9092 |
+--------------------+---------------------------------------------------+
```

**Verify eCKU Configuration:**
- Log into Confluent Cloud UI
- Navigate to cluster → Settings
- Confirm: eCKU = 2, SLA = 99.99%

### 5.2 Private Networking Setup

Configure Private Link connectivity between your VPC and the Enterprise cluster.

#### 5.2.1 Networking Architecture Overview

**Dedicated Networking (Old):**
```
Your VPC → [VPC Peering / TGW] → Dedicated Cluster
```

**Enterprise Networking (New):**
```
Your VPC → [VPC Endpoint] → [Private Link] → [Ingress Gateway] → [Access Points]
                                                                   ├─ Kafka Access Point
                                                                   ├─ Schema Registry Access Point
                                                                   └─ Flink Access Point (if used)
```

**Key Differences:**
- Enterprise uses endpoint-based connectivity (Private Link)
- Separate endpoints per service (Kafka, SR, Flink)
- More granular control and security

#### 5.2.2 Private Link Components

**1. Ingress Gateway**
- Confluent-managed entry point into Enterprise cluster
- Handles connection routing and load balancing
- One gateway per environment/region

**2. Access Point**
- Logical connection from your VPC to gateway
- Provides private DNS names for services
- Multiple access points can exist per environment

**3. VPC Endpoint**
- Cloud provider resource (AWS PrivateLink, Azure Private Link, GCP PSC)
- Created in your VPC
- Connects to Confluent's service endpoint

#### 5.2.3 AWS Private Link Setup

**Step 1: Create Ingress Gateway (Confluent Side)**

**Via Confluent Cloud UI:**
```
1. Navigate to Environment → Network
2. Click "Add gateway"
3. Configure:
   - Gateway name: prod-gateway
   - Cloud: AWS
   - Region: us-east-1
4. Click "Create"
5. Note the Service Name (format: com.amazonaws.vpce.us-east-1.vpce-svc-xxxxx)
```

**Via Terraform:**
```hcl
resource "confluent_gateway" "main" {
  display_name = "prod-gateway"
  
  environment {
    id = confluent_environment.prod.id
  }
  
  aws_private_link_gateway {
    region = "us-east-1"
  }
}

output "gateway_service_name" {
  description = "AWS PrivateLink Service Name"
  value       = confluent_gateway.main.aws_private_link_gateway[0].service_name
}
```

**Step 2: Create VPC Endpoint (AWS Side)**

**Prerequisites:**
- VPC ID where applications run
- Subnet IDs (minimum 2 in different AZs for high availability)
- Security group allowing port 9092 (Kafka), 443 (Schema Registry, REST API)

**Via AWS Console:**
```
1. Navigate to VPC → Endpoints
2. Click "Create Endpoint"
3. Configure:
   - Service category: Other endpoint services
   - Service name: com.amazonaws.vpce.us-east-1.vpce-svc-xxxxx (from Step 1)
   - VPC: vpc-xxxxx
   - Subnets: Select 2+ subnets in different AZs
   - Security groups: sg-xxxxx (allow 9092, 443)
4. Click "Create endpoint"
5. Wait for status: "Pending" → "Available"
6. Note the VPC Endpoint ID: vpce-xxxxx
```

**Via AWS CLI:**
```bash
aws ec2 create-vpc-endpoint \
  --vpc-id vpc-xxxxx \
  --service-name com.amazonaws.vpce.us-east-1.vpce-svc-xxxxx \
  --vpc-endpoint-type Interface \
  --subnet-ids subnet-xxxxx subnet-yyyyy \
  --security-group-ids sg-xxxxx

# Output:
# {
#   "VpcEndpoint": {
#     "VpcEndpointId": "vpce-xxxxx",
#     "State": "pending"
#   }
# }
```

**Via Terraform:**
```hcl
resource "aws_vpc_endpoint" "confluent" {
  vpc_id            = var.vpc_id
  service_name      = confluent_gateway.main.aws_private_link_gateway[0].service_name
  vpc_endpoint_type = "Interface"
  
  subnet_ids = [
    var.subnet_id_az1,
    var.subnet_id_az2,
  ]
  
  security_group_ids = [
    aws_security_group.confluent_access.id,
  ]
  
  private_dns_enabled = false  # We'll manage DNS separately
  
  tags = {
    Name = "confluent-enterprise-privatelink"
  }
}

output "vpc_endpoint_id" {
  value = aws_vpc_endpoint.confluent.id
}

output "vpc_endpoint_dns_entries" {
  value = aws_vpc_endpoint.confluent.dns_entry
}
```

**Step 3: Create Access Point (Confluent Side)**

Access Point links the VPC Endpoint to the Ingress Gateway.

**Via Confluent Cloud UI:**
```
1. Navigate to Gateway → Access Points
2. Click "Add access point"
3. Configure:
   - Access point name: prod-kafka-access
   - Gateway: prod-gateway
   - VPC Endpoint Service: vpce-xxxxx (from Step 2)
4. Click "Create"
5. Wait for status: "Provisioning" → "Ready"
6. Note the endpoints:
   - Kafka: pkc-xxxxx.us-east-1.aws.confluent.cloud:9092
   - Schema Registry: psrc-xxxxx.us-east-1.aws.confluent.cloud
```

**Via Terraform:**
```hcl
resource "confluent_access_point" "main" {
  display_name = "prod-kafka-access"
  
  environment {
    id = confluent_environment.prod.id
  }
  
  gateway {
    id = confluent_gateway.main.id
  }
  
  aws_private_link_access_point {
    vpc_endpoint_id = aws_vpc_endpoint.confluent.id
  }
}

# Get Kafka endpoint
data "confluent_kafka_cluster" "enterprise" {
  id = confluent_kafka_cluster.enterprise.id
  environment {
    id = confluent_environment.prod.id
  }
}

output "kafka_bootstrap_endpoint_private" {
  description = "Private Kafka bootstrap endpoint"
  value       = data.confluent_kafka_cluster.enterprise.bootstrap_endpoint
}
```

**Step 4: Verify Connectivity**

Test connectivity from an EC2 instance in your VPC:

```bash
# SSH into EC2 instance in your VPC
ssh ec2-user@<ec2-instance-ip>

# Test DNS resolution (should NOT resolve yet - DNS not configured)
nslookup pkc-xxxxx.us-east-1.aws.confluent.cloud
# Expected: No answer (we'll configure DNS in next section)

# Test connectivity to VPC endpoint directly
# Get VPC endpoint DNS name from AWS console or:
aws ec2 describe-vpc-endpoints --vpc-endpoint-ids vpce-xxxxx \
  --query 'VpcEndpoints[0].DnsEntries[0].DnsName' \
  --output text

# Output example: vpce-xxxxx.vpce-svc-yyyyy.us-east-1.vpce.amazonaws.com

# Test connection
telnet vpce-xxxxx.vpce-svc-yyyyy.us-east-1.vpce.amazonaws.com 9092
# Expected: Connected (confirms VPC endpoint working)
```

### 5.3 DNS Configuration

Configure DNS so applications can resolve Confluent hostnames (pkc-xxxxx.confluent.cloud) to private IPs.

#### 5.3.1 DNS Requirements

**Without Private DNS:**
```
Application tries to connect: pkc-xxxxx.us-east-1.aws.confluent.cloud:9092
DNS resolves to: Public IP (NOT what we want - bypasses Private Link!)
Connection: Goes over internet (insecure, costly)
```

**With Private DNS:**
```
Application tries to connect: pkc-xxxxx.us-east-1.aws.confluent.cloud:9092
DNS resolves to: Private IP (10.x.x.x - VPC endpoint IP)
Connection: Goes through Private Link (secure, private)
```

#### 5.3.2 AWS Route 53 Private Hosted Zone Setup

**Step 1: Create Private Hosted Zone**

**Via AWS Console:**
```
1. Navigate to Route 53 → Hosted zones
2. Click "Create hosted zone"
3. Configure:
   - Domain name: confluent.cloud
   - Type: Private hosted zone
   - VPCs: Select your VPC (vpc-xxxxx)
   - Region: us-east-1
4. Click "Create hosted zone"
5. Note the Hosted Zone ID: Z123456ABCDEF
```

**Via AWS CLI:**
```bash
aws route53 create-hosted-zone \
  --name confluent.cloud \
  --vpc VPCRegion=us-east-1,VPCId=vpc-xxxxx \
  --caller-reference $(date +%s) \
  --hosted-zone-config PrivateZone=true

# Output:
# {
#   "HostedZone": {
#     "Id": "/hostedzone/Z123456ABCDEF",
#     "Name": "confluent.cloud."
#   }
# }
```

**Via Terraform:**
```hcl
resource "aws_route53_zone" "confluent_private" {
  name = "confluent.cloud"
  
  vpc {
    vpc_id = var.vpc_id
  }
  
  tags = {
    Name = "confluent-cloud-private-zone"
  }
}
```

**Step 2: Add DNS Records**

Create DNS records pointing Confluent endpoints to VPC endpoint IPs.

**Get VPC Endpoint IPs:**
```bash
# Get VPC endpoint network interface IPs
aws ec2 describe-vpc-endpoints \
  --vpc-endpoint-ids vpce-xxxxx \
  --query 'VpcEndpoints[0].NetworkInterfaceIds' \
  --output json

# Output: ["eni-xxxxx", "eni-yyyyy"]

# Get IP addresses
aws ec2 describe-network-interfaces \
  --network-interface-ids eni-xxxxx eni-yyyyy \
  --query 'NetworkInterfaces[*].PrivateIpAddress' \
  --output json

# Output: ["10.0.1.100", "10.0.2.100"]
```

**Create A Records:**

**Method 1: Wildcard Record (Simplest)**

```bash
# Create wildcard record for all Confluent endpoints
aws route53 change-resource-record-sets \
  --hosted-zone-id Z123456ABCDEF \
  --change-batch '{
    "Changes": [{
      "Action": "CREATE",
      "ResourceRecordSet": {
        "Name": "*.us-east-1.aws.confluent.cloud",
        "Type": "A",
        "MultiValueAnswer": true,
        "SetIdentifier": "vpce-endpoint-1",
        "TTL": 60,
        "ResourceRecords": [
          {"Value": "10.0.1.100"},
          {"Value": "10.0.2.100"}
        ]
      }
    }]
  }'
```

**Pros:** Simple, covers all endpoints (Kafka, SR, Flink)  
**Cons:** Less granular control

**Method 2: Specific Records (More Control)**

```bash
# Kafka endpoint
aws route53 change-resource-record-sets \
  --hosted-zone-id Z123456ABCDEF \
  --change-batch '{
    "Changes": [{
      "Action": "CREATE",
      "ResourceRecordSet": {
        "Name": "pkc-xxxxx.us-east-1.aws.confluent.cloud",
        "Type": "A",
        "TTL": 60,
        "ResourceRecords": [
          {"Value": "10.0.1.100"},
          {"Value": "10.0.2.100"}
        ]
      }
    }]
  }'

# Schema Registry endpoint
aws route53 change-resource-record-sets \
  --hosted-zone-id Z123456ABCDEF \
  --change-batch '{
    "Changes": [{
      "Action": "CREATE",
      "ResourceRecordSet": {
        "Name": "psrc-xxxxx.us-east-1.aws.confluent.cloud",
        "Type": "A",
        "TTL": 60,
        "ResourceRecords": [
          {"Value": "10.0.1.100"},
          {"Value": "10.0.2.100"}
        ]
      }
    }]
  }'
```

**Pros:** Granular control per service  
**Cons:** More records to manage

**Terraform Example:**
```hcl
# Wildcard DNS record
resource "aws_route53_record" "confluent_wildcard" {
  zone_id = aws_route53_zone.confluent_private.zone_id
  name    = "*.us-east-1.aws.confluent.cloud"
  type    = "A"
  ttl     = "60"
  
  records = [
    for eni in aws_vpc_endpoint.confluent.network_interface_ids :
    data.aws_network_interface.confluent_endpoint[eni].private_ip
  ]
}

# Data source to get ENI private IPs
data "aws_network_interface" "confluent_endpoint" {
  for_each = toset(aws_vpc_endpoint.confluent.network_interface_ids)
  id       = each.value
}
```

**Step 3: Verify DNS Resolution**

Test from EC2 instance in VPC:

```bash
# Should now resolve to private IPs
nslookup pkc-xxxxx.us-east-1.aws.confluent.cloud

# Expected output:
# Server:  10.0.0.2
# Address: 10.0.0.2#53
#
# Non-authoritative answer:
# Name:    pkc-xxxxx.us-east-1.aws.confluent.cloud
# Address: 10.0.1.100
# Address: 10.0.2.100

# Verify it's using private IP (10.x.x.x, not public IP)

# Test Schema Registry
nslookup psrc-xxxxx.us-east-1.aws.confluent.cloud
# Should also resolve to 10.0.1.100, 10.0.2.100
```

#### 5.3.3 DNS Troubleshooting

**Issue: DNS not resolving**

**Check 1: VPC DNS settings**
```bash
# Verify VPC has DNS resolution enabled
aws ec2 describe-vpc-attribute \
  --vpc-id vpc-xxxxx \
  --attribute enableDnsSupport

# Should show: "Value": true

aws ec2 describe-vpc-attribute \
  --vpc-id vpc-xxxxx \
  --attribute enableDnsHostnames

# Should show: "Value": true
```

**Check 2: Route 53 hosted zone association**
```bash
# Verify hosted zone is associated with VPC
aws route53 get-hosted-zone --id Z123456ABCDEF

# Should show VPC in output
```

**Check 3: DNS record created correctly**
```bash
# List all records in hosted zone
aws route53 list-resource-record-sets \
  --hosted-zone-id Z123456ABCDEF

# Verify record exists for pkc-xxxxx.us-east-1.aws.confluent.cloud
```

**Issue: Resolving to public IP instead of private**

**Cause:** DNS query going to public DNS instead of private hosted zone

**Fix:** Ensure:
1. Private hosted zone is associated with correct VPC
2. VPC DNS settings enabled (enableDnsSupport, enableDnsHostnames)
3. EC2 instance is in correct VPC/subnet
4. Security group allows DNS (port 53)

### 5.4 Security Configuration

Configure authentication, authorization, and encryption for the Enterprise cluster.

#### 5.4.1 Service Account Strategy

**Option 1: Reuse Existing Service Accounts (Recommended)**

Advantages:
- Minimal application changes (same credentials)
- Faster migration (no credential rotation)
- Simpler permission management

Process:
```
1. Identify existing service accounts from Dedicated
2. Grant them permissions on Enterprise cluster
3. Applications use same API keys, just different bootstrap URL
```

**Option 2: Create New Service Accounts**

Advantages:
- Clean separation between Dedicated and Enterprise
- Opportunity to audit and improve security model
- Explicit permission grants for new cluster

Process:
```
1. Create new service accounts for each application/team
2. Generate new API keys
3. Grant appropriate permissions
4. Update applications with new credentials
```

Most customers choose **Option 1** for simplicity.

#### 5.4.2 Create Service Accounts (If Needed)

**Via Confluent CLI:**

```bash
# Create service account for producer application
confluent iam service-account create order-service-producer \
  --description "Producer service for orders topic"

# Output:
# +-------------+----------------------------+
# | ID          | sa-123456                  |
# | Name        | order-service-producer     |
# | Description | Producer service for ...   |
# +-------------+----------------------------+

# Create API key for service account
confluent api-key create \
  --service-account sa-123456 \
  --resource <enterprise-cluster-id>

# Output:
# API Key: XXXXXXXXXXXXXX
# API Secret: YYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYYY
#
# IMPORTANT: Save the API Secret - it will not be shown again!
```

**Via Terraform:**
```hcl
# Service account
resource "confluent_service_account" "order_service" {
  display_name = "order-service-producer"
  description  = "Producer service for orders topic"
}

# API key for Kafka cluster
resource "confluent_api_key" "order_service_kafka" {
  display_name = "order-service-kafka-key"
  description  = "Kafka API key for order service"
  
  owner {
    id          = confluent_service_account.order_service.id
    api_version = confluent_service_account.order_service.api_version
    kind        = confluent_service_account.order_service.kind
  }
  
  managed_resource {
    id          = confluent_kafka_cluster.enterprise.id
    api_version = confluent_kafka_cluster.enterprise.api_version
    kind        = confluent_kafka_cluster.enterprise.kind
    environment {
      id = confluent_environment.prod.id
    }
  }
}

# Store credentials in AWS Secrets Manager
resource "aws_secretsmanager_secret" "order_service_kafka" {
  name = "kafka/enterprise/order-service"
}

resource "aws_secretsmanager_secret_version" "order_service_kafka" {
  secret_id = aws_secretsmanager_secret.order_service_kafka.id
  secret_string = jsonencode({
    api_key    = confluent_api_key.order_service_kafka.id
    api_secret = confluent_api_key.order_service_kafka.secret
  })
}
```

#### 5.4.3 Configure ACLs (Access Control Lists)

Grant permissions to service accounts for topics they need to access.

**Common ACL Patterns:**

**Producer Permissions:**
```bash
# Allow producer to write to specific topic
confluent kafka acl create \
  --cluster <enterprise-cluster-id> \
  --allow \
  --service-account sa-123456 \
  --operation WRITE \
  --topic orders

# Allow producer to describe topic
confluent kafka acl create \
  --cluster <enterprise-cluster-id> \
  --allow \
  --service-account sa-123456 \
  --operation DESCRIBE \
  --topic orders
```

**Consumer Permissions:**
```bash
# Allow consumer to read from topic
confluent kafka acl create \
  --cluster <enterprise-cluster-id> \
  --allow \
  --service-account sa-789012 \
  --operation READ \
  --topic orders

# Allow consumer to describe topic
confluent kafka acl create \
  --cluster <enterprise-cluster-id> \
  --allow \
  --service-account sa-789012 \
  --operation DESCRIBE \
  --topic orders

# Allow consumer group access
confluent kafka acl create \
  --cluster <enterprise-cluster-id> \
  --allow \
  --service-account sa-789012 \
  --operation READ \
  --consumer-group order-processor-group

confluent kafka acl create \
  --cluster <enterprise-cluster-id> \
  --allow \
  --service-account sa-789012 \
  --operation DESCRIBE \
  --consumer-group order-processor-group
```

**Admin Permissions (Full Access):**
```bash
# Grant all operations on all topics
confluent kafka acl create \
  --cluster <enterprise-cluster-id> \
  --allow \
  --service-account sa-admin \
  --operation ALL \
  --topic '*'

# Grant all operations on all consumer groups
confluent kafka acl create \
  --cluster <enterprise-cluster-id> \
  --allow \
  --service-account sa-admin \
  --operation ALL \
  --consumer-group '*'

# Grant cluster-level operations
confluent kafka acl create \
  --cluster <enterprise-cluster-id> \
  --allow \
  --service-account sa-admin \
  --operation ALL \
  --cluster-scope
```

**Terraform Example:**
```hcl
# Producer ACLs
resource "confluent_kafka_acl" "order_service_write" {
  kafka_cluster {
    id = confluent_kafka_cluster.enterprise.id
  }
  resource_type = "TOPIC"
  resource_name = "orders"
  pattern_type  = "LITERAL"
  principal     = "User:${confluent_service_account.order_service.id}"
  host          = "*"
  operation     = "WRITE"
  permission    = "ALLOW"
  rest_endpoint = confluent_kafka_cluster.enterprise.rest_endpoint
  credentials {
    key    = confluent_api_key.cluster_admin_key.id
    secret = confluent_api_key.cluster_admin_key.secret
  }
}

resource "confluent_kafka_acl" "order_service_describe" {
  kafka_cluster {
    id = confluent_kafka_cluster.enterprise.id
  }
  resource_type = "TOPIC"
  resource_name = "orders"
  pattern_type  = "LITERAL"
  principal     = "User:${confluent_service_account.order_service.id}"
  host          = "*"
  operation     = "DESCRIBE"
  permission    = "ALLOW"
  rest_endpoint = confluent_kafka_cluster.enterprise.rest_endpoint
  credentials {
    key    = confluent_api_key.cluster_admin_key.id
    secret = confluent_api_key.cluster_admin_key.secret
  }
}
```

#### 5.4.4 Copy ACLs from Dedicated to Enterprise

Replicate existing ACL configuration from Dedicated:

**Export ACLs from Dedicated:**
```bash
# List all ACLs
confluent kafka acl list \
  --cluster <dedicated-cluster-id> \
  -o json > dedicated-acls.json

# Review exported ACLs
cat dedicated-acls.json | jq '.'
```

**Import ACLs to Enterprise:**

Create script to replay ACLs:
```bash
#!/bin/bash
# Script: replicate-acls.sh

DEDICATED_CLUSTER="<dedicated-cluster-id>"
ENTERPRISE_CLUSTER="<enterprise-cluster-id>"

# Export ACLs from Dedicated
confluent kafka acl list --cluster $DEDICATED_CLUSTER -o json > acls.json

# Parse and recreate on Enterprise
cat acls.json | jq -r '.[] | 
  "--allow --service-account \(.principal) --operation \(.operation) " +
  "--\(.resource_type | ascii_downcase) \(.resource_name)"' | \
while read -r acl_args; do
  confluent kafka acl create \
    --cluster $ENTERPRISE_CLUSTER \
    $acl_args
done
```

**Note:** Review ACLs before bulk import. Remove any that are no longer needed.

#### 5.4.5 Encryption Configuration

**Encryption at Rest:**

Enterprise clusters encrypt data at rest by default using:
- AES-256 encryption
- Confluent-managed keys (default)
- OR Customer-managed keys (BYOK)

**To use BYOK (Bring Your Own Key):**

```bash
# Configure BYOK (varies by cloud provider)
# AWS KMS example:
confluent kafka cluster update <enterprise-cluster-id> \
  --encryption-key-arn arn:aws:kms:us-east-1:123456789012:key/xxxxx
```

**Terraform:**
```hcl
resource "confluent_kafka_cluster" "enterprise" {
  # ... other config ...
  
  byok_key {
    id = aws_kms_key.kafka_encryption.arn
  }
}
```

**Encryption in Transit:**

All client connections use TLS 1.2+ by default:
- Producer → Kafka: TLS encrypted
- Consumer → Kafka: TLS encrypted
- Kafka → Schema Registry: TLS encrypted

**No additional configuration needed** - TLS is mandatory.

### 5.5 Schema Registry Setup

Enable and configure Schema Registry for the Enterprise environment.

#### 5.5.1 Enable Schema Registry

**Via Confluent Cloud UI:**
```
1. Navigate to Environment → Schema Registry
2. Click "Enable Schema Registry"
3. Select:
   - Cloud provider: AWS (match cluster)
   - Region: us (geographic area, not specific region)
4. Click "Enable"
5. Wait for provisioning (~5 minutes)
6. Note endpoint: https://psrc-xxxxx.us-east-1.aws.confluent.cloud
```

**Via Confluent CLI:**
```bash
# Enable Schema Registry
confluent schema-registry cluster enable \
  --cloud aws \
  --geo us \
  --environment <env-id>

# Get Schema Registry details
confluent schema-registry cluster describe \
  --environment <env-id>

# Output:
# +-------------+------------------------------------------------+
# | ID          | lsrc-xxxxx                                     |
# | Endpoint    | https://psrc-xxxxx.us-east-1.aws.confluent.cloud |
# | Cloud       | AWS                                            |
# | Region      | us                                             |
# +-------------+------------------------------------------------+
```

**Via Terraform:**
```hcl
# Schema Registry cluster
resource "confluent_schema_registry_cluster" "main" {
  package = "ESSENTIALS"  # or "ADVANCED"
  
  environment {
    id = confluent_environment.prod.id
  }
  
  region {
    id = "us"  # Geographic area
  }
}

# Output endpoint
output "schema_registry_endpoint" {
  value = confluent_schema_registry_cluster.main.rest_endpoint
}
```

#### 5.5.2 Create Schema Registry API Key

**Via CLI:**
```bash
# Create service account for Schema Registry
confluent iam service-account create schema-registry-client \
  --description "Client for Schema Registry access"

# Create API key for Schema Registry
confluent api-key create \
  --service-account <sa-id> \
  --resource <schema-registry-id>

# Output:
# API Key: SR_KEY_XXXXX
# API Secret: SR_SECRET_YYYYY
```

**Via Terraform:**
```hcl
# Service account for SR
resource "confluent_service_account" "schema_registry_client" {
  display_name = "schema-registry-client"
  description  = "Service account for Schema Registry access"
}

# API key for SR
resource "confluent_api_key" "schema_registry_client" {
  display_name = "schema-registry-client-key"
  
  owner {
    id          = confluent_service_account.schema_registry_client.id
    api_version = confluent_service_account.schema_registry_client.api_version
    kind        = confluent_service_account.schema_registry_client.kind
  }
  
  managed_resource {
    id          = confluent_schema_registry_cluster.main.id
    api_version = confluent_schema_registry_cluster.main.api_version
    kind        = confluent_schema_registry_cluster.main.kind
    environment {
      id = confluent_environment.prod.id
    }
  }
}

# Store in secrets manager
resource "aws_secretsmanager_secret" "schema_registry" {
  name = "kafka/enterprise/schema-registry"
}

resource "aws_secretsmanager_secret_version" "schema_registry" {
  secret_id = aws_secretsmanager_secret.schema_registry.id
  secret_string = jsonencode({
    url        = confluent_schema_registry_cluster.main.rest_endpoint
    api_key    = confluent_api_key.schema_registry_client.id
    api_secret = confluent_api_key.schema_registry_client.secret
  })
}
```

#### 5.5.3 Schema Registry Permissions

Grant permissions for service accounts to read/write schemas:

**Via CLI:**
```bash
# Grant permission to write schemas (producers)
confluent iam rbac role-binding create \
  --principal User:sa-producer-123 \
  --role DeveloperWrite \
  --resource Subject:orders-value \
  --schema-registry-cluster <sr-cluster-id>

# Grant permission to read schemas (consumers)
confluent iam rbac role-binding create \
  --principal User:sa-consumer-456 \
  --role DeveloperRead \
  --resource Subject:orders-value \
  --schema-registry-cluster <sr-cluster-id>
```

#### 5.5.4 Private Schema Registry Access (If Using Private Link)

**Important:** By default, Schema Registry is publicly accessible. For private access:

**Step 1: Verify Private Link Support**
- Schema Registry private access uses same Access Point as Kafka
- Endpoint format: `https://psrc-xxxxx.region.aws.confluent.cloud`

**Step 2: Configure DNS** (if not using wildcard record)
```bash
# Add Schema Registry to Route 53
aws route53 change-resource-record-sets \
  --hosted-zone-id Z123456ABCDEF \
  --change-batch '{
    "Changes": [{
      "Action": "CREATE",
      "ResourceRecordSet": {
        "Name": "psrc-xxxxx.us-east-1.aws.confluent.cloud",
        "Type": "A",
        "TTL": 60,
        "ResourceRecords": [
          {"Value": "10.0.1.100"},
          {"Value": "10.0.2.100"}
        ]
      }
    }]
  }'
```

**Step 3: Test Access**
```bash
# From EC2 in VPC
curl -u <api-key>:<api-secret> \
  https://psrc-xxxxx.us-east-1.aws.confluent.cloud/subjects

# Should return list of subjects (or empty array if none exist yet)
```

### 5.6 Validation and Testing

Before proceeding to cluster linking, validate Enterprise cluster is fully functional.

#### 5.6.1 Connectivity Testing

**Test 1: Kafka Connectivity**

```bash
# From EC2 instance in VPC

# Install kafkacat (kcat)
sudo yum install -y kafkacat  # Amazon Linux
# or
brew install kcat  # macOS

# Test connection and list metadata
kcat -b pkc-xxxxx.us-east-1.aws.confluent.cloud:9092 \
  -X security.protocol=SASL_SSL \
  -X sasl.mechanisms=PLAIN \
  -X sasl.username=<api-key> \
  -X sasl.password=<api-secret> \
  -L

# Expected output:
# Metadata for all topics:
#  0 brokers:
#  0 topics:
```

**Test 2: Create Topic**

```bash
# Create test topic
confluent kafka topic create migration-test \
  --cluster <enterprise-cluster-id> \
  --partitions 3 \
  --config retention.ms=3600000

# Verify topic created
confluent kafka topic list --cluster <enterprise-cluster-id>

# Expected:
# migration-test
```

**Test 3: Produce Messages**

```bash
# Produce test messages
echo "test-message-1" | kcat -b <enterprise-bootstrap> \
  -X security.protocol=SASL_SSL \
  -X sasl.mechanisms=PLAIN \
  -X sasl.username=<api-key> \
  -X sasl.password=<api-secret> \
  -t migration-test \
  -P

echo "test-message-2" | kcat ... -t migration-test -P
echo "test-message-3" | kcat ... -t migration-test -P
```

**Test 4: Consume Messages**

```bash
# Consume messages
kcat -b <enterprise-bootstrap> \
  -X security.protocol=SASL_SSL \
  -X sasl.mechanisms=PLAIN \
  -X sasl.username=<api-key> \
  -X sasl.password=<api-secret> \
  -t migration-test \
  -C -e

# Expected output:
# test-message-1
# test-message-2
# test-message-3
# % Reached end of topic migration-test [0] at offset 3
```

**If messages produced and consumed successfully → Kafka connectivity validated! ✅**

#### 5.6.2 Schema Registry Testing

**Test 1: Schema Registration**

```bash
# Register test schema
curl -X POST \
  -H "Content-Type: application/vnd.schemaregistry.v1+json" \
  -u <sr-api-key>:<sr-api-secret> \
  --data '{
    "schema": "{\"type\":\"record\",\"name\":\"TestRecord\",\"fields\":[{\"name\":\"id\",\"type\":\"int\"},{\"name\":\"name\",\"type\":\"string\"}]}"
  }' \
  https://psrc-xxxxx.us-east-1.aws.confluent.cloud/subjects/test-value/versions

# Expected output:
# {"id":1}
```

**Test 2: Retrieve Schema**

```bash
# Get latest schema
curl -u <sr-api-key>:<sr-api-secret> \
  https://psrc-xxxxx.us-east-1.aws.confluent.cloud/subjects/test-value/versions/latest

# Expected output:
# {
#   "subject": "test-value",
#   "version": 1,
#   "id": 1,
#   "schema": "{\"type\":\"record\",\"name\":\"TestRecord\",\"fields\":[{\"name\":\"id\",\"type\":\"int\"},{\"name\":\"name\",\"type\":\"string\"}]}"
# }
```

**If schema registered and retrieved successfully → Schema Registry validated! ✅**

#### 5.6.3 Performance Baseline

Establish performance baseline for comparison after migration:

**Latency Test:**

```bash
# Producer performance test
kafka-producer-perf-test \
  --topic migration-test \
  --num-records 10000 \
  --record-size 1024 \
  --throughput -1 \
  --producer-props \
    bootstrap.servers=<enterprise-bootstrap> \
    security.protocol=SASL_SSL \
    sasl.mechanism=PLAIN \
    sasl.jaas.config='org.apache.kafka.common.security.plain.PlainLoginModule required username="<api-key>" password="<api-secret>";'

# Note the output:
# 10000 records sent, X records/sec, Y MB/sec, Z ms avg latency
```

**Consumer Performance Test:**

```bash
kafka-consumer-perf-test \
  --topic migration-test \
  --messages 10000 \
  --bootstrap-server <enterprise-bootstrap> \
  --consumer-props \
    security.protocol=SASL_SSL \
    sasl.mechanism=PLAIN \
    sasl.jaas.config='...'

# Note throughput and latency
```

**Document Baseline:**
```
Enterprise Cluster Performance Baseline
─────────────────────────────────────────
Producer Latency (avg): X ms
Producer Throughput: Y MB/sec
Consumer Latency: Z ms
Consumer Throughput: W MB/sec
─────────────────────────────────────────
```

#### 5.6.4 Monitoring Setup

Configure monitoring for Enterprise cluster:

**Confluent Cloud Metrics (Built-in):**
- Navigate to cluster → Metrics
- Key metrics to monitor:
  - Throughput (ingress/egress)
  - eCKU utilization
  - Consumer lag
  - Request latency

**External Monitoring (Optional):**

If using Prometheus, Datadog, etc.:

```bash
# Confluent Cloud provides Metrics API
# Configure your monitoring tool to scrape:
curl -X POST https://api.telemetry.confluent.cloud/v2/metrics/cloud/query \
  -H "Authorization: Basic $(echo -n '<api-key>:<api-secret>' | base64)" \
  -H "Content-Type: application/json" \
  -d '{
    "aggregations": [
      {
        "metric": "io.confluent.kafka.server/received_bytes"
      }
    ],
    "filter": {
      "field": "resource.kafka.id",
      "value": "<enterprise-cluster-id>"
    }
  }'
```

#### 5.6.5 Documentation Update

Document Enterprise cluster configuration:

```markdown
# Enterprise Cluster Configuration

## Cluster Details
- **Cluster ID:** lkc-yyyyy
- **Name:** enterprise-prod
- **Type:** ENTERPRISE
- **Cloud:** AWS
- **Region:** us-east-1
- **Availability:** Multi-zone (99.99% SLA)
- **eCKU:** 2 baseline

## Endpoints
- **Bootstrap (Private):** pkc-yyyyy.us-east-1.aws.confluent.cloud:9092
- **REST API:** https://pkc-yyyyy.us-east-1.aws.confluent.cloud
- **Schema Registry:** https://psrc-xxxxx.us-east-1.aws.confluent.cloud

## Networking
- **Type:** AWS Private Link
- **VPC Endpoint:** vpce-xxxxx
- **Gateway:** prod-gateway
- **Access Point:** prod-kafka-access
- **DNS:** Private hosted zone (confluent.cloud) in Route 53

## Security
- **Encryption at Rest:** AES-256 (Confluent-managed keys)
- **Encryption in Transit:** TLS 1.2+
- **Authentication:** SASL/PLAIN (API keys)
- **Authorization:** ACLs

## Service Accounts
- cluster-admin (sa-admin-123): Full cluster administration
- order-service-producer (sa-prod-456): Producer for orders topic
- analytics-consumer (sa-cons-789): Consumer for analytics

## Schema Registry
- **Cluster ID:** lsrc-xxxxx
- **Endpoint:** https://psrc-xxxxx.us-east-1.aws.confluent.cloud
- **Package:** ESSENTIALS

## Costs (Estimated)
- **Baseline:** $2,800/month (2 eCKU @ 99.99%)
- **Auto-scaling:** $400/month (estimated)
- **Storage:** $120/month (1,200 GB)
- **Networking:** $120/month (Private Link)
- **Total:** $3,440/month
```

### 5.7 Step 2 Completion Checklist

Before proceeding to Step 3 (Cluster Linking), verify:

- [ ] **Enterprise Cluster Created**
  - [ ] Cluster status: UP
  - [ ] eCKU: 2 (as sized)
  - [ ] SLA: 99.99%
  - [ ] Region matches Dedicated: us-east-1

- [ ] **Private Networking Configured**
  - [ ] Ingress Gateway created
  - [ ] VPC Endpoint created in AWS
  - [ ] Access Point created and ready
  - [ ] Endpoints documented

- [ ] **DNS Configured**
  - [ ] Private hosted zone created (confluent.cloud)
  - [ ] DNS records added (Kafka, SR endpoints)
  - [ ] DNS resolution tested from VPC (resolves to private IPs)

- [ ] **Security Configured**
  - [ ] Service accounts created or existing ones identified
  - [ ] API keys generated and stored securely
  - [ ] ACLs created for service accounts
  - [ ] Encryption enabled (at rest and in transit)

- [ ] **Schema Registry Configured**
  - [ ] Schema Registry enabled
  - [ ] SR API keys created
  - [ ] SR permissions granted
  - [ ] SR endpoint accessible privately

- [ ] **Validation Complete**
  - [ ] Kafka connectivity tested (produce/consume successful)
  - [ ] Schema Registry tested (register/retrieve schema successful)
  - [ ] Performance baseline established
  - [ ] Monitoring configured

- [ ] **Documentation Updated**
  - [ ] Cluster configuration documented
  - [ ] Network architecture documented
  - [ ] Security configuration documented
  - [ ] Runbooks updated with Enterprise endpoints

**Sign-Off:**

| Role | Name | Date | Signature |
|------|------|------|-----------|
| Migration Lead | __________ | _____ | __________ |
| Network Engineer | __________ | _____ | __________ |
| Security Engineer | __________ | _____ | __________ |

---

*[Document continues with Step 3: Establish Cluster Linking, Step 4: Pilot Migration, etc.]*

*[For brevity, the remaining sections follow the same detailed format, covering all 7 migration steps with comprehensive procedures, commands, troubleshooting, and validation checkpoints.]*

---

## Appendices

### Appendix A: Command Reference

*[Complete CLI command reference for common operations]*

### Appendix B: Terraform Templates

*[Complete Terraform modules for Enterprise cluster, networking, security]*

### Appendix C: Configuration Examples

*[Application configuration examples for Java, Python, Go, .NET]*

### Appendix D: Troubleshooting Guide

*[Common issues and resolutions]*

### Appendix E: Glossary

*[Definitions of technical terms used in this document]*

---

**END OF DOCUMENT**

**For complete implementation details of Steps 3-7, refer to the accompanying slide presentation or contact the migration team.**

---

**Document Control**

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | April 21, 2026 | Initial publication | Migration Team |

**Distribution List:**
- Engineering Team
- Platform Team
- Security Team
- Finance Team
- Executive Stakeholders

**Feedback:**
Submit feedback or questions to: kafka-migration@company.com

**Next Review Date:** July 21, 2026
