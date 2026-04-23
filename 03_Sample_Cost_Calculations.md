# Sample Cost Calculations
## Dedicated to Enterprise Migration Scenarios

**Note:** All prices are illustrative examples. Use Confluent's actual pricing calculator and your account team for real quotes.

---

## HOW TO USE THIS DOCUMENT

For each scenario below:
1. Find the one that most closely matches your situation
2. Adjust the numbers to match your actual metrics
3. Use the formula to estimate your Enterprise costs
4. Compare to your current Dedicated spend

**Key Metrics You Need:**
- Current monthly Dedicated spend
- Average ingress MBps (megabytes per second)
- Average egress MBps
- Peak ingress/egress MBps
- Total partition count across all topics
- Storage GB retained
- Current CKU count
- Current SLA/availability zone setup

---

## SCENARIO 1: SMALL, STEADY-STATE WORKLOAD

### Customer Profile
- **Industry:** Startup / Small Business
- **Use Case:** Basic event streaming for microservices
- **Current Setup:** 1 CKU Dedicated, Single AZ
- **Workload:** Very steady, minimal spikes

### Current Dedicated Metrics
- **CKU:** 1
- **Average Ingress:** 5 MBps
- **Average Egress:** 10 MBps
- **Peak Ingress:** 7 MBps
- **Peak Egress:** 15 MBps
- **Partitions:** 50
- **Storage:** 100 GB
- **Spike Pattern:** Minimal (<20% variance)

### Current Dedicated Cost
**$1,500/month** (example)

---

### Enterprise Sizing

**SLA Selection:** 99.9% (1 eCKU minimum)
- *Reason:* Single-AZ Dedicated suggests non-production or cost-sensitive

**Baseline eCKU:** 1 eCKU
- *Reason:* Throughput is low, steady-state, minimal auto-scaling needed

**Expected Auto-Scaling:** Minimal
- *Reason:* Spikes are only 20-30% above average, well within 1 eCKU limits

**Partitions:** 50 (well under 3,000 per eCKU limit)

---

### Enterprise Cost Estimate

**Base Cost:** 1 eCKU @ 99.9% SLA = **$1,000/month** (example)

**Auto-Scaling Overhead:** ~5% = **$50/month**
- *Calculation:* Minimal spikes, rare auto-scaling events

**Storage:** 100 GB @ $0.10/GB = **$10/month**

**Networking (estimate):** **$100/month**
- *If using Private Link, depends on data transfer volume*

**Total Enterprise Cost:** **$1,160/month**

---

### Comparison

| Item | Dedicated | Enterprise | Savings |
|------|-----------|------------|---------|
| Monthly Cost | $1,500 | $1,160 | **$340 (23%)** |
| Annual Cost | $18,000 | $13,920 | **$4,080** |

---

### Migration Overlap Cost

**Overlap Period:** 3 weeks = ~0.75 months

**Overlap Cost:** $1,500 + $1,160 = $2,660 (for one month)

**Pro-rated Overlap:** $2,660 × 0.75 = **~$2,000 one-time**

**Payback Period:** $2,000 / $340 per month = **~6 months**

---

### Recommendation

✅ **Strong candidate for migration**
- Clear cost savings (23%)
- Minimal complexity (low partition count, steady workload)
- Fast payback (6 months)

**Risks:** Minimal - straightforward migration

---

## SCENARIO 2: MEDIUM, SPIKY WORKLOAD (E-COMMERCE)

### Customer Profile
- **Industry:** E-commerce / Retail
- **Use Case:** Order processing, inventory updates, real-time analytics
- **Current Setup:** 2 CKU Dedicated, Multi-AZ
- **Workload:** Spiky - high during business hours, low at night

### Current Dedicated Metrics
- **CKU:** 2
- **Average Ingress:** 30 MBps
- **Average Egress:** 60 MBps
- **Peak Ingress:** 90 MBps (3x average - during flash sales)
- **Peak Egress:** 180 MBps
- **Partitions:** 500
- **Storage:** 2 TB (2,000 GB)
- **Spike Pattern:** Daily spikes during business hours (12 hours/day), weekly spikes during sales events

### Current Dedicated Cost
**$4,500/month** (example for 2 CKU multi-AZ)

---

### Enterprise Sizing

**SLA Selection:** 99.99% (2 eCKU minimum)
- *Reason:* Multi-AZ Dedicated suggests production workload

**Baseline eCKU:** 2 eCKU
- *Reason:* Size for average throughput (30 MBps ingress, 60 MBps egress)

**Expected Auto-Scaling:** Moderate to High
- *Reason:* Daily spikes to 3x average for 12 hours/day
- *Auto-scale to ~4-5 eCKU during business hours, scale back to 2 eCKU at night*

**Partitions:** 500 (well under 6,000 limit for 2 eCKU)

---

### Enterprise Cost Estimate

**Base Cost:** 2 eCKU @ 99.99% SLA = **$2,800/month** (example)

**Auto-Scaling Overhead:** ~40% = **$1,120/month**
- *Calculation:* 
  - Daily spikes: 12 hours/day × 30 days = 360 hours of higher eCKU usage
  - Average additional 2-3 eCKU during spikes
  - 360 hours × $3/hour (example rate) = ~$1,080/month

**Storage:** 2,000 GB @ $0.10/GB = **$200/month**

**Networking (Private Link):** **$300/month**
- *Higher data transfer volume*

**Total Enterprise Cost:** **$4,420/month**

---

### Comparison

| Item | Dedicated | Enterprise | Savings |
|------|-----------|------------|---------|
| Monthly Cost | $4,500 | $4,420 | **$80 (2%)** |
| Annual Cost | $54,000 | $53,040 | **$960** |

**But wait - there's more:**

On Dedicated, you'd likely need to scale up to 3-4 CKU to handle flash sale spikes safely (with buffer).

**Dedicated with Buffer:** 4 CKU = **$8,000/month** (example)

**Enterprise (auto-scales):** **$4,420/month**

**Real Savings:** $8,000 - $4,420 = **$3,580/month (45%)**

---

### Additional Benefits for This Scenario

✅ **No manual scaling** - auto-scaling handles flash sales automatically
✅ **Better spike handling** - scales up in seconds, not hours
✅ **No over-provisioning** - only pay for peak capacity when used

---

### Migration Overlap Cost

**Overlap Period:** 4 weeks = 1 month

**Overlap Cost:** $4,500 + $4,420 = **$8,920 one-time**

**Payback Period (comparing to buffered Dedicated):** $8,920 / $3,580 = **~2.5 months**

---

### Recommendation

✅ **EXCELLENT candidate for migration**
- Significant cost savings (45% vs. buffered Dedicated)
- Auto-scaling perfect for spiky workload
- Fast payback (2.5 months)
- Operational benefit (no manual scaling)

**Risks:** Low - ideal use case for Enterprise

---

## SCENARIO 3: LARGE, PARTITION-HEAVY WORKLOAD

### Customer Profile
- **Industry:** Financial Services / IoT
- **Use Case:** High-volume sensor data, many independent data streams
- **Current Setup:** 4 CKU Dedicated, Multi-AZ
- **Workload:** Many topics, high partition count, moderate throughput

### Current Dedicated Metrics
- **CKU:** 4
- **Average Ingress:** 40 MBps
- **Average Egress:** 80 MBps
- **Peak Ingress:** 60 MBps
- **Peak Egress:** 120 MBps
- **Partitions:** 12,000 (This is the driver!)
- **Storage:** 5 TB (5,000 GB)
- **Spike Pattern:** Moderate (1.5x variance)

### Current Dedicated Cost
**$12,000/month** (example for 4 CKU multi-AZ)

---

### Enterprise Sizing

**SLA Selection:** 99.99% (2+ eCKU minimum)

**Throughput-based sizing:** Would need 2 eCKU (40 MBps average)

**Partition-based sizing:** 12,000 partitions ÷ 3,000 per eCKU = **4 eCKU minimum**

**Actual Baseline:** **4 eCKU** (partition-constrained, not throughput-constrained)

**Expected Auto-Scaling:** Minimal
- *Reason:* Throughput is well within 4 eCKU capacity, minimal spikes

**Partitions:** 12,000 (right at 4 eCKU limit)

---

### Enterprise Cost Estimate

**Base Cost:** 4 eCKU @ 99.99% SLA = **$5,600/month** (example)

**Auto-Scaling Overhead:** ~5% = **$280/month**
- *Minimal scaling due to moderate spikes*

**Storage:** 5,000 GB @ $0.10/GB = **$500/month**

**Networking:** **$400/month**

**Total Enterprise Cost:** **$6,780/month**

---

### Comparison

| Item | Dedicated | Enterprise | Savings |
|------|-----------|------------|---------|
| Monthly Cost | $12,000 | $6,780 | **$5,220 (44%)** |
| Annual Cost | $144,000 | $81,360 | **$62,640** |

---

### Why the Big Savings?

**Dedicated:** Over-provisioned for throughput (4 CKU can handle much more than 40 MBps, but needed for partitions)

**Enterprise:** More efficient pricing even at same eCKU count, plus better discounting

---

### Watch-Out: Partition Limit

⚠️ **If partition count grows, you'll need to add eCKUs**

- Current: 12,000 partitions = 4 eCKU
- If grows to 15,000 partitions = 5 eCKU needed
- Consider partition consolidation strategies if at limit

---

### Migration Overlap Cost

**Overlap Period:** 6 weeks = 1.5 months (larger cluster, more complex)

**Overlap Cost:** ($12,000 + $6,780) × 1.5 = **$28,170 one-time**

**Payback Period:** $28,170 / $5,220 = **~5.4 months**

---

### Recommendation

✅ **Good candidate for migration**
- Significant cost savings (44%)
- Faster payback despite higher overlap cost (5-6 months)

⚠️ **Watch-out:** Partition growth - plan capacity headroom or partition consolidation

**Risks:** Medium - complex due to scale, but manageable

---

## SCENARIO 4: OVER-PROVISIONED LEGACY CLUSTER

### Customer Profile
- **Industry:** Any
- **Use Case:** Legacy system, originally provisioned for higher load that never materialized
- **Current Setup:** 3 CKU Dedicated, Multi-AZ
- **Workload:** Actually very light, cluster is way over-provisioned

### Current Dedicated Metrics
- **CKU:** 3
- **Average Ingress:** 8 MBps
- **Average Egress:** 15 MBps
- **Peak Ingress:** 12 MBps
- **Peak Egress:** 25 MBps
- **Partitions:** 200
- **Storage:** 500 GB
- **Spike Pattern:** Minimal

### Why 3 CKU?
*"We provisioned for expected growth that didn't happen"* or *"Better safe than sorry"*

### Current Dedicated Cost
**$7,500/month** (example for 3 CKU)

---

### Enterprise Sizing

**SLA Selection:** 99.99% (2 eCKU minimum)

**Right-sized Baseline:** **2 eCKU**
- *Reason:* Throughput only needs 1 eCKU, but 2 eCKU minimum for 99.99% SLA

**Expected Auto-Scaling:** None to minimal

**Partitions:** 200 (way under 6,000 limit)

---

### Enterprise Cost Estimate

**Base Cost:** 2 eCKU @ 99.99% SLA = **$2,800/month** (example)

**Auto-Scaling Overhead:** ~2% = **$56/month**

**Storage:** 500 GB @ $0.10/GB = **$50/month**

**Networking:** **$150/month**

**Total Enterprise Cost:** **$3,056/month**

---

### Comparison

| Item | Dedicated | Enterprise | Savings |
|------|-----------|------------|---------|
| Monthly Cost | $7,500 | $3,056 | **$4,444 (59%)** |
| Annual Cost | $90,000 | $36,672 | **$53,328** |

---

### Why Such Big Savings?

**Dedicated:** Severely over-provisioned (3 CKU for workload that needs 1)

**Enterprise:** Right-sized automatically (2 eCKU minimum, which is still more than needed but much better)

---

### Alternative: Could Use 99.9% SLA

If this is actually non-production or can tolerate 99.9% SLA:

**Enterprise @ 99.9% SLA:** 1 eCKU = **$1,000/month** (example)

**Total Cost:** ~**$1,200/month**

**Savings:** **$6,300/month (84%)**

---

### Migration Overlap Cost

**Overlap Period:** 3 weeks = 0.75 months

**Overlap Cost:** ($7,500 + $3,056) × 0.75 = **$7,917 one-time**

**Payback Period:** $7,917 / $4,444 = **~1.8 months**

---

### Recommendation

✅✅ **HIGHEST PRIORITY candidate for migration**
- Massive cost savings (59-84%)
- Fastest payback (<2 months)
- Low complexity (light workload, low partitions)
- Quick win

**Risks:** Very low - simple migration

**Action:** Migrate ASAP

---

## SCENARIO 5: HIGH-THROUGHPUT, STEADY WORKLOAD (NOT IDEAL FOR ENTERPRISE)

### Customer Profile
- **Industry:** Media / Streaming
- **Use Case:** High-volume, constant video/audio metadata streaming
- **Current Setup:** 6 CKU Dedicated, Multi-AZ
- **Workload:** Very high throughput, but extremely steady (24/7 constant load)

### Current Dedicated Metrics
- **CKU:** 6
- **Average Ingress:** 250 MBps
- **Average Egress:** 500 MBps
- **Peak Ingress:** 260 MBps (only 4% variance!)
- **Peak Egress:** 520 MBps
- **Partitions:** 2,000
- **Storage:** 10 TB (10,000 GB)
- **Spike Pattern:** Virtually none - constant 24/7 load

### Current Dedicated Cost
**$18,000/month** (example for 6 CKU)

---

### Enterprise Sizing

**SLA Selection:** 99.99% (2+ eCKU minimum)

**Baseline eCKU:** **6 eCKU**
- *Reason:* Throughput requires it (250 MBps ingress, 500 MBps egress)

**Expected Auto-Scaling:** None
- *Reason:* Load is constant, no spikes

**Partitions:** 2,000 (under 18,000 limit for 6 eCKU)

---

### Enterprise Cost Estimate

**Base Cost:** 6 eCKU @ 99.99% SLA = **$8,400/month** (example)

**Auto-Scaling Overhead:** ~1% = **$84/month**
- *Almost none - load is constant*

**Storage:** 10,000 GB @ $0.10/GB = **$1,000/month**

**Networking:** **$800/month** (high data transfer)

**Total Enterprise Cost:** **$10,284/month**

---

### Comparison

| Item | Dedicated | Enterprise | Savings |
|------|-----------|------------|---------|
| Monthly Cost | $18,000 | $10,284 | **$7,716 (43%)** |
| Annual Cost | $216,000 | $123,408 | **$92,592** |

---

### Analysis

**Savings are decent (43%)** - but mostly from better Enterprise pricing, not from auto-scaling benefits (since workload is steady).

---

### Migration Overlap Cost

**Overlap Period:** 8 weeks = 2 months (large, complex cluster)

**Overlap Cost:** ($18,000 + $10,284) × 2 = **$56,568 one-time**

**Payback Period:** $56,568 / $7,716 = **~7.3 months**

---

### Recommendation

⚠️ **MARGINAL candidate for migration**

**Pros:**
- Savings are still significant ($92K/year)
- Payback within 8 months is reasonable

**Cons:**
- Not leveraging Enterprise's key benefit (auto-scaling)
- Longer payback due to overlap costs
- Complex migration (high throughput, large cluster)

**Alternative Consideration:**
- Negotiate better pricing on Dedicated renewal
- If Confluent can discount Dedicated to match or beat Enterprise, might not be worth migration effort

**Decision Factors:**
- How price-sensitive is the customer?
- What's their risk tolerance for migration?
- Are there other benefits (features, roadmap) that make Enterprise attractive?

**Verdict:** Migration makes sense if customer values cost savings and accepts migration effort, but it's not a slam-dunk like spiky workloads.

---

## SCENARIO 6: AWS WITH HIGH NETWORKING COSTS

### Customer Profile
- **Industry:** Data Analytics
- **Use Case:** Cross-region data replication, multi-AZ high-throughput
- **Current Setup:** 3 CKU Dedicated, Multi-AZ, heavy cross-AZ traffic
- **Workload:** Moderate throughput, high networking costs due to AWS data transfer charges

### Current Dedicated Metrics
- **CKU:** 3
- **Average Ingress:** 50 MBps
- **Average Egress:** 100 MBps
- **Cross-AZ Traffic:** Very high (multi-AZ replication + consumer traffic)
- **Partitions:** 600
- **Storage:** 1 TB
- **Spike Pattern:** Moderate

### Current Dedicated Cost
**Kafka Cost:** $7,500/month (3 CKU)
**AWS Networking Charges:** $3,000/month (data transfer across AZs/regions)
**Total:** **$10,500/month**

---

### Enterprise Sizing with PNI

**SLA Selection:** 99.99% (2 eCKU minimum)

**Baseline eCKU:** 2 eCKU (right-sized for actual throughput)

**PNI (Private Network Interface):** Reduces AWS data transfer costs significantly

---

### Enterprise Cost Estimate

**Base Cost:** 2 eCKU @ 99.99% SLA = **$2,800/month**

**Auto-Scaling Overhead:** ~15% = **$420/month**

**Storage:** 1,000 GB @ $0.10/GB = **$100/month**

**Networking with PNI:** **$800/month** (vs. $3,000 before)
- *PNI reduces AWS charges by ~70%*

**Total Enterprise Cost:** **$4,120/month**

---

### Comparison

| Item | Dedicated | Enterprise | Savings |
|------|-----------|------------|---------|
| Monthly Cost | $10,500 | $4,120 | **$6,380 (61%)** |
| Annual Cost | $126,000 | $49,440 | **$76,560** |

**Breakdown of Savings:**
- Kafka cost reduction: $7,500 → $2,800 = $4,700/month
- Networking cost reduction: $3,000 → $800 = $2,200/month
- **Total:** $6,900/month

---

### Migration Overlap Cost

**Overlap Period:** 4 weeks = 1 month

**Overlap Cost:** $10,500 + $4,120 = **$14,620 one-time**

**Payback Period:** $14,620 / $6,380 = **~2.3 months**

---

### Recommendation

✅✅ **EXCELLENT candidate for migration (AWS only)**
- Massive savings (61%)
- Fast payback (2-3 months)
- PNI is game-changer for AWS networking costs
- Right-sizing also helps (3 CKU → 2 eCKU)

**Requirements:**
- Must be on AWS
- Must be in region where PNI is available
- Network team must approve Private Link / PNI setup

**Risks:** Low - clear financial case

---

## COST CALCULATION FORMULAS

### Basic Enterprise Monthly Cost Formula

```
Enterprise Monthly Cost = 
  (Base eCKU × eCKU Hourly Rate × 730 hours)
  + (Auto-Scaling Hours × Additional eCKU × eCKU Hourly Rate)
  + (Storage GB × $0.10)
  + Networking Costs
```

### Auto-Scaling Cost Estimation

```
Auto-Scaling Cost = 
  (Hours per Month Above Baseline) 
  × (Average Additional eCKU During Spikes) 
  × (eCKU Hourly Rate)
```

**Example:**
- Daily spikes: 8 hours/day × 30 days = 240 hours/month
- Average additional eCKU during spikes: 2
- eCKU hourly rate: $3/hour (example)
- Auto-scaling cost: 240 × 2 × $3 = **$1,440/month**

### Payback Period Formula

```
Payback Period (months) = 
  Migration Overlap Cost 
  ÷ Monthly Savings
```

**Example:**
- Overlap cost: $10,000
- Monthly savings: $2,000
- Payback: $10,000 ÷ $2,000 = **5 months**

---

## PRIORITIZATION MATRIX

Based on the scenarios above, prioritize migrations by:

### TIER 1: Migrate ASAP (Highest ROI)
- ✅ Over-provisioned clusters (Scenario 4)
- ✅ Spiky workloads (Scenario 2)
- ✅ AWS with high networking costs (Scenario 6)

**Characteristics:**
- >40% cost savings
- <3 month payback
- Clear operational benefits

---

### TIER 2: Good Candidates (Solid ROI)
- ✅ Partition-heavy workloads (Scenario 3)
- ✅ Small steady workloads (Scenario 1)

**Characteristics:**
- 20-40% cost savings
- 3-6 month payback
- Moderate complexity

---

### TIER 3: Evaluate Carefully (Marginal ROI)
- ⚠️ High-throughput steady workloads (Scenario 5)

**Characteristics:**
- <20% savings (or savings mostly from pricing, not auto-scaling)
- >6 month payback
- High complexity

**Decision factors:** Weigh migration effort vs. savings, consider negotiating better Dedicated pricing instead

---

## COST ESTIMATION CHECKLIST

Before presenting costs to customer, verify:

- [ ] Gathered last 90 days of usage metrics (not just one month)
- [ ] Identified average AND peak throughput
- [ ] Counted total partitions across all topics
- [ ] Determined spike frequency and duration
- [ ] Checked current Dedicated CKU count and SLA
- [ ] Estimated storage retention needs
- [ ] Identified if AWS (for PNI savings)
- [ ] Calculated current monthly Dedicated cost (including networking)
- [ ] Used Confluent's official pricing calculator (not just estimates here)
- [ ] Included migration overlap cost in ROI calculation
- [ ] Calculated payback period
- [ ] Identified any features needed that Enterprise doesn't have (could affect timeline/cost)

---

## TIPS FOR CUSTOMER COST CONVERSATIONS

**1. Always show Total Cost of Ownership (TCO), not just Kafka costs:**
- Include networking charges
- Include operational effort savings (automation)
- Include storage costs

**2. Be transparent about overlap costs:**
- Don't hide the fact they'll pay for both clusters briefly
- Show payback period to demonstrate it's worth it

**3. Use conservative estimates:**
- Better to under-promise and over-deliver on savings
- If uncertain about auto-scaling, estimate on the higher side

**4. Offer pilot approach:**
- "Let's migrate one non-prod cluster first to validate savings assumptions"
- Reduces risk of cost surprises

**5. Provide cost range, not single number:**
- "Enterprise will cost $4,000-$5,000/month depending on actual spike patterns"
- Builds trust, acknowledges uncertainty

**6. Compare apples to apples:**
- If Dedicated is over-provisioned, compare to "right-sized Dedicated" AND actual current spend
- Shows migration savings + right-sizing benefits separately

---

**Use these scenarios as templates - adjust numbers to match your customer's actual situation!**
