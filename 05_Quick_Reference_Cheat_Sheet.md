# QUICK REFERENCE CHEAT SHEET
## Dedicated to Enterprise Migration
**🖨️ PRINT THIS - Keep next to you during customer calls**

---

## 🎯 KAFKA BASICS (FOR NEW CUSTOMERS)

| Term | Simple Explanation |
|------|-------------------|
| **Kafka** | Digital postal service for real-time messages between apps |
| **Confluent** | Company that offers managed Kafka in the cloud |
| **Topic** | Category/channel for messages (like a mailbox) |
| **Partition** | Parallel lane within a topic (for scale) |
| **Producer** | App that WRITES data to Kafka |
| **Consumer** | App that READS data from Kafka |
| **CKU/eCKU** | Capacity units (like computing power blocks) |

---

## 💰 KEY NUMBERS

| Metric | Value |
|--------|-------|
| **Typical Savings** | 20-60% |
| **Migration Timeline** | 4-11 weeks |
| **Overlap Period** | 2-6 weeks (paying for both clusters) |
| **Payback Period** | 2-6 months |
| **Partition Difference** | eCKU has 1,500 FEWER partitions than CKU |
| **SLA Options** | 99.9% (1 eCKU min) or 99.99% (2 eCKU min) |
| **Throughput per eCKU** | 50 MBps ingress, 150 MBps egress |

---

## 🎤 OPENING DISCOVERY QUESTIONS

**Ask 3-5 of these to understand their situation:**

1. ✅ "Are you currently running Dedicated clusters? How many?"
2. ✅ "What's driving your interest in Enterprise - cost, auto-scaling, something else?"
3. ✅ "How familiar is your team with Kafka - should I start with basics or dive deep?"
4. ✅ "What concerns you most about a migration?"
5. ✅ "Does your workload have traffic spikes throughout the day/week?"
6. ✅ "Are you on AWS, Azure, or GCP?"
7. ✅ "Do you use Flink, KSQL, or connectors?"

---

## 🔑 CORE VALUE PROP (30-SECOND PITCH)

*"Enterprise clusters save customers 20-60% compared to Dedicated, primarily through three benefits:*

1. ***Cost efficiency** - you only pay for capacity when you use it, not 24/7*
2. ***Auto-scaling** - handles traffic spikes automatically, no manual intervention*
3. ***Networking savings** - for AWS, PNI reduces data transfer charges significantly*

*Migration takes 4-8 weeks with zero downtime using Cluster Linking. Would you like to see what savings look like for your specific workload?"*

---

## 🚦 CUSTOMER PRIORITIZATION (Who to Target First)

### **🟢 TIER 1: HIGHEST ROI - Target These First**
- ✅ Over-provisioned Dedicated clusters (>2 CKU but low traffic)
- ✅ Spiky workloads (e-commerce, retail, batch processing)
- ✅ AWS customers with high networking costs
- **Savings:** 40-60% | **Payback:** 2-3 months

### **🟡 TIER 2: GOOD ROI - Solid Candidates**
- ✅ Partition-heavy workloads
- ✅ Small steady workloads
- **Savings:** 20-40% | **Payback:** 3-6 months

### **🔴 TIER 3: EVALUATE CAREFULLY - Marginal**
- ⚠️ High-throughput, perfectly steady workloads
- ⚠️ Already optimized Dedicated clusters
- **Savings:** <20% | **Payback:** >6 months

---

## ⚡ TOP 5 OBJECTIONS & QUICK RESPONSES

### 1️⃣ **"Too risky / Can't afford downtime"**
**Response:** *"Zero-downtime migration using Cluster Linking - Dedicated stays operational the whole time, Enterprise syncs in background. If anything goes wrong, instant rollback. Like building a new highway next to the old one."*

---

### 2️⃣ **"Too expensive / Overlap costs"**
**Response:** *"You'll pay for both clusters 2-4 weeks. Example: If you save $2K/month and overlap costs $8K, you break even in 4 months. After that, $24K/year in your pocket. Worth a 1-month investment?"*

---

### 3️⃣ **"Multi-tenant isn't secure / Compliance won't allow it"**
**Response:** *"Enterprise is SOC 2, ISO 27001, HIPAA, PCI-DSS certified. Your data is logically isolated, encrypted with your keys. Think: apartment building (shared infrastructure) but your unit is private and locked. Can we schedule a security deep-dive with your compliance team?"*

---

### 4️⃣ **"We're too busy right now"**
**Response:** *"Every month on Dedicated costs 30-40% more than Enterprise. Delaying 3 months = $X,000 in lost savings. Most hands-on work is 1-2 weeks during cutover. We can time it for your slower period and start planning now in background. What if we scoped a pilot requiring <20 hours of your team's time?"*

---

### 5️⃣ **"We use KSQL heavily - migration is too hard"**
**Response:** *"KSQL is deprecated even on Dedicated - migration to Flink is inevitable. How many queries? Simple ones translate easily to Flink SQL. We have migration guides and Professional Services to help convert. Do it now as part of this migration (one-time effort) vs. two separate migrations later."*

---

## 📊 COST SCENARIO MATCHING

**Match customer to scenario, reference that page in cost doc:**

| Customer Profile | Scenario | Savings | Doc Page |
|-----------------|----------|---------|----------|
| 1 CKU, steady, small | #1 Small Steady | 23% | Page 1 |
| 2 CKU, spiky (e-commerce) | #2 Spiky Workload | 45% | Page 3 |
| 4+ CKU, many partitions | #3 Partition-Heavy | 44% | Page 6 |
| 3+ CKU, low traffic | #4 Over-Provisioned | 59-84% | Page 9 |
| High throughput, steady | #5 Steady Workload | 43% | Page 12 |
| AWS, high networking | #6 AWS PNI | 61% | Page 15 |

---

## 🛠️ MIGRATION CHECKLIST (Quick Validation)

**Ask customer to confirm BEFORE proceeding:**

- [ ] **Multi-tenant OK?** (compliance/security policies)
- [ ] **Features available?** (check comparison matrix - no blockers)
- [ ] **Networking change acceptable?** (Private Link vs. Peering)
- [ ] **KSQL plan?** (migrate to Flink or stay on Dedicated for now)
- [ ] **Last 90 days metrics?** (for cost estimate)
- [ ] **Partition count?** (verify under eCKU limits)
- [ ] **Internal buy-in?** (engineering, finance, security aligned)

---

## 🗓️ TYPICAL TIMELINE

```
Week 1-2:  Discovery, sizing, provision Enterprise cluster
Week 2-3:  Establish Cluster Linking, data backfill
Week 3-4:  Pilot migration (test 1-2 applications)
Week 4-6:  Full migration (all apps, connectors, Flink)
Week 6-11: Validation period (keep both clusters)
Week 11+:  Decommission Dedicated, full savings realized
```

**Cutover itself:** Hours (most time is prep & validation)

---

## 📞 NEXT STEPS (CLOSING)

**If customer is interested:**

✅ **Immediate:**
- Send: Presentation deck + FAQ document + feature comparison link
- Gather: Last 90 days usage metrics from their Dedicated cluster
- Schedule: Follow-up in 1-2 weeks to review cost estimate

✅ **Week 1-2:**
- Build detailed cost estimate with their metrics
- Technical Q&A session if needed
- Decision: Proceed to pilot or full migration

✅ **Week 3+:**
- Provision Enterprise cluster
- Establish Cluster Linking
- Execute pilot migration

**If customer needs more info:**
- Offer: Technical deep-dive with Confluent SA
- Offer: Reference call with customer who completed migration
- Offer: Pilot/POC to test before committing

**If customer not ready:**
- Send: Materials for when they're ready
- Follow-up: Quarterly check-in
- Keep: Updated on new Enterprise features

---

## 🔗 LINKS TO SHARE

**Feature Comparison:**  
https://docs.confluent.io/cloud/current/clusters/cluster-types.html

**Cluster Linking Docs:**  
https://docs.confluent.io/cloud/current/multi-cloud/cluster-linking/

**Pricing Calculator:**  
https://www.confluent.io/confluent-cloud/pricing/

**Confluent Training:**  
https://developer.confluent.io/

---

## 🎯 ANALOGIES (Make It Simple)

| Concept | Analogy |
|---------|---------|
| **Dedicated vs Enterprise** | Rented dedicated truck vs. Uber (auto-scales) |
| **Cluster Linking** | Two mirrors that sync in real-time |
| **Partitions** | Highway lanes (more lanes = more parallel traffic) |
| **Multi-tenant** | Apartment building (shared building, private units) |
| **Private Link** | Secure tunnel from your building to theirs |
| **Auto-scaling** | Restaurant staff (minimum + more during rush hour) |
| **Kafka** | Super-fast postal service for digital messages |

---

## ❗ WATCH-OUTS (Address Proactively)

⚠️ **Networking/DNS:** Access Points = different endpoints per service  
⚠️ **Partitions:** eCKU has 1,500 FEWER partitions - count before migrating  
⚠️ **KSQL:** Not available - must migrate to Flink  
⚠️ **Flink:** Verify private networking GA in customer's region  
⚠️ **Connectors:** Need reconfiguration for new networking  
⚠️ **Schema Registry:** Different endpoints per Access Point  

---

## 💡 PRO TIPS

✅ **Lead with value:** "I've identified $50K/year savings for you"  
✅ **Use their language:** If they say "cost," focus on savings. If "operations," focus on auto-scaling  
✅ **Pilot first:** Always recommend pilot before full migration  
✅ **Be honest:** If Enterprise isn't right fit, say so (builds trust)  
✅ **Ask questions:** Discovery > presentation  
✅ **Show, don't tell:** Pull up cost calculator during call  
✅ **Document everything:** Capture objections, questions, next steps  

---

## 🚨 WHEN TO WALK AWAY

**Enterprise NOT right if:**
- ❌ Genuine compliance blocker (rare, but possible)
- ❌ Heavy KSQL usage with zero budget for Flink migration
- ❌ Partition-density exceeds Enterprise limits (even at max eCKU)
- ❌ Customer unwilling to move to multi-tenant under any circumstance

**Say:** *"Enterprise might not be the right fit right now. Let's revisit in 6 months when [Feature X available / contract renewal / KSQL deprecated]."*

**Integrity > Short-term sale**

---

## 📋 POST-CALL CHECKLIST

**Within 24 hours:**
- [ ] Send thank-you email
- [ ] Attach FAQ document + presentation deck
- [ ] Propose next steps with calendar invite
- [ ] Log objections/questions in CRM

**Within 1 week:**
- [ ] Build cost estimate with their metrics
- [ ] Research any technical questions
- [ ] Coordinate with Confluent SA/account team
- [ ] Follow up on next meeting

---

## 🎓 REMEMBER

**Your Goal:** Help customer make RIGHT decision (not just "sell Enterprise")

**Success = Customer Success:** If they migrate and save money, you win  
**Failure = Overselling:** If you push them to Enterprise when Dedicated is better, everyone loses

**Trust > Commission:** Honest assessment builds long-term relationships

---

**🖨️ PRINT THIS & KEEP IT NEXT TO YOU DURING CALLS**

**Last Updated:** April 20, 2026  
**Questions?** Reference the full documents in this kit  
**Need help?** Contact your Confluent account team

---

**GOOD LUCK!** 🚀
