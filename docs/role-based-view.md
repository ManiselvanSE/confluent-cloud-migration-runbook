# Role-Based Migration View
## Confluent Cloud: Single-AZ to Multi-AZ Migration

This document provides **role-specific perspectives** on the Confluent Cloud migration from Single-AZ Dedicated to Multi-AZ Enterprise clusters. Use the section relevant to your role.

---

# 1. Solution Engineer View
**Audience**: Pre-sales engineers, solution consultants, customer success engineers

## Business Problem

**Current State Risk**: Single-availability-zone Kafka clusters create a single point of failure. When that zone goes down (power failure, network outage, cloud provider incident), the entire event streaming platform becomes unavailable. For businesses processing real-time transactions, IoT telemetry, or customer interactions, this means:

- **Revenue Impact**: Lost transactions during outage (e-commerce, payments)
- **Operational Blindness**: Missing critical telemetry (fraud detection, monitoring)
- **Customer Experience**: Application failures (mobile apps, web services)
- **Compliance Risk**: SLA violations, regulatory breaches

**Business Value of Multi-AZ Migration**:

| Benefit | Single-AZ | Multi-AZ | Business Impact |
|---------|-----------|----------|-----------------|
| **Availability** | 99.5% - 99.9% | 99.99% | 52 min downtime/year vs. 4 hours/year |
| **Durability** | RF=1-2 (zone-only) | RF=3 (cross-zone) | Zero data loss on zone failure |
| **Recovery Time** | Hours (manual recovery) | <30 seconds (automatic failover) | Minutes of downtime vs. hours |
| **Compliance** | Single failure domain | Multi-failure domain | Meets financial/healthcare regulations |

## Customer Discussion Points

### Opening Questions

1. **"What's your current uptime SLA, and what does downtime cost your business?"**
   - Quantify downtime cost (revenue/hour lost)
   - Compare to migration investment

2. **"Have you experienced cloud infrastructure failures in the past?"**
   - AWS us-east-1 outages (2021, 2022)
   - Relate to Kafka availability dependency

3. **"What compliance requirements do you have around data durability and availability?"**
   - PCI-DSS, HIPAA, SOC 2
   - Multi-AZ often required for certification

### Migration Value Proposition

**"We can migrate your Kafka platform to a zone-resilient architecture with near-zero downtime and no data loss."**

**Key Selling Points**:

✅ **Resilience**: Survives complete availability zone failures  
✅ **Near-Zero Downtime**: <5 minute consumer interruption during cutover  
✅ **No Data Loss**: Automatic offset translation, byte-identical replication  
✅ **Reversible**: Full rollback capability within 15 minutes if issues arise  
✅ **Proven Technology**: Confluent Cluster Linking used in thousands of enterprise migrations  

### Objection Handling

| Objection | Response |
|-----------|----------|
| **"Migration sounds risky, what if it fails?"** | "We use Cluster Linking, which replicates in parallel to your production system. If any issues arise during cutover, we can revert to the source cluster in <15 minutes. The link remains active as a safety net for 7-14 days post-migration." |
| **"We can't afford downtime for migration."** | "Producer downtime is zero (rolling restart). Consumer downtime is <5 minutes during cutover. Kafka Connect pauses for 2-5 minutes. This is a live migration, not a rebuild." |
| **"Multi-AZ will be too expensive."** | "Typical cost increase is 30-40% (higher replication, cross-zone bandwidth). Calculate cost-per-hour of downtime vs. annual multi-AZ premium. For most businesses, a single 1-hour outage costs more than the yearly increase." |
| **"Our team doesn't have capacity."** | "We provide full runbooks, automation scripts, and can execute as a managed service. Typical hands-on effort: 2 weeks planning, 4 hours cutover execution, 2 weeks monitoring." |

### Demo Flow (15-Minute Walkthrough)

**1. Current State Walkthrough** (3 min)
- Show Single-AZ architecture diagram
- Point out single failure domain
- "If this zone fails, entire cluster unavailable"

**2. Target State Overview** (3 min)
- Show Multi-AZ architecture diagram
- Explain 3-zone replica distribution
- "Zone A fails → automatic failover to Zones B & C"

**3. Migration Approach** (5 min)
- Explain Cluster Linking (Active-Passive replication)
- Show migration timeline (10 weeks overview)
- Highlight cutover window (4 hours, <5 min consumer downtime)

**4. Risk Mitigation** (2 min)
- Offset translation (no consumer reprocessing)
- Rollback plan (<15 min revert)
- Validation procedures (data integrity checks)

**5. ROI Summary** (2 min)
- Quantify downtime risk eliminated
- Compare cost increase vs. downtime cost
- Next steps: technical deep-dive, staging environment test

### Success Metrics

**Present these outcomes**:

- **Availability Improvement**: 99.5% → 99.99% (10x reduction in downtime)
- **Mean Time to Recovery (MTTR)**: Hours → <30 seconds (automatic failover)
- **Data Loss Risk**: Possible (RF=1-2) → Zero (RF=3, min.insync.replicas=2)
- **Business Continuity**: Manual recovery → Automatic (no human intervention needed)

### Customer References

**Use cases to highlight**:

- **Financial Services**: Payment processing platform (zero-downtime mandate)
- **E-Commerce**: Real-time inventory and order management (revenue protection)
- **IoT/Telemetry**: Manufacturing sensor data (operational continuity)
- **Healthcare**: Patient monitoring systems (regulatory compliance)

### Next Steps After Demo

1. **Technical Deep-Dive Session**: Solution Architect presents detailed design (networking, security, cutover)
2. **Staging Environment Test**: Proof-of-concept migration on customer's non-prod cluster
3. **Cost-Benefit Analysis**: Detailed TCO comparison (current vs. multi-AZ)
4. **Migration Planning Workshop**: Define timeline, roles, responsibilities
5. **Executive Briefing**: Present business case to decision-makers

---

# 2. Solution Architect View
**Audience**: Solutions architects, cloud architects, platform engineers

## Architecture Design Considerations

### Current State: Single-AZ Dedicated Cluster

**Architecture Characteristics**:

```
┌─────────────────────────────────────────┐
│   Single Availability Zone (us-east-1a) │
│                                          │
│  ┌──────┐  ┌──────┐  ┌──────┐          │
│  │ B-1  │  │ B-2  │  │ B-3  │          │
│  │(RF=1)│  │(RF=1)│  │(RF=1)│          │
│  └──────┘  └──────┘  └──────┘          │
│                                          │
│  Failure Domain: Single Zone            │
└─────────────────────────────────────────┘

Constraints:
- Replication Factor: 1-2 (in-zone only)
- min.insync.replicas: 1
- Zone failure = complete outage
- No automatic failover capability
```

**Limitations**:
- **Availability**: Zone-dependent (99.5-99.9%)
- **Durability**: Limited to in-zone replication
- **Fault Tolerance**: Cannot survive zone failure
- **Scalability**: Bounded by single-zone capacity

### Target State: Multi-AZ Enterprise Cluster

**Architecture Characteristics**:

```
┌──────────────────────────────────────────────────────────┐
│            Multi-AZ Enterprise Cluster                    │
│                                                            │
│  ┌─────────┐      ┌─────────┐      ┌─────────┐          │
│  │ Zone A  │      │ Zone B  │      │ Zone C  │          │
│  │  B-1    │◄────►│  B-2    │◄────►│  B-3    │          │
│  │  B-4    │      │  B-5    │      │  B-6    │          │
│  │(Replica)│      │(Replica)│      │(Replica)│          │
│  └─────────┘      └─────────┘      └─────────┘          │
│                                                            │
│  Replication Factor: 3 (one per zone)                     │
│  min.insync.replicas: 2 (requires 2 zones for ack)        │
│  Zone A fails → B & C continue (zero downtime)            │
└──────────────────────────────────────────────────────────┘

For topic "orders" partition 0:
- Leader: Broker 1 (Zone A)
- Follower 1: Broker 2 (Zone B) [ISR]
- Follower 2: Broker 3 (Zone C) [ISR]

Zone A failure → Leader election → Broker 2 becomes leader
```

**Improvements**:
- **Availability**: 99.99% (survives zone failure)
- **Durability**: Cross-zone replication (RF=3)
- **Fault Tolerance**: Automatic failover (<30s)
- **Scalability**: Horizontal across zones

### Migration Strategy: Cluster Linking (Active-Passive)

**Why Cluster Linking (vs. Alternatives)**:

| Approach | Downtime | Data Loss Risk | Complexity | Confluent Recommendation |
|----------|----------|----------------|------------|--------------------------|
| **Cluster Linking** | <5 min | Zero | Medium | ✅ **Recommended** |
| Rebuild from Scratch | Hours | Possible | High | ❌ Not Recommended |
| MirrorMaker 2 | Hours | Possible | High | ❌ Deprecated |
| Manual Export/Import | Days | High | Very High | ❌ Not Feasible |

**Cluster Linking Architecture**:

```
Source Cluster                    Destination Cluster
(Single-AZ Dedicated)             (Multi-AZ Enterprise)
┌─────────────────┐              ┌─────────────────┐
│ Topic: orders   │              │ Topic: orders   │
│ Partitions: 12  │──Link────────►│ Partitions: 12  │
│ RF: 1           │ Replication  │ RF: 3           │
│ Offset: 500K    │              │ Offset: 500K    │
└─────────────────┘              └─────────────────┘
        ▲                                 │
        │                                 │
   Producers/                        Read-Only
   Consumers                         (Syncing)
   (Active)                               │
                                          ▼
                    CUTOVER: Switch traffic to Destination
```

**Key Design Decisions**:

1. **Link Direction**: Destination → Source (destination pulls from source)
2. **Offset Translation**: Automatic (consumer.offset.sync.enable=true)
3. **ACL Sync**: Enabled (acl.sync.enable=true)
4. **Mirror Topics**: Read-only until cutover (prevents accidental writes)

### Networking Architecture

#### Option 1: VPC Peering (AWS Example)

**Topology**:
```
Customer VPC                    Confluent Cloud VPC
(Application Tier)              (Multi-AZ Cluster)
┌────────────────┐              ┌────────────────┐
│  Subnet-1 (AZ-A)│              │  Zone A        │
│  Subnet-2 (AZ-B)│◄──Peering──►│  Zone B        │
│  Subnet-3 (AZ-C)│              │  Zone C        │
└────────────────┘              └────────────────┘

Route Tables:
Customer VPC → Confluent CIDR via Peering
Confluent VPC → Customer CIDR via Peering

Security Groups:
Egress: Port 9092 (Kafka), 443 (Schema Registry)
```

**Pros**: Simple, low latency, no PrivateLink cost  
**Cons**: CIDR overlap risk, more route table management

#### Option 2: PrivateLink (Recommended for Enterprise)

**Topology**:
```
Customer VPC                         PrivateLink
┌────────────────┐                ┌──────────────┐
│ VPC Endpoint   │◄───Interface──►│ Endpoint Svc │
│ (eni-xxx)      │                │ (Confluent)  │
│ Private IP     │                └──────────────┘
└────────────────┘                         │
        ▲                                  ▼
   Applications                  Multi-AZ Cluster

Benefits:
- No CIDR overlap issues
- Simplified security (no route tables)
- Private DNS names
- AWS-managed connectivity
```

**Recommendation**: Use PrivateLink for production enterprise deployments

### Security Architecture

**Authentication**: SASL/PLAIN with API keys
```
Service Accounts (per application/team):
- sa-producer-orders (WRITE on topic "orders")
- sa-consumer-analytics (READ on topic "orders", consumer group "analytics")
- sa-cluster-link (DeveloperRead on source, DeveloperWrite on destination)
```

**Authorization**: ACLs (Access Control Lists)
```
Example ACL for producer:
  Principal: User:sa-producer-orders
  Operation: WRITE
  Resource: Topic:orders
  Permission: ALLOW

Example ACL for consumer:
  Principal: User:sa-consumer-analytics
  Operation: READ
  Resource: Topic:orders, Group:analytics-group
  Permission: ALLOW
```

**Encryption**:
- **In-Transit**: TLS 1.2+ for all broker and client connections
- **At-Rest**: AES-256 encryption (Confluent Cloud default)

**Network Security**:
- Private connectivity only (VPC Peering or PrivateLink)
- No public internet exposure
- Security groups restrict ingress to application VPCs

### Scaling & Resiliency Design

**Replication Factor Strategy**:

| Topic Type | RF | min.insync.replicas | Rationale |
|------------|----|--------------------|-----------|
| Critical (orders, payments) | 3 | 2 | Zero data loss, survives 1 zone failure |
| Non-Critical (logs, metrics) | 3 | 2 | Consistency with cluster default |
| Test/Dev | 3 | 1 | Lower durability acceptable |

**Partition Distribution**:
- Confluent Cloud automatically distributes partitions across zones (rack-awareness)
- Leader replicas balanced across zones for even load

**Scalability Considerations**:
- **Vertical**: Enterprise cluster supports larger CKU sizes (4, 8, 16+ CKUs)
- **Horizontal**: Add brokers across zones as load increases
- **Auto-Scaling**: Confluent Cloud auto-scales storage; CKU scaling requires manual intervention

**Resiliency Guarantees**:
- **Zone Failure**: Automatic failover, zero data loss (with min.insync.replicas=2)
- **Broker Failure**: Leader election within seconds
- **Network Partition**: Producers block until ISR restored (no data loss)

### Performance Characteristics

**Latency Impact**:

| Metric | Single-AZ | Multi-AZ | Delta |
|--------|-----------|----------|-------|
| Producer p99 (acks=all) | 10-15ms | 13-22ms | +3-7ms |
| Consumer p99 | 5-10ms | 5-10ms | Negligible |
| End-to-End | 50-100ms | 55-110ms | +5-10ms |

**Mitigation**: Producer batching, compression (snappy/lz4)

**Throughput**:
- **Single-AZ**: ~50-100 MB/s per CKU
- **Multi-AZ**: ~50-100 MB/s per CKU (same, more brokers compensate for cross-zone overhead)

### Data Consistency Guarantees

**Cluster Linking Replication**:
- **Ordering**: Preserved within each partition
- **Exactly-Once**: Not preserved (transactions are cluster-specific; requires application-level idempotency)
- **Offset Preservation**: Automatic translation via offset sync
- **Schema Compatibility**: Shared Schema Registry (no migration needed)

**Producer Durability Settings**:
```properties
# Recommended for Multi-AZ
acks=all                    # Wait for all ISR replicas (min 2 zones)
enable.idempotence=true     # Prevent duplicates on retry
max.in.flight.requests=5    # Pipelining for throughput
```

**Consumer Reliability Settings**:
```properties
enable.auto.commit=false    # Manual commit for exactly-once
isolation.level=read_committed  # Only read committed transactions (if using EOS)
```

### Risk Assessment & Mitigation

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Consumer offset translation failure | Medium | High | Validate ALL consumer groups pre-cutover; manual reset capability |
| Cross-zone latency degrades application SLA | Medium | Medium | Performance test in staging; tune producer batching |
| Network connectivity failure during replication | Low | High | Pre-validate VPC peering/PrivateLink; monitor Cluster Link lag |
| ACL mismatch post-migration | Low | High | Enable acl.sync.enable=true; diff ACLs pre-cutover |
| Schema Registry compatibility break | Low | Critical | Freeze schema changes during cutover window |
| Application config error (wrong bootstrap server) | Medium | Critical | Canary rollout (5% producers first); validate configs in CI/CD |

### Rollback Architecture

**Design Principle**: Keep Cluster Link active for 7-14 days post-cutover

**Rollback Capability**:
```
T+0: Cutover complete, all traffic on Destination
T+1 day: Issue discovered (e.g., consumer lag increasing)
T+1 day + 15 min: Rollback executed
  - Stop destination traffic
  - Revert configs to Source
  - Restart on Source
  - Source Cluster Link still active (safety net remains)
```

**Rollback Decision Tree**:
- Data loss detected (>1%) → **Immediate rollback**
- Producer error rate >5% → **Rollback within 15 min**
- Consumer lag uncontrollable → **Rollback within 15 min**
- Latency >2x baseline → **Evaluate; rollback if SLA breached**

### Cost Architecture

**Cost Breakdown**:

| Component | Single-AZ | Multi-AZ | Increase |
|-----------|-----------|----------|----------|
| Cluster Compute (CKUs) | 4 CKUs × $1.50/hr | 4 CKUs × $2.00/hr | +33% |
| Storage (RF=1 → RF=3) | 1TB × $0.10/GB | 3TB × $0.10/GB | +200% |
| Cross-Zone Bandwidth | $0 | ~$50-100/mo | Variable |
| **Total Monthly** | ~$2,000 | ~$2,800 | +40% |

**Optimization Strategies**:
- Reduce retention for non-critical topics (7d → 3d)
- Enable tiered storage (move old data to S3)
- Right-size CKUs after 2-week monitoring period
- Enable compression (snappy) to reduce storage and bandwidth

### Migration Timeline (Architecture Phases)

**Week 1-2**: Architecture design & planning
- Network topology design (VPC peering vs. PrivateLink)
- Capacity planning (CKU sizing based on growth projections)
- Security design (service accounts, ACLs, RBAC)

**Week 3**: Staging environment validation
- Provision staging multi-AZ cluster
- Execute full migration rehearsal
- Performance benchmarking

**Week 4**: Production cluster provisioning
- Provision multi-AZ Enterprise cluster
- Configure networking (VPC peering/PrivateLink)
- Create service accounts and API keys

**Week 4-5**: Cluster Link replication
- Establish Cluster Link
- Monitor lag reduction (10M messages → 100 → 0)

**Week 6**: Production cutover (4-hour window)
- Producer cutover (phased: 5% → 100%)
- Consumer cutover (offset translation validated)
- Kafka Connect reconfiguration

**Week 6-8**: Monitoring & validation
- 24/7 monitoring
- Performance tuning
- Issue resolution

**Week 10**: Decommission
- Delete Cluster Link
- Delete source cluster
- Update documentation

---

# 3. Implementation Consultant View
**Audience**: DevOps engineers, SREs, implementation consultants, migration execution teams

## Step-by-Step Execution Approach

### Phase 1: Pre-Migration Preparation (Week 1-2)

#### Step 1.1: Inventory Source Cluster

**Objective**: Document complete source cluster state

**Execution**:

```bash
# 1.1.1 List all topics
confluent kafka topic list --cluster <source-cluster-id> > topics-inventory.txt

# 1.1.2 Export topic configurations
while read topic; do
  confluent kafka topic describe $topic \
    --cluster <source-cluster-id> -o json \
    > "topic-config-${topic}.json"
done < topics-inventory.txt

# 1.1.3 List consumer groups
confluent kafka consumer group list \
  --cluster <source-cluster-id> > consumer-groups.txt

# 1.1.4 Export consumer group lag
while read group; do
  confluent kafka consumer group describe $group \
    --cluster <source-cluster-id> \
    > "consumer-group-${group}.txt"
done < consumer-groups.txt

# 1.1.5 Export ACLs
kafka-acls --bootstrap-server <source-bootstrap>:9092 \
  --command-config source-client.properties \
  --list > acls-export.txt

# 1.1.6 List Kafka Connect connectors
confluent connect list --cluster <connect-cluster-id> -o json \
  > connectors-inventory.json
```

**Deliverable**: Inventory spreadsheet with:
- Topic count, partition totals, replication factors
- Consumer group count, total lag
- Connector count (source vs. sink)
- ACL count

#### Step 1.2: Network Connectivity Setup

**Objective**: Establish VPC peering or PrivateLink

**Execution (VPC Peering - AWS)**:

```bash
# 1.2.1 Create VPC peering request
# (via Confluent Cloud Console: Cluster → Networking → VPC Peering → Add)
# Record peering connection ID: pcx-xxxxx

# 1.2.2 Accept peering in customer AWS account
aws ec2 accept-vpc-peering-connection \
  --vpc-peering-connection-id pcx-xxxxx \
  --region us-east-1

# 1.2.3 Update route tables
aws ec2 create-route \
  --route-table-id rtb-<customer-rtb-id> \
  --destination-cidr-block <confluent-cloud-cidr> \
  --vpc-peering-connection-id pcx-xxxxx

# 1.2.4 Update security groups
aws ec2 authorize-security-group-egress \
  --group-id sg-<app-sg-id> \
  --protocol tcp \
  --port 9092 \
  --cidr <confluent-cloud-cidr>
```

**Validation**:
```bash
# Test connectivity from application server
telnet <destination-bootstrap-server> 9092
# Expected: Connected

# Test DNS resolution
nslookup <destination-bootstrap-server>
# Expected: Private IP address
```

#### Step 1.3: Provision Destination Cluster

**Objective**: Create Multi-AZ Enterprise cluster

**Execution**:

```bash
# 1.3.1 Create cluster via CLI
confluent kafka cluster create prod-enterprise-multiaz \
  --cloud aws \
  --region us-east-1 \
  --type enterprise \
  --availability multi-zone

# Record cluster ID
DEST_CLUSTER_ID="lkc-xxxxx"

# 1.3.2 Wait for provisioning (15-30 minutes)
confluent kafka cluster describe $DEST_CLUSTER_ID -o json | jq '.status'
# Expected: "PROVISIONED"
```

**Validation**:
```bash
# Verify multi-zone availability
confluent kafka cluster describe $DEST_CLUSTER_ID -o json | jq '.availability'
# Expected: "MULTI_ZONE"
```

#### Step 1.4: Create Service Accounts and API Keys

**Objective**: Set up authentication for Cluster Link and applications

**Execution**:

```bash
# 1.4.1 Create service account for Cluster Link
confluent iam service-account create cluster-link-sa \
  --description "Cluster Linking service account"

SA_LINK=$(confluent iam service-account list -o json \
  | jq -r '.[] | select(.name=="cluster-link-sa") | .id')

# 1.4.2 Create API keys for Cluster Link
# Source cluster key
confluent api-key create \
  --service-account $SA_LINK \
  --resource <source-cluster-id> \
  --description "Cluster link source"

# Store securely (output: API_KEY, API_SECRET)
LINK_SOURCE_KEY="<key>"
LINK_SOURCE_SECRET="<secret>"

# Destination cluster key
confluent api-key create \
  --service-account $SA_LINK \
  --resource $DEST_CLUSTER_ID \
  --description "Cluster link destination"

LINK_DEST_KEY="<key>"
LINK_DEST_SECRET="<secret>"

# 1.4.3 Create service accounts and keys for applications
# (Repeat for each producer/consumer application)
confluent iam service-account create producer-orders-sa
confluent api-key create \
  --service-account <sa-id> \
  --resource $DEST_CLUSTER_ID
```

**Store Secrets**:
```bash
# Example: AWS Secrets Manager
aws secretsmanager create-secret \
  --name confluent/dest/cluster-link \
  --secret-string "{\"api_key\":\"$LINK_DEST_KEY\",\"api_secret\":\"$LINK_DEST_SECRET\"}"
```

### Phase 2: Cluster Link Setup (Week 4)

#### Step 2.1: Create Cluster Link

**Objective**: Establish replication from source to destination

**Execution**:

```bash
# 2.1.1 Create link configuration file
cat > cluster-link.config << EOF
bootstrap.servers=<source-cluster-bootstrap>:9092
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username='$LINK_SOURCE_KEY' password='$LINK_SOURCE_SECRET';

consumer.offset.sync.enable=true
consumer.offset.sync.ms=30000

acl.sync.enable=true
acl.sync.ms=30000

link.mode=DESTINATION
cluster.link.metadata.topic.replication.factor=3
cluster.link.metadata.topic.min.insync.replicas=2
EOF

# 2.1.2 Create Cluster Link
confluent kafka link create migration-link \
  --cluster $DEST_CLUSTER_ID \
  --source-cluster <source-cluster-id> \
  --config-file cluster-link.config
```

**Validation**:
```bash
# Check link status
confluent kafka link describe migration-link \
  --cluster $DEST_CLUSTER_ID

# Expected: State: ACTIVE
```

#### Step 2.2: Create Mirror Topics

**Objective**: Replicate all source topics to destination

**Execution**:

```bash
# 2.2.1 Get list of topics to mirror (exclude internal topics)
SOURCE_TOPICS=$(confluent kafka topic list \
  --cluster <source-cluster-id> -o json \
  | jq -r '.[].name' | grep -v '^_')

# 2.2.2 Create mirrors
for topic in $SOURCE_TOPICS; do
  echo "Creating mirror for: $topic"
  confluent kafka mirror create $topic \
    --link migration-link \
    --cluster $DEST_CLUSTER_ID
  sleep 2
done
```

**Validation**:
```bash
# Verify mirror creation
confluent kafka mirror list \
  --link migration-link \
  --cluster $DEST_CLUSTER_ID
```

#### Step 2.3: Monitor Replication Lag

**Objective**: Wait for lag to reach near-zero

**Execution**:

```bash
# 2.3.1 Create monitoring script
cat > monitor-lag.sh << 'EOF'
#!/bin/bash
while true; do
  echo "=== $(date) ==="
  confluent kafka mirror list \
    --link migration-link \
    --cluster $DEST_CLUSTER_ID -o json \
    | jq -r '.[] | "\(.source_topic_name): \(.num_partition_lag // 0) messages"'
  echo ""
  sleep 60
done
EOF

chmod +x monitor-lag.sh

# 2.3.2 Run monitoring
./monitor-lag.sh
```

**Cutover Readiness Criteria**:
- [ ] Mirror lag < 1000 messages for ALL topics
- [ ] Lag stable for 30+ minutes
- [ ] Mirror state: ACTIVE (no errors)

**Timeline**: Typically 5-7 days for 100TB cluster

### Phase 3: Cutover Execution (Week 6, Day X)

#### Pre-Cutover Checklist (T-1 hour)

```bash
# Final lag check
./monitor-lag.sh | head -20

# Verify consumer offset sync
kafka-consumer-groups --bootstrap-server <source-bootstrap>:9092 \
  --command-config source-client.properties \
  --describe --group <consumer-group> > source-offsets-final.txt

kafka-consumer-groups --bootstrap-server <dest-bootstrap>:9092 \
  --command-config dest-client.properties \
  --describe --group <consumer-group> > dest-offsets-final.txt

# Compare (offsets should match within lag window)
diff source-offsets-final.txt dest-offsets-final.txt
```

#### Cutover Timeline (30-Minute Execution)

**T=0: Pause Kafka Connect**

```bash
# List connectors
CONNECTORS=$(confluent connect list --cluster <connect-cluster-id> -o json \
  | jq -r '.[].name')

# Pause all connectors
for connector in $CONNECTORS; do
  confluent connect pause $connector --cluster <connect-cluster-id>
done

# Validate paused
for connector in $CONNECTORS; do
  confluent connect describe $connector --cluster <connect-cluster-id> | grep State
done
# Expected: State: PAUSED
```

**T+2: Deploy Producer Canary (5%)**

```bash
# Update Kubernetes ConfigMap with destination cluster config
kubectl create configmap producer-orders-config \
  --from-literal=KAFKA_BOOTSTRAP_SERVERS=<dest-bootstrap>:9092 \
  --from-literal=KAFKA_API_KEY=$PRODUCER_KEY \
  --from-literal=KAFKA_API_SECRET=$PRODUCER_SECRET \
  --dry-run=client -o yaml | kubectl apply -f -

# Rolling restart canary deployment
kubectl rollout restart deployment/producer-orders-canary
kubectl rollout status deployment/producer-orders-canary
```

**T+5: Validate Canary**

```bash
# Check messages appearing on destination
kafka-console-consumer \
  --bootstrap-server <dest-bootstrap>:9092 \
  --consumer.config dest-client.properties \
  --topic orders \
  --max-messages 10 \
  --timeout-ms 5000

# Expected: Messages from canary producers
```

**Decision Point**: If canary error rate >1%, STOP and rollback. Else, proceed.

**T+8: Full Producer Rollout**

```bash
# Phase 1: 25%
kubectl set env deployment/producer-orders \
  KAFKA_BOOTSTRAP_SERVERS=<dest-bootstrap>:9092 \
  KAFKA_API_KEY=$PRODUCER_KEY \
  KAFKA_API_SECRET=$PRODUCER_SECRET

kubectl scale deployment/producer-orders --replicas=5  # 25% of 20
kubectl rollout status deployment/producer-orders

# Wait 5 minutes, monitor

# Phase 2: 50%
kubectl scale deployment/producer-orders --replicas=10
kubectl rollout status deployment/producer-orders

# Wait 3 minutes

# Phase 3: 100%
kubectl scale deployment/producer-orders --replicas=20
kubectl rollout status deployment/producer-orders
```

**T+15: Stop Consumers on Source**

```bash
# Gracefully scale down
kubectl scale deployment/consumer-analytics --replicas=0

# Wait for pods to terminate
kubectl wait --for=delete pod -l app=consumer-analytics --timeout=60s
```

**T+17: Capture Final Source Offsets**

```bash
kafka-consumer-groups --bootstrap-server <source-bootstrap>:9092 \
  --command-config source-client.properties \
  --describe --group analytics-group > final-source-offsets.txt
```

**T+18: Start Consumers on Destination**

```bash
# Update consumer config
kubectl set env deployment/consumer-analytics \
  KAFKA_BOOTSTRAP_SERVERS=<dest-bootstrap>:9092 \
  KAFKA_API_KEY=$CONSUMER_KEY \
  KAFKA_API_SECRET=$CONSUMER_SECRET

# Scale up
kubectl scale deployment/consumer-analytics --replicas=10
kubectl rollout status deployment/consumer-analytics
```

**Validation**:
```bash
# Check consumers joined and consuming
kafka-consumer-groups --bootstrap-server <dest-bootstrap>:9092 \
  --command-config dest-client.properties \
  --describe --group analytics-group

# Expected: Consumers assigned to partitions, LAG decreasing
```

**T+22: Validate Offset Translation**

```bash
kafka-consumer-groups --bootstrap-server <dest-bootstrap>:9092 \
  --command-config dest-client.properties \
  --describe --group analytics-group > dest-starting-offsets.txt

# Compare final source vs. destination starting offsets
diff final-source-offsets.txt dest-starting-offsets.txt

# Expected: Offsets match (within replication lag)
```

**T+25: Resume Kafka Connect**

```bash
# Update connector configs (scripted or manual)
# For each connector, update:
#   - kafka.bootstrap.servers → <dest-bootstrap>
#   - kafka.sasl.jaas.config → destination API key

# Example (manual via Confluent Cloud Console or CLI)
for connector in $CONNECTORS; do
  # Export config, update, re-apply
  confluent connect describe $connector --cluster <connect-cluster-id> -o json \
    > connector-$connector.json
  
  # Update config (use jq or manual edit)
  # ...
  
  # Update connector
  confluent connect update $connector \
    --cluster <connect-cluster-id> \
    --config-file connector-$connector-updated.json
  
  # Resume
  confluent connect resume $connector --cluster <connect-cluster-id>
done
```

**T+30: Cutover Complete**

**Post-Cutover Validation Checklist**:
- [ ] All producers writing to destination (metrics show traffic)
- [ ] All consumers reading from destination (lag decreasing)
- [ ] Kafka Connect connectors RUNNING
- [ ] No errors in application logs
- [ ] End-to-end latency <500ms
- [ ] Producer error rate <0.01%

### Phase 4: Post-Migration Validation (Week 6-8)

#### Step 4.1: Data Integrity Validation

**Objective**: Confirm zero data loss

**Execution**:

```bash
# 4.1.1 Compare message counts (source vs. destination)
cat > validate-counts.sh << 'EOF'
#!/bin/bash
TOPICS=$(cat topics-inventory.txt)

echo "Topic,Source Count,Dest Count,Diff,Status"

for topic in $TOPICS; do
  src_count=$(kafka-run-class kafka.tools.GetOffsetShell \
    --broker-list <source-bootstrap>:9092 \
    --topic $topic --time -1 | awk -F: '{sum+=$3} END {print sum}')
  
  dest_count=$(kafka-run-class kafka.tools.GetOffsetShell \
    --broker-list <dest-bootstrap>:9092 \
    --topic $topic --time -1 | awk -F: '{sum+=$3} END {print sum}')
  
  diff=$((src_count - dest_count))
  
  if [ $diff -eq 0 ]; then
    status="✓ MATCH"
  elif [ ${diff#-} -lt 100 ]; then
    status="⚠ MINOR"
  else
    status="✗ MISMATCH"
  fi
  
  echo "$topic,$src_count,$dest_count,$diff,$status"
done
EOF

chmod +x validate-counts.sh
./validate-counts.sh
```

**Expected**: All topics MATCH or MINOR (<100 message difference)

#### Step 4.2: Performance Benchmarking

**Objective**: Validate performance meets baseline

**Execution**:

```bash
# 4.2.1 Producer throughput test
kafka-producer-perf-test \
  --topic perf-test \
  --num-records 1000000 \
  --record-size 1024 \
  --throughput -1 \
  --producer-props \
    bootstrap.servers=<dest-bootstrap>:9092 \
    security.protocol=SASL_SSL \
    sasl.mechanism=PLAIN \
    sasl.jaas.config='...' \
    acks=all \
    compression.type=snappy

# Compare to source baseline (captured pre-migration)

# 4.2.2 Consumer throughput test
kafka-consumer-perf-test \
  --broker-list <dest-bootstrap>:9092 \
  --topic perf-test \
  --messages 1000000 \
  --consumer.config dest-client.properties
```

### Rollback Procedure (If Needed)

**Trigger**: Critical issue detected within 24 hours post-cutover

**Execution** (<15 minute SLA):

```bash
# STEP 1: STOP destination traffic (IMMEDIATE)
kubectl scale deployment/producer-orders --replicas=0
kubectl scale deployment/consumer-analytics --replicas=0

# STEP 2: Revert producer config to source
kubectl set env deployment/producer-orders \
  KAFKA_BOOTSTRAP_SERVERS=<source-bootstrap>:9092 \
  KAFKA_API_KEY=<source-key> \
  KAFKA_API_SECRET=<source-secret>

# STEP 3: Restart producers on source
kubectl scale deployment/producer-orders --replicas=20

# STEP 4: Revert consumer config
kubectl set env deployment/consumer-analytics \
  KAFKA_BOOTSTRAP_SERVERS=<source-bootstrap>:9092 \
  KAFKA_API_KEY=<source-key> \
  KAFKA_API_SECRET=<source-secret>

# STEP 5: Restart consumers on source
kubectl scale deployment/consumer-analytics --replicas=10

# STEP 6: Verify source cluster traffic restored
kafka-topics --bootstrap-server <source-bootstrap>:9092 \
  --command-config source-client.properties \
  --describe --topic orders
```

### Commands & Configuration Reference

#### Confluent CLI Commands

```bash
# Cluster operations
confluent kafka cluster list
confluent kafka cluster describe <cluster-id>
confluent kafka cluster create <name> --cloud aws --region us-east-1 --type enterprise

# Topic operations
confluent kafka topic list --cluster <cluster-id>
confluent kafka topic describe <topic> --cluster <cluster-id>

# Cluster Link operations
confluent kafka link create <link-name> --cluster <dest-cluster-id> --source-cluster <source-cluster-id> --config-file link.config
confluent kafka link describe <link-name> --cluster <cluster-id>
confluent kafka link delete <link-name> --cluster <cluster-id>

# Mirror operations
confluent kafka mirror create <topic> --link <link-name> --cluster <cluster-id>
confluent kafka mirror list --link <link-name> --cluster <cluster-id>

# Consumer group operations
confluent kafka consumer group list --cluster <cluster-id>
confluent kafka consumer group describe <group> --cluster <cluster-id>

# API key operations
confluent api-key create --service-account <sa-id> --resource <cluster-id>
confluent api-key list --resource <cluster-id>
```

#### Kafka CLI Commands

```bash
# Topics
kafka-topics --bootstrap-server <bootstrap> --command-config client.properties --list
kafka-topics --bootstrap-server <bootstrap> --command-config client.properties --describe --topic <topic>

# Consumer groups
kafka-consumer-groups --bootstrap-server <bootstrap> --command-config client.properties --list
kafka-consumer-groups --bootstrap-server <bootstrap> --command-config client.properties --describe --group <group>

# Offset reset (manual)
kafka-consumer-groups --bootstrap-server <bootstrap> \
  --command-config client.properties \
  --reset-offsets --to-datetime 2026-04-20T14:00:00.000 \
  --group <group> --topic <topic> --execute

# ACLs
kafka-acls --bootstrap-server <bootstrap> --command-config client.properties --list
kafka-acls --bootstrap-server <bootstrap> --command-config client.properties --add --allow-principal User:<sa-id> --operation READ --topic <topic>

# Performance testing
kafka-producer-perf-test --topic <topic> --num-records 1000000 --record-size 1024 --throughput -1 --producer-props bootstrap.servers=<bootstrap> ...
kafka-consumer-perf-test --broker-list <bootstrap> --topic <topic> --messages 1000000 --consumer.config client.properties
```

#### Configuration Files

**client.properties** (for Kafka CLI tools):
```properties
bootstrap.servers=<bootstrap-server>:9092
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username='<api-key>' password='<api-secret>';
```

**Producer configuration** (application):
```properties
bootstrap.servers=<bootstrap>:9092
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username='<key>' password='<secret>';

# Multi-AZ optimization
acks=all
enable.idempotence=true
linger.ms=10
batch.size=32768
compression.type=snappy
request.timeout.ms=30000
```

**Consumer configuration** (application):
```properties
bootstrap.servers=<bootstrap>:9092
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username='<key>' password='<secret>';

# Multi-AZ reliability
group.id=<consumer-group>
auto.offset.reset=earliest
enable.auto.commit=false
session.timeout.ms=45000
heartbeat.interval.ms=15000
```

### Validation Checklists

**Pre-Cutover Validation** (T-1 hour):
- [ ] Mirror lag < 100 messages ALL topics
- [ ] Consumer offset sync lag < 30 seconds
- [ ] Network connectivity validated (telnet test)
- [ ] API keys tested on destination
- [ ] All deployment configs updated and tested in staging
- [ ] Rollback plan reviewed and ready
- [ ] Monitoring dashboards prepared
- [ ] Team on bridge

**Post-Cutover Validation** (T+30 min):
- [ ] Producers writing to destination (metrics confirm)
- [ ] Consumers reading from destination (lag decreasing)
- [ ] Kafka Connect connectors RUNNING
- [ ] No application errors in logs
- [ ] Consumer lag healthy (<5000 messages)
- [ ] End-to-end latency within SLA
- [ ] Message count validation (source vs. destination)

**Decommission Readiness** (Week 10):
- [ ] 7-14 days stable operation on destination
- [ ] Zero critical incidents
- [ ] Business validation complete
- [ ] Performance meets baseline
- [ ] No traffic on source cluster (verified via metrics)

---

**END OF ROLE-BASED VIEW**
