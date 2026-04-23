# Frequently Asked Questions
## Confluent Dedicated to Enterprise Migration

**Version 1.0 | Last Updated: April 2026**

---

## TABLE OF CONTENTS

1. [General Questions](#general-questions)
2. [Technical Questions](#technical-questions)
3. [Migration Process Questions](#migration-process-questions)
4. [Cost & Pricing Questions](#cost--pricing-questions)
5. [Security & Compliance Questions](#security--compliance-questions)
6. [Performance Questions](#performance-questions)
7. [Support & Troubleshooting](#support--troubleshooting)

---

## GENERAL QUESTIONS

### Q1: What is the difference between Dedicated and Enterprise clusters?

**A:** Think of Dedicated as a rented dedicated server, and Enterprise as a serverless platform:

**Dedicated Clusters:**
- Fixed capacity that you pre-provision (CKUs)
- You manually scale up/down as needed
- Single-tenant infrastructure (your own servers)
- You pay for capacity 24/7, whether you use it or not

**Enterprise Clusters:**
- Auto-scaling capacity based on actual usage (eCKUs)
- Scales automatically in response to traffic
- Multi-tenant infrastructure (shared servers, isolated data)
- You pay for baseline + hourly charges when auto-scaling

**Key Benefit:** Enterprise eliminates manual scaling and reduces costs for most workloads.

---

### Q2: Why should I migrate from Dedicated to Enterprise?

**A:** Three main reasons:

1. **Cost Savings:** 20-60% lower costs for most workloads
2. **Auto-Scaling:** Automatic capacity adjustments for traffic spikes
3. **Networking Savings (AWS only):** Private Network Interface (PNI) reduces AWS data transfer charges

**Additional benefits:**
- No manual capacity planning
- Newer, more efficient infrastructure
- Strategic product direction (new features ship to Enterprise first)

---

### Q3: Is Enterprise right for every Dedicated customer?

**A:** No. Enterprise is ideal for:
- ✅ Spiky workloads (traffic varies throughout day/week)
- ✅ Over-provisioned Dedicated clusters (paying for unused capacity)
- ✅ AWS customers with high networking costs
- ✅ Customers comfortable with multi-tenant infrastructure

**Enterprise may NOT be ideal for:**
- ❌ Workloads requiring features Enterprise doesn't support yet
- ❌ Strict compliance policies against multi-tenancy
- ❌ Very partition-dense workloads near Enterprise limits
- ❌ Extremely steady workloads perfectly optimized on Dedicated

**Bottom line:** Run a cost analysis and feature comparison for your specific situation.

---

### Q4: What is Kafka? (For customers new to the technology)

**A:** Apache Kafka is a distributed event streaming platform. In simple terms:

**Think of it like a super-fast, reliable postal service for digital messages:**
- Applications send messages (events) to Kafka
- Kafka stores them reliably and in order
- Other applications read those messages in real-time

**Common use cases:**
- Real-time analytics (tracking user activity)
- Microservices communication (order service → payment service)
- Data pipelines (moving data from databases to data lakes)
- Event sourcing (recording every change that happens)

**Confluent** is the company founded by Kafka's creators that offers managed Kafka in the cloud.

---

### Q5: How long does a migration typically take?

**A:** End-to-end: **4-11 weeks** depending on complexity

**Timeline breakdown:**
- **Week 1-2:** Discovery, sizing, and Enterprise cluster provisioning
- **Week 2-3:** Establish Cluster Linking, data backfill
- **Week 3-4:** Pilot migration (test applications)
- **Week 4-6:** Full migration (all applications, connectors, Flink jobs)
- **Week 6-11:** Validation period before decommissioning Dedicated

**Factors that affect timeline:**
- Number of applications/consumers
- Number of connectors
- Complexity of Flink jobs (if any)
- Network team approval times for Private Link setup
- Internal change management processes

**Note:** The cutover itself can happen in hours - most time is preparation and validation.

---

## TECHNICAL QUESTIONS

### Q6: What is Cluster Linking and how does it enable zero-downtime migration?

**A:** Cluster Linking is a real-time data synchronization feature that mirrors topics from one Kafka cluster to another.

**How it works:**
1. You create a link from Dedicated (source) to Enterprise (destination)
2. Cluster Linking copies all existing data (backfill)
3. It then continuously mirrors new messages as they arrive
4. Your applications keep using Dedicated (no disruption)
5. When ready, you cut over applications to Enterprise
6. Cluster Linking ensures both clusters stay in sync until you're comfortable switching

**Analogy:** It's like having two synchronized whiteboards - whatever is written on one instantly appears on the other.

**Key benefit:** Your Dedicated cluster stays operational the entire time. If something goes wrong, you can instantly switch back.

---

### Q7: What are CKUs and eCKUs? How do they compare?

**A:** Both are units of capacity:

**CKU = Confluent Unit for Kafka (Dedicated)**
- Fixed capacity blocks
- Approximately 50 MBps ingress, 150 MBps egress per CKU
- ~4,500 partitions per CKU
- You pre-provision and pay for them 24/7

**eCKU = Enterprise Confluent Unit for Kafka (Enterprise)**
- Auto-scaling capacity blocks
- Same throughput limits as CKU (50 MBps ingress, 150 MBps egress)
- ~3,000 partitions per eCKU (1,500 fewer than Dedicated!)
- You set a minimum baseline and pay hourly when auto-scaling adds more

**Key difference:** CKUs are fixed, eCKUs scale dynamically.

**Important:** Partition limit difference means partition-heavy workloads may need more eCKUs.

---

### Q8: What networking changes are required?

**A:** Depends on your current setup:

**If using VPC Peering or Transit Gateway (TGW) on Dedicated:**
- ✅ Switch to **Private Link (PL)** or **Private Network Interface (PNI)** on Enterprise
- ✅ Update DNS records to use Access Point endpoints
- ✅ Configure VPC endpoints in your AWS/Azure/GCP account
- ✅ Update application connection URLs

**If using public internet:**
- ✅ Can continue using public endpoints OR switch to private networking

**Key changes:**
- **Dedicated:** One network connection, all services accessible through it
- **Enterprise:** Separate Access Points for Kafka, Flink, Schema Registry
- **Requires:** Network team to set up Private Link connections and DNS zones

---

### Q9: What features are not available in Enterprise?

**A:** As of April 2026, Enterprise does not support:

- ❌ **KSQL** (use Flink instead)
- ❌ Some advanced custom networking configurations
- ❌ Certain legacy connector versions
- ⚠️ Some features may be in preview/beta (check Confluent docs for current status)

**Where to check:** Confluent maintains a detailed feature comparison matrix:
- https://docs.confluent.io/cloud/current/clusters/cluster-types.html

**Before migrating:** Review this matrix against your current Dedicated usage to ensure no blockers.

---

### Q10: How does multi-tenancy work in Enterprise? Is my data isolated?

**A:** Multi-tenancy means multiple customers share underlying infrastructure, but data is completely isolated:

**What's SHARED:**
- Physical servers (VMs, containers)
- Networking infrastructure
- Storage systems

**What's ISOLATED:**
- Your data (encrypted and logically separated)
- Your encryption keys (you control)
- Your network access (private endpoints)
- Your access controls (RBAC, ACLs)

**Security layers:**
- Encryption at rest (using your keys or Confluent's)
- Encryption in transit (TLS)
- Network isolation (Private Link, no cross-tenant access)
- Access control (authentication, authorization per tenant)

**Analogy:** Like a secure apartment building - shared building utilities, but your apartment is private and locked.

**Compliance:** Enterprise is certified for SOC 2, ISO 27001, HIPAA, PCI-DSS, GDPR.

---

### Q11: What is an Access Point?

**A:** An Access Point is a dedicated network endpoint that provides private access from your VPC to a Confluent Enterprise environment.

**Think of it like:** A secure doorway from your network into Confluent's network

**Each Access Point provides:**
- Private connectivity to Kafka cluster
- Private connectivity to Schema Registry
- Private connectivity to Flink (if applicable)
- Unique DNS endpoints for each service

**Example DNS patterns:**
- Kafka: `pkc-xxxxx.us-east-1.aws.confluent.cloud:9092`
- Schema Registry: `psrc-xxxxx.us-east-1.aws.confluent.cloud`

**Why it matters:** You'll need to update application configs with new Access Point URLs when migrating.

---

### Q12: Can I migrate if I'm using Flink?

**A:** Yes, but with additional steps:

**Prerequisites:**
1. Verify Flink private networking is Generally Available in your cloud/region
2. Ensure Flink compute pools can reach Enterprise Kafka + Schema Registry over Private Link

**Migration process for Flink jobs:**
1. Duplicate Flink tables/jobs to point to Enterprise topics
2. Run in shadow mode (parallel execution) to validate results
3. Switch downstream consumers to Flink outputs from Enterprise
4. Decommission old Flink jobs on Dedicated

**Gotcha:** Flink networking is newer and may not be available in all regions yet - check with Confluent before committing to migration dates.

**KSQL users:** If using KSQL on Dedicated, you must migrate to Flink (KSQL not available on Enterprise).

---

### Q13: What happens to my connectors during migration?

**A:** Connectors need to be recreated on Enterprise and validated:

**Migration process:**

1. **Import/Document:** Export existing connector configs from Dedicated (use Terraform or UI)

2. **Recreate on Enterprise:** Create identical connectors pointing to Enterprise cluster

3. **Validate:**
   - **Source connectors:** Verify topics populate on Enterprise correctly
   - **Sink connectors:** Verify data flows to external systems (S3, databases, etc.)

4. **Cutover:** Once validated, delete Dedicated connectors

**Important:**
- **During migration:** You'll run duplicate connectors briefly (one on Dedicated, one on Enterprise)
- **Networking:** Connectors may need updated configs if switching from Peering to Private Link
- **Terraform users:** Use Confluent Terraform Provider to automate this

**Timeline:** Connector migration usually takes hours to days, depending on count and complexity.

---

## MIGRATION PROCESS QUESTIONS

### Q14: Can I migrate in phases or must I migrate everything at once?

**A:** You can (and should!) migrate in phases:

**Recommended approach:**

**Phase 1: Non-production environments**
- Migrate dev/test/staging clusters first
- Learn the process with low risk
- Validate cost assumptions

**Phase 2: Pilot production applications**
- Choose 1-2 non-critical applications
- Migrate using Cluster Linking
- Validate performance and functionality
- Keep Dedicated running as fallback

**Phase 3: Full production migration**
- Migrate remaining applications in waves
- Group by team, service domain, or risk level
- Keep cluster link active for rollback capability

**Phase 4: Decommission Dedicated**
- After 1-4 weeks of stable Enterprise operation
- Delete old cluster, save costs

**Benefits of phased approach:**
- Lower risk
- Faster learning curve
- Easier rollback if issues arise
- Spreads effort over time

---

### Q15: What if I need to rollback during migration?

**A:** Rollback is straightforward while Cluster Linking is active:

**Rollback procedure:**

1. **Stop applications** consuming from Enterprise
2. **Point applications back** to Dedicated bootstrap URLs
3. **Restart applications** - they resume from where they left off
4. **Investigate issue** that triggered rollback
5. **Fix and retry** when ready

**Rollback time:** Minutes (just config changes)

**Data safety:** Dedicated cluster has all data - nothing is lost

**When rollback is NOT possible:**
- After you've **deleted Dedicated cluster**
- After you've **promoted topics** and written new data ONLY to Enterprise (creates divergence)

**Best practice:**
- Keep Dedicated running 1-4 weeks after full cutover
- Don't delete until you're 100% confident in Enterprise
- Document rollback procedures BEFORE migration
- Test rollback during pilot phase

---

### Q16: Do I need to modify my application code?

**A:** Usually NO code changes required, only configuration changes:

**What DOESN'T change:**
- Producer API calls (same code)
- Consumer API calls (same code)
- Serialization/deserialization logic
- Business logic

**What DOES change:**
- **Bootstrap server URLs** (new Enterprise endpoint)
- **Schema Registry URLs** (new Access Point-specific endpoints)
- **Credentials** (if using new service accounts)
- **TLS/SSL settings** (if private networking setup differs)

**Where config changes happen:**
- Application configuration files (application.properties, YAML, etc.)
- Environment variables
- Secret management systems (Vault, AWS Secrets Manager, etc.)

**Exception:** If you have hard-coded URLs in source code (bad practice!), you'll need code changes.

**Best practice:** Use environment variables or config files for all Kafka connection parameters.

---

### Q17: How do I validate that the migration was successful?

**A:** Multi-layer validation:

**1. Cluster Linking Validation (during migration):**
- ✅ Link lag = 0 (clusters fully synchronized)
- ✅ Consumer offsets synced
- ✅ No mirroring errors in logs

**2. Application Validation (after cutover):**
- ✅ Producers successfully writing to Enterprise
- ✅ Consumers successfully reading from Enterprise
- ✅ Consumer lag stable (not growing)
- ✅ No connection errors in application logs
- ✅ Business metrics unchanged (orders processed, events counted, etc.)

**3. Connector Validation:**
- ✅ Source connectors: Data flowing into Enterprise topics
- ✅ Sink connectors: Data arriving in external systems (S3, databases)
- ✅ Connector status = RUNNING (no FAILED tasks)

**4. Flink Validation (if applicable):**
- ✅ Flink jobs running on Enterprise
- ✅ Output matches Dedicated baseline (shadow mode comparison)
- ✅ No job failures or restarts

**5. Infrastructure Validation:**
- ✅ eCKU utilization healthy (not maxed out, not way under-utilized)
- ✅ Private networking functional (Access Points reachable)
- ✅ DNS resolution working for all endpoints
- ✅ Monitoring and alerting configured

**6. Cost Validation:**
- ✅ Actual Enterprise costs match estimates
- ✅ No unexpected networking charges
- ✅ Auto-scaling behaving as expected

**Timeline:** Run validation for 1-4 weeks before decommissioning Dedicated.

---

### Q18: What tools do I need for migration?

**A:** Depends on your approach:

**Minimum (manual migration):**
- ✅ Confluent Cloud console (UI)
- ✅ Kafka CLI tools (kafka-topics, kafka-consumer-groups, etc.)
- ✅ Access to application config files

**Recommended (automated migration):**
- ✅ **Terraform** with Confluent Provider v2.60+ (infrastructure-as-code)
- ✅ **Git** for version control of configs
- ✅ **CI/CD pipeline** for config deployments
- ✅ **Monitoring tools** (Prometheus, Grafana, Datadog, etc.)

**Optional (helpful):**
- ✅ Confluent CLI (command-line management)
- ✅ Resource Importer (auto-generates Terraform from existing resources)
- ✅ Kafka lag monitoring (Burrow, Kafka Lag Exporter)
- ✅ Load testing tools (for validation)

**Confluent-provided:**
- ✅ Cluster Linking (built into Confluent Cloud)
- ✅ Metrics & monitoring (Confluent Cloud console)
- ✅ Professional Services (if you need hands-on help)

---

### Q19: Can Confluent help with the migration, or do I have to do it myself?

**A:** Multiple support options:

**1. Self-Service (documentation + support):**
- Confluent documentation and guides
- Community forums
- Standard support tickets
- **Good for:** Experienced Kafka teams, simple migrations

**2. Guided Migration (advisory support):**
- Technical Account Manager (TAM) guidance
- Architecture reviews and best practices
- Scheduled check-ins during migration
- **Good for:** Teams comfortable with Kafka but want expert oversight

**3. Professional Services (hands-on assistance):**
- Confluent engineers work with your team
- Hands-on migration execution
- Custom runbooks and automation
- **Good for:** Complex migrations, tight timelines, limited internal Kafka expertise

**Pricing:**
- Self-service: Included in your support plan
- Guided: May be included with TAM (depends on contract)
- Professional Services: Separate engagement (billable)

**Recommendation:** Start with discovery call to scope the right level of support for your situation.

---

## COST & PRICING QUESTIONS

### Q20: How much will Enterprise cost compared to my current Dedicated cluster?

**A:** Typically 20-60% less, but it depends on your workload:

**Cost factors:**
- **Baseline eCKU:** Minimum capacity (1 or 2 eCKU depending on SLA)
- **Auto-scaling usage:** How often and how much you spike above baseline
- **Storage:** Data retention volume
- **Networking:** Private Link, data transfer charges
- **SLA choice:** 99.9% or 99.99%

**Biggest savings for:**
- ✅ Over-provisioned Dedicated clusters
- ✅ Spiky workloads (auto-scaling saves money)
- ✅ AWS customers (PNI reduces networking costs)

**Smaller savings for:**
- Extremely steady workloads (no auto-scaling benefit)
- Already optimized, right-sized Dedicated clusters

**To get accurate estimate:**
1. Gather last 90 days of Dedicated usage metrics
2. Use Confluent pricing calculator
3. Compare to current monthly Dedicated spend
4. Factor in migration overlap costs

**Contact your Confluent account team for a custom estimate.**

---

### Q21: What is the cost during the migration overlap period?

**A:** You'll pay for both clusters during the overlap:

**Overlap period:** Typically 2-6 weeks

**Cost calculation:**
```
Overlap Cost = (Dedicated monthly cost + Enterprise monthly cost) × (overlap weeks ÷ 4.33)
```

**Example:**
- Dedicated: $5,000/month
- Enterprise: $3,000/month
- Overlap: 4 weeks (1 month)
- Overlap cost: ($5,000 + $3,000) × 1 = **$8,000 one-time**

**Payback period:**
```
Payback = Overlap cost ÷ Monthly savings
```

**Example:**
- Overlap cost: $8,000
- Monthly savings: $2,000
- Payback: $8,000 ÷ $2,000 = **4 months**

**After 4 months, you've recovered the overlap cost and everything after is pure savings.**

**How to minimize overlap costs:**
- Plan migration carefully (don't drag it out)
- Migrate non-production environments separately (faster, lower cost)
- Use pilot approach (validate quickly, commit to full migration)

---

### Q22: Are there any hidden costs I should know about?

**A:** No "hidden" costs, but some that are easy to overlook:

**1. Networking costs:**
- Private Link endpoint charges (AWS/Azure/GCP)
- Data transfer charges (especially cross-region)
- **Tip:** For AWS, use PNI to reduce these

**2. Storage costs:**
- Enterprise charges separately for storage retention
- $0.10/GB/month (example - check current pricing)
- **Tip:** Review retention policies, delete old data

**3. Auto-scaling costs:**
- You pay hourly when Enterprise scales above baseline eCKU
- Can be variable month-to-month depending on traffic
- **Tip:** Monitor eCKU usage to understand patterns

**4. Connector costs:**
- Managed connectors have separate pricing (same on Dedicated and Enterprise)
- **Tip:** Audit which connectors you actually need

**5. Schema Registry costs:**
- Usually included, but check your specific contract
- May have usage-based pricing in some cases

**6. Professional Services (if used):**
- Migration assistance is a separate billable engagement
- **Tip:** Get quote upfront

**7. Internal labor costs:**
- Your team's time for migration planning, execution, validation
- **Tip:** Factor this into ROI calculations

**Best practice:** Request a detailed cost breakdown from Confluent before committing.

---

### Q23: Does Enterprise have the same SLA as Dedicated?

**A:** Enterprise offers two SLA options:

**99.9% SLA (three nines):**
- Allows ~43 minutes downtime per month
- Minimum 1 eCKU
- Lower cost
- **Good for:** Non-production, dev/test, cost-sensitive workloads

**99.99% SLA (four nines):**
- Allows ~4 minutes downtime per month
- Minimum 2 eCKU (multi-AZ deployment)
- Higher cost
- **Good for:** Production, business-critical workloads

**Dedicated SLA for comparison:**
- Single-AZ (1 CKU): ~99.9%
- Multi-AZ (2+ CKU): ~99.99%

**Match your current Dedicated SLA:**
- If Dedicated is 1 CKU → Enterprise 99.9%
- If Dedicated is 2+ CKU multi-AZ → Enterprise 99.99%

**SLA credits:** If Confluent fails to meet SLA, you receive service credits (per contract terms).

---

## SECURITY & COMPLIANCE QUESTIONS

### Q24: Is Enterprise as secure as Dedicated?

**A:** Yes - same security controls, just multi-tenant infrastructure:

**Encryption:**
- ✅ At rest: AES-256 (same as Dedicated)
- ✅ In transit: TLS 1.2+ (same as Dedicated)
- ✅ BYOK support: Bring Your Own Key for encryption (same as Dedicated)

**Network Security:**
- ✅ Private Link / PNI for private connectivity (same as Dedicated)
- ✅ IP allowlisting (same as Dedicated)
- ✅ VPC isolation (your apps can't reach other tenants' data)

**Access Control:**
- ✅ RBAC (Role-Based Access Control) - same as Dedicated
- ✅ ACLs (Access Control Lists) - same as Dedicated
- ✅ API keys and service accounts - same as Dedicated
- ✅ SSO / SAML integration - same as Dedicated

**Compliance:**
- ✅ SOC 2 Type II certified
- ✅ ISO 27001 certified
- ✅ HIPAA compliant (with BAA)
- ✅ PCI-DSS compliant
- ✅ GDPR compliant

**Audit:**
- ✅ Audit logs available (same as Dedicated)
- ✅ CloudTrail integration for AWS

**What's different:**
- Infrastructure is shared (compute, storage) - but logically isolated
- Your data never mixes with other tenants' data
- Think: Multi-tenant building, single-tenant apartments

**If you have specific compliance requirements, discuss with Confluent Security team.**

---

### Q25: Can I use my own encryption keys (BYOK) with Enterprise?

**A:** Yes, Enterprise supports BYOK (Bring Your Own Key):

**Supported key management services:**
- AWS KMS (Key Management Service)
- Azure Key Vault
- Google Cloud KMS

**How it works:**
1. You create encryption keys in your KMS
2. Grant Confluent permission to use keys for encryption/decryption
3. Confluent encrypts your data at rest using your keys
4. You maintain control - can revoke access anytime

**Benefits:**
- ✅ You control key lifecycle (rotation, revocation)
- ✅ Meets compliance requirements for customer-managed keys
- ✅ Additional audit trail in your KMS logs

**Considerations:**
- Additional cost (your cloud provider charges for KMS usage)
- Slightly more complex setup
- If you revoke key access, Confluent can't decrypt your data (by design - be careful!)

**Best practice:** Use BYOK if compliance requires it, otherwise Confluent-managed keys are simpler and sufficient for most use cases.

---

### Q26: How does Enterprise handle data residency requirements?

**A:** Enterprise supports data residency through regional deployments:

**What you control:**
- ✅ **Cloud provider:** AWS, Azure, GCP
- ✅ **Region:** Any region where Confluent Enterprise is available
- ✅ **Data stays in region:** Data at rest remains in chosen region

**Example:** If you create Enterprise cluster in `eu-west-1` (Ireland):
- All data stored in EU
- Meets GDPR data residency requirements

**What travels outside region:**
- ⚠️ **Metadata** for cluster management (cluster configs, metrics) may go to Confluent control plane (different region)
- ⚠️ **Audit logs** may be replicated to Confluent's central logging

**If strict data residency required:**
- Verify with Confluent which data stays in-region vs. travels for management
- Review Data Processing Addendum (DPA) in your contract
- Discuss specific requirements with Confluent compliance team

**Most customers:** Standard regional deployment meets their data residency needs.

---

## PERFORMANCE QUESTIONS

### Q27: Will Enterprise perform as well as Dedicated?

**A:** Yes, typically meets or exceeds Dedicated performance:

**Throughput:**
- ✅ Same limits per eCKU as CKU (50 MBps ingress, 150 MBps egress)
- ✅ Auto-scaling can provide MORE capacity during spikes than fixed Dedicated

**Latency:**
- ✅ Comparable to Dedicated (single-digit millisecond p99 latency)
- ✅ Newer infrastructure may actually be faster in some cases

**Reliability:**
- ✅ 99.99% SLA (same as multi-AZ Dedicated)
- ✅ Multi-AZ deployment for high availability

**Advantages of Enterprise:**
- ✅ Auto-scaling responds faster than manual CKU additions
- ✅ Optimized resource allocation in multi-tenant model

**When to test:**
- Run pilot migration with performance benchmarks
- Compare latency (p50, p95, p99) before and after
- Load test during spike scenarios

**If performance doesn't meet expectations:**
- Confluent will work with you to tune
- Can add baseline eCKU if needed
- SLA credits if Confluent at fault

**Bottom line:** Performance is rarely a blocker for Enterprise migration.

---

### Q28: How fast does auto-scaling respond to traffic spikes?

**A:** Very fast - typically seconds to minutes:

**Auto-scaling triggers:**
- ✅ Throughput exceeds baseline eCKU capacity
- ✅ Partition load increases
- ✅ Storage usage grows

**Scale-up time:**
- **Typical:** 30 seconds to 2 minutes
- **Add capacity:** Transparently, no disruption to producers/consumers

**Scale-down time:**
- **More gradual:** 5-15 minutes
- **Reason:** Prevents thrashing (constant up/down)

**What you see:**
- eCKU utilization metric in Confluent Cloud console
- Auto-scaling events in audit logs
- Hourly billing for additional eCKU when active

**Comparison to Dedicated:**
- **Dedicated manual scaling:** 15-30 minutes (requires manual intervention + provisioning time)
- **Enterprise auto-scaling:** Seconds (automatic, no manual action)

**Best for:**
- Flash sales, sudden traffic spikes
- Batch job processing (spike for an hour, then drop)
- Unpredictable load patterns

**Not needed for:**
- Perfectly steady workloads (no spikes to respond to)

---

### Q29: What happens if I hit partition limits on Enterprise?

**A:** Enterprise has partition limits per eCKU - if you hit them, you need to add eCKUs or reduce partitions:

**Partition limits (approximate - check current docs):**
- ~3,000 partitions per eCKU
- vs. ~4,500 partitions per Dedicated CKU

**If you hit the limit:**

**Option 1: Add baseline eCKUs**
- Increase minimum eCKU count to accommodate more partitions
- **Example:** 12,000 partitions ÷ 3,000 = 4 eCKU minimum
- **Cost impact:** Higher baseline cost

**Option 2: Reduce partition count**
- **Consolidate topics:** Merge similar topics
- **Reduce partition count per topic:** If over-partitioned
- **Archive old topics:** Delete topics no longer needed
- **Cost impact:** Lower resource usage, but requires planning

**Option 3: Optimize partition usage**
- **Audit:** Are all partitions actually used?
- **Right-size:** Some topics may have too many partitions
- **Example:** 100-partition topic with 1 producer/consumer could use 10 partitions
- **Cost impact:** Neutral, just optimization

**Best practice:**
- Count partitions BEFORE migrating
- Plan for growth (don't run at 100% partition capacity)
- Monitor partition usage over time

**If partitions are a blocker:** Discuss with Confluent - they may have other options or roadmap changes.

---

## SUPPORT & TROUBLESHOOTING

### Q30: What support is available during and after migration?

**A:** Support level depends on your contract:

**Standard Support (included):**
- ✅ 24/7 ticket-based support
- ✅ P1 (critical): <1 hour response
- ✅ P2 (high): <4 hours response
- ✅ Access to documentation and community forums

**Enhanced Support (if in contract):**
- ✅ Technical Account Manager (TAM)
- ✅ Scheduled check-ins and reviews
- ✅ Proactive monitoring and alerts
- ✅ Architecture guidance

**Professional Services (billable):**
- ✅ Hands-on migration assistance
- ✅ Custom runbooks and automation
- ✅ Onsite or remote support
- ✅ Training for your team

**During migration:**
- Open support ticket for any issues
- TAM can provide migration oversight (if assigned)
- Escalate critical issues via Severity 1 tickets

**After migration:**
- Standard support continues
- Confluent monitors Enterprise cluster health 24/7
- Alerts if SLA at risk

**Community resources:**
- Confluent Community Slack
- Confluent Forums
- GitHub (for Terraform Provider issues)

**Best practice:** Establish support contact BEFORE starting migration - don't wait until you hit an issue.

---

### Q31: What are common migration issues and how do I avoid them?

**A:** Top issues and solutions:

**Issue 1: Network connectivity failures**
- **Symptom:** Applications can't reach Enterprise cluster
- **Cause:** Private Link not configured correctly, DNS issues
- **Prevention:** 
  - Test connectivity BEFORE migrating applications
  - Validate Access Point endpoints are reachable from VPC
  - Set up private DNS zones correctly
- **Fix:** Work with network team to troubleshoot VPC endpoints, routes, security groups

---

**Issue 2: Permission/authentication errors**
- **Symptom:** Apps get "not authorized" errors
- **Cause:** ACLs or RBAC not configured on Enterprise
- **Prevention:**
  - Replicate service accounts, ACLs, RBAC from Dedicated to Enterprise BEFORE cutover
  - Test with pilot application first
- **Fix:** Grant necessary permissions in Enterprise cluster

---

**Issue 3: Cluster Linking lag not decreasing**
- **Symptom:** Link lag stuck at high number, not approaching zero
- **Cause:** Throughput limits, network bandwidth, configuration issues
- **Prevention:**
  - Monitor link lag from the start
  - Ensure Enterprise cluster has sufficient eCKU capacity for backfill
- **Fix:** Increase Enterprise baseline eCKU temporarily to speed up backfill

---

**Issue 4: Consumer offset sync issues**
- **Symptom:** Consumers start reading from beginning or wrong offset after cutover
- **Cause:** Consumer offset sync not configured in Cluster Link
- **Prevention:**
  - Enable consumer offset sync when creating Cluster Link
  - Verify offsets synced before cutting over consumers
- **Fix:** Manually set consumer group offsets or restart sync

---

**Issue 5: Connector failures after migration**
- **Symptom:** Connectors in FAILED state on Enterprise
- **Cause:** Networking changes (Private Link vs. Peering), credential issues
- **Prevention:**
  - Test connectors in shadow mode before deleting Dedicated connectors
  - Update connector networking configs for new setup
- **Fix:** Review connector logs, fix network/credential issues, restart connector

---

**Issue 6: Higher-than-expected costs**
- **Symptom:** Enterprise bill higher than estimate
- **Cause:** More auto-scaling than expected, storage growth, networking charges
- **Prevention:**
  - Conservative cost estimates (overestimate auto-scaling)
  - Monitor eCKU utilization during pilot
- **Fix:** Analyze cost breakdown, optimize (reduce storage, tune eCKU baseline)

---

**Issue 7: Schema Registry endpoint confusion**
- **Symptom:** Applications can't reach Schema Registry
- **Cause:** Using wrong SR endpoint (Access Point vs. public)
- **Prevention:**
  - Document correct SR endpoint per Access Point
  - Update all application configs
- **Fix:** Correct SR URL in application configs, restart apps

---

### Q32: How do I get help if I'm stuck during migration?

**A:** Multiple options:

**1. Open a Confluent Support Ticket:**
- Go to Confluent Cloud console → Support → New Ticket
- Choose appropriate severity:
  - **P1 (Critical):** Migration blocked, production impact
  - **P2 (High):** Migration delayed, no production impact yet
  - **P3 (Normal):** Question or minor issue
- Include:
  - Cluster IDs (Dedicated and Enterprise)
  - Error messages and logs
  - Steps you've already tried

**2. Contact your Confluent Account Team:**
- Account Manager (for billing, contract questions)
- Solutions Architect (for technical architecture questions)
- Technical Account Manager / TAM (if assigned - for migration guidance)

**3. Engage Professional Services:**
- If migration is complex and you need hands-on help
- Contact account team to arrange

**4. Use Community Resources:**
- Confluent Community Slack: https://launchpass.com/confluentcommunity
- Confluent Forums: https://forum.confluent.io/
- Stack Overflow: Tag questions with `apache-kafka` and `confluent`

**5. Check Documentation:**
- Cluster Linking docs: https://docs.confluent.io/cloud/current/multi-cloud/cluster-linking/
- Migration guides: https://docs.confluent.io/cloud/current/clusters/migrate/

**Best practice:**
- Don't wait until you're blocked - ask questions early
- Open ticket BEFORE production cutover if you're uncertain
- Provide detailed context to get faster resolution

---

### Q33: Can I pause or cancel a migration midway?

**A:** Yes, you can pause or cancel at any time:

**To Pause:**
1. Stop migrating additional applications
2. Leave Cluster Link running (keeps Enterprise in sync)
3. Keep both clusters operational
4. Resume when ready

**Cost during pause:**
- You continue paying for both clusters
- Minimize pause duration to avoid excess overlap costs

**To Cancel:**
1. Stop cutting over applications
2. Keep applications on Dedicated
3. Delete Cluster Link (optional - saves a bit of cost)
4. Delete Enterprise cluster (or keep for future retry)

**If you cancel:**
- No penalty from Confluent (no contract commitment for specific migration timeline)
- You lose time invested in planning/setup
- You continue paying Dedicated costs (miss out on Enterprise savings)

**When to pause/cancel:**
- Unexpected technical blockers discovered
- Internal resource constraints (team too busy)
- Business priorities shift
- Cost-benefit no longer favorable

**When to push through:**
- Pause costs add up (overlap period gets expensive)
- Most issues are solvable with Confluent support
- Opportunity cost of staying on Dedicated is high

**Recommendation:** If you hit a blocker, engage Confluent support BEFORE deciding to cancel - most issues have solutions.

---

## NEXT STEPS

### Q34: I'm interested in migrating. What do I do next?

**A:** Follow these steps:

**Step 1: Discovery (Week 1)**
- ✅ Review feature comparison matrix - ensure no blockers
- ✅ Gather last 90 days usage metrics from Dedicated cluster
- ✅ Count total partitions across all topics
- ✅ Document current monthly Dedicated costs

**Step 2: Cost Estimate (Week 1-2)**
- ✅ Use Confluent pricing calculator OR
- ✅ Request estimate from your Confluent account team
- ✅ Compare to current Dedicated spend
- ✅ Calculate payback period (including overlap costs)

**Step 3: Technical Review (Week 2)**
- ✅ Review networking requirements (Private Link setup)
- ✅ Identify applications that will migrate
- ✅ List connectors and Flink jobs (if any)
- ✅ Assess KSQL migration effort (if using KSQL)

**Step 4: Internal Alignment (Week 2-3)**
- ✅ Get buy-in from stakeholders (engineering, finance, security)
- ✅ Allocate team resources for migration
- ✅ Define migration timeline and milestones

**Step 5: Plan Migration (Week 3-4)**
- ✅ Create detailed migration runbook
- ✅ Schedule migration windows (pilot and full)
- ✅ Coordinate with network team for Private Link setup
- ✅ Set up Enterprise cluster

**Step 6: Execute Pilot (Week 4-5)**
- ✅ Migrate one non-critical application
- ✅ Validate cost, performance, functionality
- ✅ Identify and resolve issues
- ✅ Document lessons learned

**Step 7: Full Migration (Week 6+)**
- ✅ Execute phased migration per plan
- ✅ Monitor and validate continuously
- ✅ Decommission Dedicated after 1-4 weeks stable operation

**Need help?** Contact your Confluent account team to get started.

---

### Q35: Where can I learn more?

**A:** Resources:

**Confluent Documentation:**
- Cluster Types Comparison: https://docs.confluent.io/cloud/current/clusters/cluster-types.html
- Cluster Linking: https://docs.confluent.io/cloud/current/multi-cloud/cluster-linking/
- Enterprise Clusters: https://docs.confluent.io/cloud/current/clusters/enterprise/

**Pricing:**
- Pricing Calculator: https://www.confluent.io/confluent-cloud/pricing/
- (Check with account team for custom quotes)

**Training:**
- Confluent Developer: https://developer.confluent.io/
- Kafka Fundamentals: https://www.confluent.io/training/

**Community:**
- Confluent Community Slack: https://launchpass.com/confluentcommunity
- Forums: https://forum.confluent.io/

**Support:**
- Contact your Confluent Account Manager
- Open support ticket via Confluent Cloud console

**Blog Posts & Case Studies:**
- Confluent Blog: https://www.confluent.io/blog/
- Customer success stories with Enterprise migrations

---

**Still have questions? Contact your Confluent team or open a support ticket. We're here to help!**

---

**Document Version:** 1.0  
**Last Updated:** April 2026  
**Maintained by:** [Your Name/Team]  
**Feedback:** [your-email@company.com]
