# Dedicated to Enterprise Migration
## Complete Step-by-Step Guide

**Confluent Cloud Platform Migration**

---

## Agenda

1. Migration Overview
2. Step 1: Discovery & Sizing
3. Step 2: Provision Enterprise Cluster
4. Step 3: Establish Cluster Linking
5. Step 4: Migrate Applications (Pilot)
6. Step 5: Full Application Migration
7. Step 6: Validation & Monitoring
8. Step 7: Decommission Dedicated
9. Timeline & Resource Requirements
10. Risk Mitigation & Rollback

---

## Migration Overview

### The Big Picture

**7-Step Migration Process:**
1. ✅ Measure current usage
2. ✅ Build Enterprise cluster
3. ✅ Connect clusters (Cluster Linking)
4. ✅ Test with pilot application
5. ✅ Migrate all applications
6. ✅ Validate everything works
7. ✅ Cleanup and decommission

**Duration:** 4-11 weeks  
**Downtime:** ZERO  
**Rollback:** Available at any stage

---

## Migration Principles

### Zero-Downtime Guarantee

**How we achieve zero downtime:**
- Dedicated cluster continues running
- Enterprise cluster built in parallel
- Cluster Linking syncs data in real-time
- Applications migrate gradually
- Rollback available until final cleanup

**Key Technology: Cluster Linking**
- Real-time data synchronization
- Automatic failover capability
- Consumer offset preservation

---

## STEP 1: Discovery & Sizing
### Week 1-2

---

## Step 1.1: Gather Current Metrics

### What to Collect

**From Confluent Cloud Console → Metrics:**

| Metric | Purpose |
|--------|---------|
| Peak Ingress/Egress | Maximum throughput capacity needed |
| Average Ingress/Egress | Baseline capacity for sizing |
| Partition Count | Critical for Enterprise sizing |
| Storage (GB) | Retention costs |
| Current CKUs | Comparison baseline |
| Cluster Load | Utilization patterns |

**Time Period:** Last 90 days minimum

---

## Step 1.2: Analyze Workload Patterns

### Spike Analysis

**Questions to Answer:**

✅ Are there daily spikes? (Business hours vs. night)  
✅ Are there weekly spikes? (Monday mornings, Friday EOD)  
✅ How big are spikes? (2x? 5x? 10x average?)  
✅ How long do spikes last? (Minutes? Hours?)  

**Example Analysis:**
```
Normal traffic: 30 MB/sec ingress
Peak traffic: 90 MB/sec ingress (3x spike)
Spike frequency: Daily, 9 AM - 6 PM (9 hours/day)
Spike duration: 9 hours per day

Recommendation: Enterprise ideal (auto-scaling benefit)
```

---

## Step 1.3: Count Partitions

### Critical for Sizing!

**Why Partitions Matter:**
- Dedicated: ~4,500 partitions per CKU
- Enterprise: ~3,000 partitions per eCKU
- **Enterprise has 1,500 FEWER partitions per unit!**

**How to Count:**
```bash
confluent kafka topic list --cluster <cluster-id>
# Sum all partition counts across topics
```

**Example:**
```
100 topics × average 20 partitions = 2,000 total partitions
Enterprise needs: 2,000 ÷ 3,000 = 0.67 eCKU (round up to 1)
But 99.99% SLA requires minimum 2 eCKU
Final: 2 eCKU baseline
```

---

## Step 1.4: Size Enterprise Cluster

### Sizing Formula

**Calculate BOTH dimensions:**

**A. Throughput-Based:**
```
eCKU needed = Average Ingress ÷ 50 MB/sec per eCKU
(or)
eCKU needed = Average Egress ÷ 150 MB/sec per eCKU
```

**B. Partition-Based:**
```
eCKU needed = Total Partitions ÷ 3,000 per eCKU
```

**C. SLA Minimum:**
```
99.9% SLA = 1 eCKU minimum
99.99% SLA = 2 eCKU minimum
```

**Final Size = MAX(throughput, partitions, SLA minimum)**

---

## Step 1.5: Cost Estimation

### Build Business Case

**Use Confluent Pricing Calculator:**
- Input: Cloud provider, region, eCKU, SLA
- Output: Monthly cost estimate

**Example Calculation:**
```
Enterprise Cost Estimate:
- 2 eCKU baseline @ 99.99%: $2,800/month
- Auto-scaling (estimated): $400/month
- Storage (100 GB): $10/month
- Networking: $150/month
─────────────────────────────
Total: $3,360/month

Current Dedicated: $4,500/month
Monthly Savings: $1,140 (25%)
Annual Savings: $13,680
```

---

## Step 1.6: Calculate ROI

### Include Overlap Costs

**One-Time Migration Costs:**
```
Overlap period (4 weeks paying for both):
- Dedicated: $4,500
- Enterprise: $3,360
─────────────────────────────
Total overlap: $7,860 one-time
```

**Payback Calculation:**
```
Payback = Overlap Cost ÷ Monthly Savings
        = $7,860 ÷ $1,140
        = 6.9 months

Year 1 Net Savings:
($1,140 × 12 months) - $7,860 = $5,820
```

---

## Step 1 Deliverables

### Documentation to Create

**Migration Inventory Document:**
- Current cluster configuration
- Application list (producers, consumers)
- Connector inventory
- Flink job inventory
- Topic list with partition counts
- Current monthly costs
- Estimated Enterprise costs
- ROI calculation

**Stakeholder Presentation:**
- Business case for migration
- Cost savings analysis
- Timeline estimate
- Risk assessment

---

## STEP 2: Provision Enterprise Cluster
### Week 2

---

## Step 2.1: Create Enterprise Cluster

### Using Confluent Cloud UI

**Process:**
1. Log into Confluent Cloud
2. Click "Add Cluster"
3. Select "Enterprise" type
4. **Configure:**
   - Cloud: AWS/Azure/GCP
   - Region: SAME as Dedicated
   - Availability: Multi-zone (99.99% SLA)
   - eCKU: Your calculated baseline
5. Launch cluster (15-30 min provisioning)

**Critical:** Choose SAME region as Dedicated for Cluster Linking!

---

## Step 2.2: Using Terraform (Recommended)

### Infrastructure as Code

**Benefits:**
- Repeatable deployments
- Version controlled
- Automated provisioning
- Easy to replicate across environments

**Terraform Configuration:**
```hcl
resource "confluent_kafka_cluster" "enterprise" {
  display_name = "enterprise-prod"
  availability = "MULTI_ZONE"
  cloud        = "AWS"
  region       = "us-east-1"
  
  enterprise {
    ecku = 2
  }
  
  environment {
    id = confluent_environment.prod.id
  }
}
```

---

## Step 2.3: Set Up Private Networking

### Private Link Configuration

**Why Private Link:**
- Secure private connectivity
- No internet exposure
- Reduced networking costs (especially with PNI on AWS)
- AWS/Azure/GCP best practice

**Steps:**
1. Create Gateway in Confluent Cloud
2. Create Access Point
3. Create VPC Endpoint in your cloud provider
4. Accept connection in Confluent
5. Configure DNS (private hosted zones)

---

## Private Link Architecture

### Enterprise Networking Model

```
Your VPC
   │
   ├─ Private Link Connection
   │
   └─ Ingress Gateway
       │
       ├─ Access Point (Kafka)
       │   └─ pkc-xxxxx.region.aws.confluent.cloud:9092
       │
       ├─ Access Point (Schema Registry)
       │   └─ psrc-xxxxx.region.aws.confluent.cloud
       │
       └─ Access Point (Flink)
           └─ flink-xxxxx.region.aws.confluent.cloud
```

**Note:** Each service has dedicated endpoint (different from Dedicated)

---

## Step 2.4: Configure DNS

### Critical for Application Connectivity

**Create Private Hosted Zone:**

**AWS Route 53 Example:**
```
1. Create hosted zone for confluent.cloud
2. Associate with your VPC
3. Add A records for each endpoint:
   - Kafka endpoint
   - Schema Registry endpoint
   - Flink endpoint (if used)
```

**Test Resolution:**
```bash
nslookup pkc-xxxxx.us-east-1.aws.confluent.cloud
# Should resolve to private IP (10.x.x.x)
```

---

## Step 2.5: Create Service Accounts

### Security Configuration

**Option 1: Reuse Existing (Recommended)**
```bash
# Grant existing service accounts access to Enterprise
confluent kafka acl create \
  --cluster <enterprise-cluster-id> \
  --allow \
  --service-account <existing-sa-id> \
  --operation WRITE --operation READ \
  --topic '*' \
  --consumer-group '*'
```

**Option 2: Create New**
```bash
# Create new service account for Enterprise
confluent iam service-account create app-producer \
  --description "Producer for Enterprise"

# Generate API key
confluent api-key create \
  --service-account <sa-id> \
  --resource <enterprise-cluster-id>
```

---

## Step 2.6: Configure Schema Registry

### Schema Management Setup

**Enable Schema Registry:**
```bash
confluent schema-registry cluster enable \
  --cloud aws \
  --geo us \
  --environment <env-id>
```

**Get Endpoint:**
```
Output: https://psrc-xxxxx.us-east-1.aws.confluent.cloud
```

**Create API Key:**
```bash
confluent api-key create \
  --resource <schema-registry-id> \
  --service-account <sa-id>
```

---

## Step 2.7: Validate Enterprise Cluster

### Connectivity Testing

**Test Kafka Connection:**
```bash
kcat -b pkc-xxxxx.us-east-1.aws.confluent.cloud:9092 \
  -X security.protocol=SASL_SSL \
  -X sasl.mechanisms=PLAIN \
  -X sasl.username=<api-key> \
  -X sasl.password=<api-secret> \
  -L
```

**Create Test Topic:**
```bash
confluent kafka topic create test-migration \
  --cluster <enterprise-cluster-id> \
  --partitions 3
```

**Produce Test Message:**
```bash
echo "Migration test successful" | kcat ... -t test-migration -P
```

**Consume Test Message:**
```bash
kcat ... -t test-migration -C
```

✅ **If message received → Cluster ready!**

---

## Step 2 Deliverables

### What You Should Have

**Infrastructure:**
- ✅ Enterprise cluster running
- ✅ Private Link configured and tested
- ✅ DNS configured and validated
- ✅ Service accounts created with permissions
- ✅ Schema Registry enabled

**Documentation:**
- Enterprise cluster details (ID, endpoints)
- Service account credentials (securely stored)
- Network configuration (VPC endpoints, DNS zones)
- Terraform code (if used)

**Validation:**
- Connectivity test results
- Test message produce/consume successful

---

## STEP 3: Establish Cluster Linking
### Week 2-3

---

## Step 3.1: Understand Cluster Linking

### The Magic Behind Zero-Downtime

**What is Cluster Linking:**
- Real-time data synchronization between clusters
- Native Kafka replication protocol
- Preserves topic data, schemas, and consumer offsets
- Enables zero-downtime migration

**How It Works:**
```
Dedicated Cluster (Source)
   │
   │ ← Cluster Link (continuous sync)
   │
   ↓
Enterprise Cluster (Destination)
   └─ Mirror Topics (read-only copies)
```

---

## Step 3.2: Create Cluster Link

### Configuration

**Important:** Create link FROM Enterprise (destination) TO Dedicated (source)

**Via Confluent Cloud UI:**
1. Navigate to Enterprise Cluster → Cluster Linking
2. Click "Create cluster link"
3. Source cluster: Select Dedicated
4. Link name: `dedicated-to-enterprise`
5. Enable consumer offset sync ✅
6. Select topics to mirror (or "All topics")

**Via CLI:**
```bash
confluent kafka link create dedicated-to-enterprise \
  --source-cluster <dedicated-cluster-id> \
  --destination-cluster <enterprise-cluster-id> \
  --source-api-key <dedicated-key> \
  --source-api-secret <dedicated-secret> \
  --config consumer.offset.sync.enable=true
```

---

## Step 3.3: Create Mirror Topics

### Topic Synchronization

**Mirror All Topics:**
```bash
# Get topic list from Dedicated
confluent kafka topic list --cluster <dedicated-id>

# Create mirrors on Enterprise
confluent kafka topic list --cluster <dedicated-id> | \
  while read topic; do
    confluent kafka mirror create $topic \
      --cluster <enterprise-cluster-id> \
      --link dedicated-to-enterprise \
      --source-topic $topic
  done
```

**Or Mirror Specific Topics:**
```bash
confluent kafka mirror create orders \
  --cluster <enterprise-cluster-id> \
  --link dedicated-to-enterprise \
  --source-topic orders
```

---

## Step 3.4: Monitor Cluster Link Lag

### Key Metric: Mirror Lag

**What is Mirror Lag:**
- Number of messages Enterprise is behind Dedicated
- Goal: Lag → 0 before migrating applications

**Check Lag:**
```bash
confluent kafka link describe dedicated-to-enterprise \
  --cluster <enterprise-cluster-id>

confluent kafka mirror list \
  --cluster <enterprise-cluster-id> \
  --link dedicated-to-enterprise
```

**Typical Backfill Timeline:**
- Small cluster (100 GB): 1-4 hours
- Medium cluster (1 TB): 4-12 hours
- Large cluster (10+ TB): 1-3 days

**Monitor in UI:** Enterprise Cluster → Cluster Linking → View lag metric

---

## Cluster Link Status Progression

### What to Expect

**Phase 1: Initial Backfill (Hours to Days)**
```
Mirror Lag: 5,000,000 messages
State: ACTIVE
Status: Syncing historical data
```

**Phase 2: Catching Up**
```
Mirror Lag: 500,000 messages (decreasing)
State: ACTIVE
Status: Almost synced
```

**Phase 3: Fully Synced (Ready!)**
```
Mirror Lag: 0-100 messages (near zero)
State: ACTIVE
Status: Ready for migration ✅
```

**Only proceed to Step 4 when lag is near zero!**

---

## Step 3.5: Verify Consumer Offset Sync

### Critical for Seamless Cutover

**Why Consumer Offsets Matter:**
- Tell consumers "where they left off reading"
- Prevent duplicate processing or data loss
- Enable resumption on Enterprise from exact position

**Check Offset Sync:**
```bash
# List consumer groups on Dedicated
confluent kafka consumer group list --cluster <dedicated-id>

# Verify offsets synced to Enterprise
confluent kafka consumer group describe payment-processor \
  --cluster <enterprise-cluster-id>
```

**Expected Output:**
```
GROUP            TOPIC      PARTITION  OFFSET
payment-processor orders    0          125,430
payment-processor orders    1          124,980
payment-processor orders    2          125,100
```

✅ **If offsets present on Enterprise → Consumers can resume seamlessly!**

---

## Step 3 Deliverables

### What You Should Have

**Cluster Link:**
- ✅ Link created and ACTIVE
- ✅ All topics mirrored
- ✅ Mirror lag near zero
- ✅ Consumer offsets synced

**Monitoring:**
- Dashboard showing mirror lag trends
- Alerts configured for link failures
- Regular lag checks documented

**Readiness:**
- Enterprise cluster has complete copy of Dedicated data
- Ready to begin application migration

---

## STEP 4: Pilot Migration
### Week 3 (1-3 days)

---

## Step 4.1: Select Pilot Application

### Choosing the Right Test Case

**Ideal Pilot Characteristics:**

✅ **Non-critical** - Low business impact if issues occur  
✅ **Simple** - Minimal dependencies, straightforward logic  
✅ **Representative** - Similar to other apps (validates process)  
✅ **Observable** - Easy to monitor (good metrics, clear logs)  
✅ **Small scale** - Limited traffic volume  

**Good Examples:**
- Analytics consumer reading user events
- Internal monitoring dashboard
- Dev/test application

**Bad Examples:**
- Payment processing (too critical!)
- Complex multi-service app (too many dependencies)
- Legacy app with poor documentation

---

## Step 4.2: Stop Application on Dedicated

### Graceful Shutdown

**For Consumer Applications:**
```bash
# SSH to application server
ssh app-server-01

# Stop service gracefully
sudo systemctl stop analytics-consumer

# Verify stopped
sudo systemctl status analytics-consumer
```

**For Containerized Apps (Kubernetes):**
```bash
kubectl scale deployment analytics-consumer --replicas=0

# Verify pods terminated
kubectl get pods -l app=analytics-consumer
```

**Important:** Don't stop producers yet! Only consumers in pilot.

---

## Step 4.3: Update Application Configuration

### Configuration Changes Required

**OLD Configuration (Dedicated):**
```properties
# application.properties
bootstrap.servers=pkc-old.us-east-1.aws.confluent.cloud:9092
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.username=DEDICATED_API_KEY
sasl.password=DEDICATED_API_SECRET

schema.registry.url=https://psrc-old.us-east-1.aws.confluent.cloud
schema.registry.basic.auth.user.info=SR_KEY:SR_SECRET

group.id=analytics-consumer-group
```

**NEW Configuration (Enterprise):**
```properties
# application.properties
bootstrap.servers=pkc-xxxxx.us-east-1.aws.confluent.cloud:9092  ← Changed
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.username=ENTERPRISE_API_KEY                                ← Changed
sasl.password=ENTERPRISE_API_SECRET                             ← Changed

schema.registry.url=https://psrc-xxxxx.us-east-1.aws.confluent.cloud  ← Changed
schema.registry.basic.auth.user.info=SR_KEY:SR_SECRET

group.id=analytics-consumer-group  ← SAME (important!)
```

---

## Configuration Update Methods

### How to Update Configs

**Method 1: Configuration Files**
```bash
# Edit application.properties
vi /etc/analytics-consumer/application.properties
# Update endpoints and credentials
```

**Method 2: Environment Variables**
```bash
# Update environment file
vi /etc/default/analytics-consumer

KAFKA_BOOTSTRAP_SERVERS=pkc-xxxxx.us-east-1.aws.confluent.cloud:9092
KAFKA_API_KEY=<enterprise-key>
KAFKA_API_SECRET=<enterprise-secret>
```

**Method 3: Secrets Manager (Recommended)**
```bash
# AWS Secrets Manager example
aws secretsmanager update-secret \
  --secret-id analytics-consumer/kafka \
  --secret-string '{
    "bootstrap": "pkc-xxxxx.us-east-1.aws.confluent.cloud:9092",
    "api_key": "<enterprise-key>",
    "api_secret": "<enterprise-secret>"
  }'
```

---

## Step 4.4: Start Application on Enterprise

### Launch and Monitor

**Start Application:**
```bash
# Start service
sudo systemctl start analytics-consumer

# Check status
sudo systemctl status analytics-consumer
```

**Watch Logs Carefully:**
```bash
# Tail application logs
sudo journalctl -u analytics-consumer -f

# Look for:
✅ "Successfully connected to Kafka"
✅ "Subscribed to topics: [user-events]"
✅ "Consumer group rebalanced"
✅ "Fetching records from partition..."

❌ "Connection refused"
❌ "Authentication failed"
❌ "Topic not found"
```

---

## Step 4.5: Validate Pilot Success

### Multi-Layer Validation

**Level 1: Technical Validation**
```bash
# Check consumer group lag on Enterprise
confluent kafka consumer group describe analytics-consumer-group \
  --cluster <enterprise-cluster-id>

Expected Output:
TOPIC        PARTITION  CURRENT-OFFSET  LAG
user-events  0          45,230          0      ← Lag should be 0
user-events  1          43,100          0
user-events  2          44,980          0
```

**Level 2: Application Validation**
```bash
# Check application metrics
curl http://localhost:8080/metrics | grep messages_processed

Expected: Counter increasing steadily
```

**Level 3: Business Validation**
```
# Verify business outcomes
- Dashboard shows data? ✅
- Alerts working? ✅
- Reports generating? ✅
```

---

## Pilot Validation Checklist

### Before Declaring Success

- [ ] Application started without errors
- [ ] Connected to Enterprise cluster (check logs)
- [ ] Consumer lag = 0 or decreasing
- [ ] No authentication/authorization errors
- [ ] Schema Registry working (if used)
- [ ] Application processing messages correctly
- [ ] Business metrics unchanged
- [ ] No duplicate message processing
- [ ] Downstream systems receiving data
- [ ] Ran for 24-48 hours without issues
- [ ] Stakeholders confirm "looks normal"

**Only proceed to Step 5 when ALL checks pass!**

---

## Step 4.6: Pilot Failure - Rollback

### If Pilot Has Issues

**Rollback Procedure (Takes Minutes):**

1. **Stop application on Enterprise**
   ```bash
   sudo systemctl stop analytics-consumer
   ```

2. **Revert configuration to Dedicated**
   ```bash
   # Restore old config
   cp /etc/analytics-consumer/application.properties.backup \
      /etc/analytics-consumer/application.properties
   ```

3. **Restart on Dedicated**
   ```bash
   sudo systemctl start analytics-consumer
   ```

4. **Verify working**
   ```bash
   # Check consuming from Dedicated again
   confluent kafka consumer group describe analytics-consumer-group \
     --cluster <dedicated-cluster-id>
   ```

**Data Safety:** Dedicated has all data - nothing lost!

---

## Pilot Troubleshooting Guide

### Common Issues and Fixes

**Issue 1: Connection Refused**
```
Error: "Failed to connect to pkc-xxxxx:9092"

Fixes:
✅ Check DNS resolution (nslookup endpoint)
✅ Verify Private Link endpoint created
✅ Check security groups allow port 9092
✅ Verify VPC routing to Private Link
```

**Issue 2: Authentication Failed**
```
Error: "SASL authentication failed"

Fixes:
✅ Verify API key is for Enterprise (not Dedicated)
✅ Check API key has correct permissions (ACLs)
✅ Ensure no typos in credentials
✅ Verify service account not deleted
```

**Issue 3: Topic Not Found**
```
Error: "Topic 'user-events' not found"

Fixes:
✅ Verify Cluster Link created mirror topic
✅ Check mirror topic name matches (case-sensitive)
✅ Ensure Cluster Link lag reached zero
✅ Verify topic permissions (ACLs)
```

---

## Step 4 Deliverables

### What You Should Have

**Successful Pilot:**
- ✅ One application migrated and validated
- ✅ Rollback procedure tested (if issues occurred)
- ✅ Configuration update process documented
- ✅ Validation checklist completed

**Documentation:**
- Pilot application migration runbook
- Troubleshooting notes (issues encountered and fixes)
- Configuration templates for other apps
- Validation criteria for future migrations

**Lessons Learned:**
- What worked well
- What needed adjustment
- Estimated time per application
- Skills/tools needed

---

## STEP 5: Full Application Migration
### Week 3-6 (2-4 weeks)

---

## Step 5.1: Plan Migration Waves

### Phased Migration Strategy

**Wave 1: Low-Risk Consumers (Week 3)**
- Analytics consumers
- Monitoring/metrics consumers
- Internal tooling
- Dev/test applications
- **Risk Level:** LOW

**Wave 2: Business-Critical Consumers (Week 4)**
- Customer-facing applications
- High-volume consumers
- Real-time processing apps
- **Risk Level:** MEDIUM

**Wave 3: Producers (Week 5)**
- All producer applications
- Only after consumers validated
- **Risk Level:** MEDIUM-HIGH

**Wave 4: Final Cutover (Week 5-6)**
- Promote mirror topics to writable
- Cleanup and validation
- **Risk Level:** HIGH

---

## Migration Waves - Visual Timeline

### 4-Week Execution Plan

```
Week 3: Wave 1 (Low-Risk Consumers)
├─ Day 1-2: Migrate analytics apps (5 apps)
├─ Day 3-4: Migrate monitoring apps (8 apps)
└─ Day 5: Validate, monitor

Week 4: Wave 2 (Critical Consumers)
├─ Day 1-2: Migrate customer apps (10 apps)
├─ Day 3-4: Migrate processing apps (7 apps)
└─ Day 5: Validate, monitor

Week 5: Wave 3 (Producers)
├─ Day 1: Stop all producers
├─ Day 2: Verify Cluster Link lag = 0
├─ Day 3: Promote topics to writable
├─ Day 4: Migrate producers (15 apps)
└─ Day 5: Validate end-to-end

Week 6: Final Validation
├─ Monitor all applications
├─ Verify business metrics
├─ Cost validation
└─ Stakeholder sign-off
```

---

## Step 5.2: Migrate Consumers

### Process for Each Consumer

**Pre-Migration:**
```
1. Identify all instances (servers, pods)
2. Backup current configuration
3. Update configuration for Enterprise
4. Update secrets/credentials
5. Schedule maintenance window (if needed)
```

**Migration:**
```
1. Stop consumer on Dedicated
2. Deploy updated config
3. Start consumer on Enterprise
4. Validate consuming (check lag)
5. Monitor for 24 hours
```

**Post-Migration:**
```
1. Confirm consumer lag stable
2. Verify business metrics
3. Document any issues
4. Mark application as "migrated"
```

---

## Step 5.3: Consumer Migration Checklist

### Per-Application Checklist

**Application: [Name]**

**Pre-Migration:**
- [ ] Application owner notified
- [ ] Configuration files updated
- [ ] Credentials updated in secrets manager
- [ ] Deployment scripts tested
- [ ] Rollback plan documented
- [ ] Monitoring alerts configured

**Migration:**
- [ ] Application stopped on Dedicated
- [ ] Configuration deployed
- [ ] Application started on Enterprise
- [ ] Logs reviewed (no errors)
- [ ] Consumer lag checked (lag = 0)

**Post-Migration:**
- [ ] 24-hour monitoring complete
- [ ] Business metrics validated
- [ ] Stakeholder confirmation received
- [ ] Documentation updated

---

## Step 5.4: Promote Mirror Topics

### Making Topics Writable

**When to Promote:**
- ✅ ALL consumers migrated and validated
- ✅ Cluster Link lag = 0
- ✅ Ready to migrate producers

**What "Promote" Means:**
- Mirror topics become independent (no longer read-only)
- Topics accept writes on Enterprise
- Topics stop syncing from Dedicated
- **WARNING: Dedicated and Enterprise will DIVERGE after this!**

**How to Promote:**
```bash
# Promote single topic
confluent kafka mirror promote orders \
  --cluster <enterprise-cluster-id> \
  --link dedicated-to-enterprise

# Promote ALL topics
confluent kafka topic list --cluster <enterprise-cluster-id> | \
  while read topic; do
    confluent kafka mirror promote $topic \
      --cluster <enterprise-cluster-id> \
      --link dedicated-to-enterprise
  done
```

---

## Step 5.5: Migrate Producers

### Final Application Cutover

**Pre-Migration:**
```
1. Ensure ALL consumers migrated ✅
2. Ensure topics promoted ✅
3. Coordinate with all producer teams
4. Schedule coordinated maintenance window
```

**Migration Process:**

**Step 1: Stop ALL Producers on Dedicated**
```bash
# Stop all producer services
sudo systemctl stop order-service
sudo systemctl stop payment-service
sudo systemctl stop inventory-service
# ... all producers
```

**Step 2: Verify Cluster Link Lag = 0**
```bash
confluent kafka link describe dedicated-to-enterprise \
  --cluster <enterprise-cluster-id>

# Should show: Mirror Lag: 0 messages
```

---

## Producer Migration (Continued)

### Cutover Execution

**Step 3: Promote Topics (if not done in Step 5.4)**
```bash
# Make all topics writable on Enterprise
confluent kafka mirror promote <topic> ...
```

**Step 4: Update Producer Configs**
```properties
# OLD (Dedicated)
bootstrap.servers=pkc-old.us-east-1.aws.confluent.cloud:9092

# NEW (Enterprise)
bootstrap.servers=pkc-xxxxx.us-east-1.aws.confluent.cloud:9092
```

**Step 5: Start Producers on Enterprise**
```bash
sudo systemctl start order-service
sudo systemctl start payment-service
sudo systemctl start inventory-service
```

**Step 6: Validate Producing**
```bash
# Check messages arriving on topics
kcat -b <enterprise-bootstrap> \
  -X security.protocol=SASL_SSL \
  -t orders -C -e -q | wc -l

# Watch count increase over time
```

---

## Step 5.6: Parallel Migration Strategy

### For Large Application Counts

**If you have 50+ applications:**

**Team-Based Parallelization:**
```
Team A: Migrate their 15 apps (Week 3)
Team B: Migrate their 20 apps (Week 3)
Team C: Migrate their 18 apps (Week 4)
Team D: Migrate their 12 apps (Week 4)

Result: 65 apps migrated in 2 weeks (vs. 13 weeks sequential)
```

**Automation:**
- Use configuration management (Ansible, Terraform)
- Template configuration updates
- Scripted deployment processes
- Automated validation checks

**Coordination:**
- Central migration team tracks progress
- Daily standups to share learnings
- Shared troubleshooting knowledge base
- Slack channel for real-time support

---

## Step 5 Deliverables

### What You Should Have

**Migrated Applications:**
- ✅ All consumers migrated and validated
- ✅ All producers migrated and validated
- ✅ Topics promoted to writable
- ✅ Enterprise is primary production cluster

**Status Tracking:**
- Migration status dashboard
- Application inventory (migrated vs. pending)
- Issues log and resolutions

**Documentation:**
- Updated runbooks per application
- Configuration templates
- Troubleshooting guide
- Lessons learned

**Readiness:**
- Ready for validation period
- Dedicated cluster running as backup
- Rollback procedure documented

---

## STEP 6: Validation & Monitoring
### Week 6-11 (1-4 weeks)

---

## Step 6.1: Daily Monitoring

### What to Watch

**Cluster Health Metrics:**
```
✅ eCKU Utilization (40-80% healthy)
✅ Auto-scaling events (appropriate?)
✅ Disk usage (not growing unexpectedly)
✅ Network throughput (matching expectations)
✅ Partition distribution (balanced)
✅ No cluster alerts or errors
```

**Application Health Metrics:**
```
✅ Consumer lag = 0 or stable
✅ No errors in application logs
✅ Application throughput normal
✅ Response times within SLA
✅ No monitoring alerts triggered
```

**Business Metrics:**
```
✅ Orders processed = unchanged
✅ Payments processed = unchanged
✅ Events published = unchanged
✅ Revenue/conversions = unchanged
```

---

## Step 6.2: Auto-Scaling Validation

### Verify Auto-Scaling Working Correctly

**Check Auto-Scaling Events:**
```
Confluent Cloud UI → Enterprise Cluster → Activity Log

Look for:
✅ "Scaled up to 3 eCKU" (during traffic spike)
✅ "Scaled down to 2 eCKU" (after spike)
```

**Validate Scaling Behavior:**
```
During Spike:
- Time to scale up: < 2 minutes ✅
- Sufficient capacity added ✅
- No throttling or errors ✅

After Spike:
- Time to scale down: 5-15 minutes ✅
- Scales back to baseline ✅
- No performance degradation ✅
```

**Review Scaling Costs:**
```
Baseline: 2 eCKU @ $2,800/month
Auto-scaling: 400 hours @ $3/hour = $1,200
Total: $4,000/month

vs. Estimated: $3,360/month
Variance: +19% (investigate if large)
```

---

## Step 6.3: Cost Validation

### Verify Savings Realized

**Compare Actual to Estimate:**
```
Estimated Enterprise: $3,360/month
Actual Enterprise (prorated): 
  - 20 days usage: $2,240
  - Projected full month: $3,360 ✅

vs. Dedicated: $4,500/month
Actual Savings: $1,140/month (25%) ✅
```

**Breakdown Analysis:**
```
Cost Component          Estimate    Actual    Variance
Baseline eCKU          $2,800      $2,800    0%
Auto-scaling           $400        $480      +20%
Storage                $10         $12       +20%
Networking             $150        $140      -7%
──────────────────────────────────────────────────
Total                  $3,360      $3,432    +2% ✅
```

**If Costs Higher than Expected:**
- Investigate auto-scaling frequency
- Review storage growth
- Check networking patterns
- Compare to Dedicated (still saving?)

---

## Step 6.4: Performance Validation

### Benchmark Comparison

**Latency Comparison:**
```
Dedicated (before):
- p50 latency: 12ms
- p95 latency: 25ms
- p99 latency: 45ms

Enterprise (after):
- p50 latency: 11ms ✅
- p95 latency: 23ms ✅
- p99 latency: 42ms ✅

Result: Performance maintained or improved
```

**Throughput Comparison:**
```
Dedicated: 30 MB/sec average, 90 MB/sec peak
Enterprise: 30 MB/sec average, 92 MB/sec peak

Result: Throughput unchanged ✅
```

**Load Testing (Optional but Recommended):**
```bash
# Simulate peak load
kafka-producer-perf-test \
  --topic load-test \
  --num-records 1000000 \
  --throughput 50000 \
  --record-size 1024 \
  --producer-props bootstrap.servers=<enterprise>

Result: 
- Enterprise handled peak without issues ✅
- Auto-scaled appropriately ✅
- Latency remained stable ✅
```

---

## Step 6.5: Stakeholder Validation

### Business Confirmation

**Weekly Check-ins:**
```
Week 1: 
✅ Engineering: No issues reported
✅ Product: Metrics look normal
✅ Finance: Costs tracking to estimate

Week 2:
✅ Engineering: One minor issue (resolved)
✅ Product: All features working
✅ Finance: Savings confirmed

Week 3-4:
✅ All teams: Confident in stability
✅ Ready to proceed with decommission
```

**Formal Sign-Off:**
```
Stakeholder Approval Checklist:
- [ ] Engineering Lead: Approves technical stability
- [ ] Product Manager: Confirms business metrics
- [ ] Finance: Validates cost savings
- [ ] Security: Confirms compliance maintained
- [ ] Executive Sponsor: Authorizes Dedicated cleanup
```

---

## Validation Period Durations

### How Long to Validate

**1 Week Validation:**
- Non-production environments
- Low-risk workloads
- Aggressive timeline

**2-3 Weeks Validation (Recommended):**
- Production environments
- Standard risk tolerance
- Covers multiple weekly cycles

**4+ Weeks Validation:**
- Mission-critical systems
- Conservative approach
- Covers monthly processes (month-end, quarter-end)
- Regulated industries

**Our Recommendation:**
```
Non-Prod: 1 week
Production: 3 weeks minimum
Critical: 4-6 weeks
```

---

## Step 6 Deliverables

### Validation Complete

**Metrics Reports:**
- Daily monitoring dashboards
- Weekly cost reports
- Performance comparison analysis
- Auto-scaling behavior summary

**Issue Tracking:**
- Issues encountered log
- Resolutions implemented
- Outstanding items (if any)

**Stakeholder Approval:**
- Sign-off from all stakeholders
- Authorization to decommission Dedicated
- Documentation of validation period

**Readiness:**
- Enterprise validated as stable production cluster
- Dedicated ready for decommission
- Team confident in migration success

---

## STEP 7: Decommission Dedicated
### Week 11+ (1 day)

---

## Step 7.1: Pre-Decommission Checklist

### Final Verifications

**Before You Delete Dedicated:**

- [ ] Enterprise running stably for 2-4+ weeks
- [ ] All applications migrated (Dedicated traffic = 0)
- [ ] All validation checks passed
- [ ] Stakeholder approval received
- [ ] Backup of configurations (optional paranoia)
- [ ] Rollback plan understood (can't rollback after deletion)
- [ ] Team ready to monitor Enterprise closely post-cleanup

**Check Dedicated Traffic:**
```bash
# Verify Dedicated has zero traffic
confluent kafka cluster describe <dedicated-cluster-id>

# Check metrics in UI
Dedicated Cluster → Metrics → Throughput
Should show: Ingress = 0, Egress = 0
```

**If ANY traffic on Dedicated → STOP! Investigate before deleting!**

---

## Step 7.2: Delete Cluster Link

### Remove Synchronization

**Why Delete Link First:**
- Cluster Link itself costs money
- No longer needed (Enterprise is independent)
- Clean separation before deleting Dedicated

**How to Delete:**
```bash
confluent kafka link delete dedicated-to-enterprise \
  --cluster <enterprise-cluster-id>
```

**Expected Result:**
- Link removed
- Mirror topics on Enterprise remain (but are now independent)
- No more sync from Dedicated

**Verify:**
```bash
confluent kafka link list --cluster <enterprise-cluster-id>
# Should show: No cluster links found
```

---

## Step 7.3: Delete Dedicated Cluster

### The Final Step

**Via Confluent Cloud UI:**

1. Navigate to Dedicated Cluster
2. Go to **Settings** → Scroll to bottom
3. Click **"Delete cluster"**
4. Type cluster name to confirm: `dedicated-prod`
5. Click **"Delete"**
6. Confirmation: "Cluster deletion initiated"

**Deletion Timeline:**
- Processing: 15-30 minutes
- Email notification when complete
- Billing stops immediately

---

## Delete Dedicated (CLI Method)

### Command-Line Approach

**Delete Cluster:**
```bash
confluent kafka cluster delete <dedicated-cluster-id> --force
```

**Expected Output:**
```
Cluster "lkc-xxxxx" scheduled for deletion.
This operation cannot be undone.
```

**Verify Deletion:**
```bash
confluent kafka cluster list

# Dedicated cluster should not appear in list
```

**Monitor Deletion Status:**
```
Confluent Cloud UI → Environments
Status: "Deleting..." → "Deleted"
```

---

## Step 7.4: Clean Up Networking

### Remove Old Connections

**Delete VPC Peering (if no longer needed):**
```bash
# AWS example
aws ec2 delete-vpc-peering-connection \
  --vpc-peering-connection-id pcx-xxxxx
```

**Delete Transit Gateway Attachments:**
```bash
aws ec2 delete-transit-gateway-vpc-attachment \
  --transit-gateway-attachment-id tgw-attach-xxxxx
```

**Remove DNS Entries:**
```bash
# Delete Route 53 records for Dedicated endpoints
aws route53 change-resource-record-sets \
  --hosted-zone-id Z123456 \
  --change-batch '{ "Changes": [...]}'
```

**Clean Up Security Groups (Optional):**
```bash
# Remove rules specific to Dedicated cluster
aws ec2 revoke-security-group-ingress \
  --group-id sg-xxxxx \
  --ip-permissions ...
```

---

## Step 7.5: Update Documentation

### Final Housekeeping

**Update Architecture Diagrams:**
- Remove Dedicated cluster
- Show only Enterprise cluster
- Update network flow diagrams

**Update Runbooks:**
- Application connection details (Enterprise endpoints)
- Monitoring procedures (Enterprise cluster)
- Incident response playbooks

**Update Configuration Management:**
- Remove Dedicated configs from Git
- Archive old Terraform code
- Update CI/CD pipelines

**Update Knowledge Base:**
- Migration retrospective
- Lessons learned
- Best practices for future migrations

---

## Step 7.6: Verify Cost Savings

### Confirm Billing Changes

**Next Month's Bill:**
```
Previous Month (Overlap Period):
- Dedicated: $4,500
- Enterprise: $3,360
- Total: $7,860 ❌

Current Month (Post-Migration):
- Dedicated: $0 ✅
- Enterprise: $3,360 ✅
- Total: $3,360 ✅

Monthly Savings: $1,140 (25%)
Annual Savings: $13,680
```

**Review Invoice:**
```
Confluent Cloud UI → Billing → Invoices
- Verify Dedicated charges stopped
- Verify Enterprise charges match estimate
- Confirm savings realized
```

---

## Step 7.7: Celebrate Success! 🎉

### Migration Complete!

**What You Achieved:**

✅ **Zero-downtime migration** - No service interruption  
✅ **Zero data loss** - All data migrated successfully  
✅ **Cost savings** - 20-60% monthly savings realized  
✅ **Auto-scaling** - No more manual capacity management  
✅ **Modern platform** - On Confluent's strategic platform  
✅ **Team learning** - Built migration expertise  

**Share Success:**
- Announce to organization
- Thank migration team and stakeholders
- Document case study
- Share lessons learned with peers

**Next Steps:**
- Monitor Enterprise ongoing
- Optimize costs as needed
- Plan future migrations (dev/test environments?)
- Enjoy the savings! 💰

---

## Step 7 Deliverables

### Final Documentation

**Completion Report:**
- Migration timeline (actual vs. planned)
- Cost savings achieved (actual vs. estimated)
- Issues encountered and resolutions
- Lessons learned and recommendations

**Updated Assets:**
- Architecture diagrams (Enterprise only)
- Runbooks and procedures
- Configuration management (Git, Terraform)
- Knowledge base articles

**Stakeholder Communication:**
- Success announcement
- Cost savings report
- Future optimization opportunities

**Cleanup Confirmation:**
- Dedicated cluster deleted ✅
- Networking cleaned up ✅
- Billing verified ✅

---

## Timeline Summary

### Complete Migration Journey

**Week-by-Week Breakdown:**

| Week | Activity | Status |
|------|----------|--------|
| **1-2** | Discovery & sizing | Dedicated runs normally |
| **2** | Provision Enterprise | Both clusters exist |
| **2-3** | Cluster Linking | Enterprise syncing |
| **3** | Pilot migration | Small traffic on Enterprise |
| **3-6** | Full migration | Most traffic on Enterprise |
| **6-11** | Validation | Enterprise is primary |
| **11+** | Decommission | Enterprise only ✅ |

**Total Duration:** 4-11 weeks (depends on complexity and risk tolerance)

---

## Resource Requirements

### Team and Skills Needed

**Core Migration Team (Recommended):**
- 1 Project Manager (planning, coordination, tracking)
- 2-3 Engineers (Kafka expertise, hands-on migration)
- 1 Network Engineer (Private Link setup, DNS)
- 1 DevOps Engineer (CI/CD, automation)

**Part-Time Contributors:**
- Application owners (config updates per app)
- Security team (compliance review, approvals)
- Finance team (cost tracking, approvals)

**Time Commitment:**
```
Project Manager: Full-time (8-12 weeks)
Engineers: 50-75% time (6-8 weeks)
Network Engineer: 25% time (2-3 weeks)
DevOps: 25% time (ongoing support)
App Owners: 10-20 hours each (1-2 weeks)
```

---

## Skills Required

### Technical Competencies

**Must Have:**
- ✅ Kafka fundamentals (topics, producers, consumers)
- ✅ Confluent Cloud console familiarity
- ✅ Basic networking (VPCs, DNS, Private Link)
- ✅ Application configuration management

**Nice to Have:**
- ✅ Terraform (for infrastructure-as-code)
- ✅ Kubernetes (if containerized apps)
- ✅ Monitoring tools (Prometheus, Grafana, Datadog)
- ✅ Scripting (Bash, Python for automation)

**Can Learn During Migration:**
- Cluster Linking (new to most teams)
- Enterprise cluster specifics
- Private Link networking model
- Auto-scaling behavior

**Confluent Support Available:**
- Professional Services (hands-on help)
- Technical Account Manager (guidance)
- Support tickets (troubleshooting)
- Documentation and training

---

## Risk Mitigation

### How We Reduce Risk

**Technical Risks:**

**Risk:** Data loss during migration  
**Mitigation:**
- ✅ Cluster Linking preserves all data
- ✅ Dedicated keeps running as source of truth
- ✅ Verify lag = 0 before cutover

**Risk:** Application connectivity failures  
**Mitigation:**
- ✅ Pilot migration validates process
- ✅ Test connectivity before migrating apps
- ✅ Rollback procedure documented and tested

**Risk:** Performance degradation  
**Mitigation:**
- ✅ Size Enterprise appropriately
- ✅ Load testing before production cutover
- ✅ Monitor performance closely during migration

---

## Risk Mitigation (Continued)

### More Risk Controls

**Business Risks:**

**Risk:** Extended downtime impacts revenue  
**Mitigation:**
- ✅ Zero-downtime migration strategy
- ✅ Phased migration (can pause if needed)
- ✅ Dedicated available for immediate rollback

**Risk:** Higher costs than expected  
**Mitigation:**
- ✅ Detailed cost estimate upfront
- ✅ Monitor costs during overlap period
- ✅ Can rollback if costs unfavorable

**Risk:** Missed business-critical processes  
**Mitigation:**
- ✅ Validate for full business cycle (month-end, quarter-end)
- ✅ Stakeholder review before decommission
- ✅ Keep Dedicated 2-4+ weeks for safety

---

## Rollback Strategy

### When and How to Rollback

**Rollback Scenarios:**
- ❌ Enterprise performance issues
- ❌ Critical application failures
- ❌ Data inconsistencies detected
- ❌ Network connectivity problems
- ❌ Costs significantly higher than expected

**Rollback Procedures by Stage:**

**Before Topic Promotion (Easy - Minutes):**
```
1. Stop apps on Enterprise
2. Revert config to Dedicated endpoints
3. Restart apps on Dedicated
✅ Data safe (Dedicated has everything)
```

**After Topic Promotion (Moderate - Hours):**
```
1. Create reverse Cluster Link (Enterprise → Dedicated)
2. Wait for sync
3. Promote topics on Dedicated
4. Point apps back to Dedicated
⚠️ Requires coordination, takes longer
```

---

## Rollback (Continued)

### Point of No Return

**After Dedicated Deleted (Cannot Rollback):**
```
❌ Dedicated is gone
❌ Can't go back to old cluster
✅ Must fix forward on Enterprise

Options if issues arise:
1. Troubleshoot and fix on Enterprise
2. Create new Dedicated if absolutely necessary (expensive, slow)
```

**Best Practice:**
- Don't delete Dedicated until 100% confident
- Cost of keeping Dedicated 2-4 extra weeks is small
- Insurance against costly mistakes

**Decision Framework:**
```
Can rollback? → Keep Dedicated
Cannot rollback? → Delete Dedicated only when certain
```

---

## Success Criteria

### How to Measure Success

**Technical Success:**
- ✅ Zero unplanned downtime
- ✅ Zero data loss
- ✅ All applications migrated
- ✅ Performance maintained or improved
- ✅ Auto-scaling working correctly

**Business Success:**
- ✅ Cost savings achieved (target: 20-60%)
- ✅ Business metrics unchanged
- ✅ Stakeholder satisfaction
- ✅ No customer impact

**Operational Success:**
- ✅ Team learned migration process
- ✅ Documentation complete
- ✅ Repeatable for other environments
- ✅ Reduced operational overhead (auto-scaling)

---

## Lessons Learned Template

### Post-Migration Review

**What Went Well:**
- Cluster Linking worked perfectly
- Pilot migration caught configuration issues early
- Team coordination was excellent
- Stakeholders well-informed

**What Could Be Improved:**
- DNS setup took longer than expected
- More automation needed for config updates
- Need better monitoring during migration

**Recommendations for Next Time:**
- Start with better networking documentation
- Build config templates earlier
- Allocate more time for validation

**Metrics:**
- Planned: 8 weeks | Actual: 9 weeks
- Estimated savings: 25% | Actual: 28%
- Issues: 3 minor | Downtime: 0 minutes

---

## Next Steps After Migration

### Optimization Opportunities

**Cost Optimization:**
- Review eCKU baseline (right-sized?)
- Optimize topic retention policies
- Clean up unused topics
- Leverage PNI for networking savings (AWS)

**Performance Optimization:**
- Tune producer/consumer configurations
- Review partition strategies
- Optimize Schema Registry usage
- Implement data compaction where appropriate

**Operational Improvements:**
- Set up advanced monitoring dashboards
- Create alerts for anomalies
- Document troubleshooting runbooks
- Train team on Enterprise features

**Future Migrations:**
- Apply learnings to dev/test environments
- Migrate other Dedicated clusters
- Evangelize success to other teams

---

## Appendix: Key Contacts

### Who to Contact for Help

**Confluent Support:**
- Support tickets: Confluent Cloud UI → Support
- P1 (Critical): <1 hour response
- P2 (High): <4 hours response

**Your Confluent Team:**
- Account Manager: [Name, Email]
- Solutions Architect: [Name, Email]
- Technical Account Manager: [Name, Email] (if applicable)

**Internal Team:**
- Project Manager: [Name]
- Lead Engineer: [Name]
- Network Team: [Contact]
- Security Team: [Contact]

**Emergency Contacts:**
- On-call rotation: [Phone, Slack]
- Escalation path: [Process]

---

## Appendix: Useful Commands

### Quick Reference

**Check Cluster Info:**
```bash
confluent kafka cluster describe <cluster-id>
```

**Monitor Consumer Lag:**
```bash
confluent kafka consumer group describe <group> \
  --cluster <cluster-id>
```

**Check Cluster Link Status:**
```bash
confluent kafka link describe <link-name> \
  --cluster <cluster-id>
```

**List Mirror Topics:**
```bash
confluent kafka mirror list \
  --cluster <cluster-id> \
  --link <link-name>
```

**Promote Mirror Topic:**
```bash
confluent kafka mirror promote <topic> \
  --cluster <cluster-id> \
  --link <link-name>
```

---

## Appendix: Terraform Examples

### Sample Code

**Enterprise Cluster:**
```hcl
resource "confluent_kafka_cluster" "enterprise" {
  display_name = "enterprise-prod"
  availability = "MULTI_ZONE"
  cloud        = "AWS"
  region       = "us-east-1"
  
  enterprise {
    ecku = 2
  }
  
  environment {
    id = confluent_environment.prod.id
  }
}
```

**Cluster Link:**
```hcl
resource "confluent_cluster_link" "main" {
  link_name = "dedicated-to-enterprise"
  
  source_kafka_cluster {
    id = var.dedicated_cluster_id
    credentials {
      key    = var.dedicated_api_key
      secret = var.dedicated_api_secret
    }
  }
  
  destination_kafka_cluster {
    id = confluent_kafka_cluster.enterprise.id
  }
  
  config = {
    "consumer.offset.sync.enable" = "true"
  }
}
```

---

## Q&A

### Questions?

**Common Questions:**

1. How long does the migration take?
2. Is there any downtime?
3. What if something goes wrong?
4. How much will we save?
5. Do we need Confluent help?

**Contact Information:**
- Email: [your-email@company.com]
- Slack: #kafka-migration
- Office Hours: Tuesdays 2-3 PM

---

## Thank You!

### Migration Success Awaits

**Key Takeaways:**
- ✅ Zero-downtime migration is possible
- ✅ Cluster Linking is the enabling technology
- ✅ Pilot testing reduces risk
- ✅ Phased approach is safest
- ✅ Savings typically 20-60%

**Next Steps:**
1. Review this presentation with your team
2. Schedule discovery session
3. Build business case
4. Get stakeholder buy-in
5. Plan migration timeline

**Let's get started!** 🚀

---

**END OF PRESENTATION**

**Document Version:** 1.0  
**Last Updated:** April 21, 2026  
**Prepared by:** [Your Name]  
**For:** Confluent Dedicated to Enterprise Migration
