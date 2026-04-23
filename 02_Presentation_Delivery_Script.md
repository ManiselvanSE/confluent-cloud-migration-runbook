# Presentation Delivery Script
## Dedicated to Enterprise Migration

**Total Duration: 45-60 minutes (30 min presentation + 15-30 min Q&A)**

---

## PRE-PRESENTATION (5 minutes before start)

### Room Setup Checklist
- [ ] Presentation loaded and tested
- [ ] Screen sharing working
- [ ] Whiteboard/virtual whiteboard available for diagrams
- [ ] Customer contact list confirmed (who's attending?)
- [ ] Your contact info ready to share
- [ ] Cost calculator/tools ready in background tabs
- [ ] Notepad for capturing questions/action items

### Opening Small Talk (as people join)
*"Thanks for joining today. Have you worked with Kafka migrations before, or is this your first look at moving between Confluent clusters?"*

**[Listen and gauge their experience level - adjust technical depth accordingly]**

---

## SLIDE 1: TITLE SLIDE (2 minutes)

### Opening Statement

*"Good morning/afternoon everyone. Thanks for taking the time today. My name is [YOUR NAME] from [YOUR COMPANY/ROLE], and today we're going to walk through migrating from Confluent Dedicated clusters to Enterprise clusters."*

**Pause - make eye contact**

*"Before we dive in, I'd love to understand your current situation better:"*

### Discovery Questions (CRITICAL - sets the stage)

Ask 2-3 of these:

1. *"Are you currently running Confluent Dedicated clusters? How many?"*
   - **[Listen for: cluster count, environment types, scale]**

2. *"What's driving your interest in Enterprise? Cost, auto-scaling, something else?"*
   - **[Listen for: their motivation - tailor presentation to their pain point]**

3. *"How familiar is your team with Kafka concepts? Should I start with fundamentals or dive into technical details?"*
   - **[Listen for: experience level - adjusts depth of analogies needed]**

4. *"What concerns you most about a migration like this?"*
   - **[Listen for: objections early - address proactively during presentation]**

### Transition

*"Perfect. That context helps a lot. Here's what we'll cover today:"*

**[Briefly gesture to agenda slide coming next]**

*"We'll walk through why customers migrate, what to check before starting, potential pitfalls, the step-by-step process, and how to estimate costs. I'll pause for questions throughout, but feel free to interrupt anytime."*

**[Advance to next slide]**

---

## SLIDE 2: AGENDA (2 minutes)

### Delivery

*"Here's our roadmap for the next 30-40 minutes:"*

**[Read each item with context]:**

1. *"First, common reasons for migration - **why** customers do this"*
2. *"Second, a migration checklist - **prerequisites** to verify upfront"*
3. *"Third, watch-outs - **pitfalls** others have hit that you can avoid"*
4. *"Fourth, the migration path - **step-by-step process** we recommend"*
5. *"Finally, estimating costs - **how to calculate** what this will cost in your environment"*

### Set Expectations

*"This presentation is designed to give you the full picture - the good, the challenging, and the realistic timeline. My goal is that by the end, you'll know whether Enterprise makes sense for your use case and what it takes to get there."*

**Pause**

*"Sound good? Any questions before we jump in?"*

**[Address any questions, then advance]**

---

## SLIDE 3: COMMON DRIVING FACTORS (5 minutes)

### Introduction

*"Let's start with **why** customers migrate from Dedicated to Enterprise. Usually it's one or more of three reasons..."*

---

### REASON 1: COSTS

**[Point to bullet 1]**

*"First - and most common - is **cost savings**."*

**Explain simply:**

*"Dedicated clusters work like renting a dedicated server - you pay for capacity 24/7, whether you're using it fully or not. Enterprise is more like serverless - you set a minimum baseline, but it scales up and down with your actual usage."*

**Analogy:**

*"Think of it like your cell phone plan. Dedicated is an unlimited plan even if you only use 2GB. Enterprise is pay-as-you-go - you pay for what you use, with a small minimum."*

**Key point:**

*"On average, customers save 20-40% moving to Enterprise for similar workloads."*

**Pause - let that land**

*"There's also an internal note here for transparency:"* **[read the margin comment]** *"Enterprise has better profit margins for Confluent, which means we can offer you higher discounts during negotiations. It's a win-win."*

---

### REASON 2: SPIKY WORKLOADS

**[Point to bullet 2]**

*"Second reason: **spiky workloads** - meaning your traffic isn't constant throughout the day or week."*

**Explain the problem:**

*"With Dedicated, if you have a Black Friday traffic spike, you need to manually scale up capacity before the event, then scale back down after. That's operational overhead and you're paying for that peak capacity even during low periods."*

**The solution:**

*"Enterprise auto-scales. When traffic spikes, it adds capacity automatically within seconds. When traffic drops, it scales back down. You only pay for that extra capacity during the actual spike hours, not the whole month."*

**Important caveat:**

*"Now, there's a nuance here:"* **[read the note]** *"This only really matters if your spikes go above your baseline CKU limits. If your spikes are small and stay within your normal capacity, auto-scaling doesn't help much."*

**Check for understanding:**

*"Does your workload have significant traffic spikes - like 2-3x variations throughout the day or week?"*

**[Listen - if yes, emphasize this benefit. If no, move on quickly]**

---

### REASON 3: HIGH NETWORKING COSTS (AWS)

**[Point to bullet 3]**

*"Third reason, and this is AWS-specific: **reducing networking costs**."*

**Explain:**

*"If you're on AWS, moving data across availability zones or regions costs money - sometimes a LOT. With the introduction of something called PNI - Private Network Interface - Enterprise customers can significantly reduce these data transfer charges."*

**Quick check:**

*"Are you running on AWS?"*

**[If yes]:** *"This could be a big savings driver for you. We should look at your current networking costs when we estimate savings."*

**[If no]:** *"This won't apply to you on Azure or GCP, so focus on the first two reasons."*

---

### Transition

*"So those are the three main drivers: **lower costs, automatic scaling for spiky workloads, and networking savings for AWS**. Any questions on the 'why' before we talk about the prerequisites?"*

**[Pause for questions - address them]**

**[Advance slide]**

---

## SLIDE 4: MIGRATION CHECKLIST (4 minutes)

### Introduction

*"Alright, before you jump into a migration, there are four things you need to verify. Think of this as the 'go/no-go' checklist."*

---

### ITEM 1: MULTI-TENANT ENVIRONMENT

**[Point to bullet 1]**

*"First: **Are you okay with a multi-tenant environment?**"*

**Explain:**

*"Enterprise runs on shared infrastructure - meaning multiple customers share the same underlying servers, though your data is completely isolated and encrypted. Think of it like an apartment building vs. a standalone house. Same building, but your unit is private and secure."*

**Address security concerns proactively:**

*"Enterprise is certified for SOC 2, ISO 27001, HIPAA, PCI-DSS - all major compliance frameworks. But some organizations have policies against any multi-tenancy. If that's you, it's a blocker."*

**Ask:**

*"Do you have any compliance or security policies that would prevent multi-tenant deployments?"*

**[Listen - if concern, note it for follow-up]**

---

### ITEM 2: FEATURE PARITY

**[Point to bullet 2]**

*"Second: **Check that Enterprise has all the features you're using**."*

**Explain:**

*"Enterprise has most Dedicated features, but not 100%. There's a comparison matrix linked here:"* **[point to "here" link]** *"that shows what's available and what's not."*

**Common gaps:**

*"Most customers are fine, but there are some edge cases - specific networking configurations, certain security setups, etc."*

**Action item:**

*"After this call, I recommend reviewing that matrix against your current Dedicated cluster configuration. If you find something you need that's missing, we can discuss alternatives or roadmap timeline."*

---

### ITEM 3: NETWORKING CHANGES (PEERING/TGW USERS)

**[Point to bullet 3]**

*"Third: **If you're using VPC Peering or Transit Gateway for private networking, you'll need to switch to Private Link**."*

**Explain the difference:**

*"Dedicated typically uses VPC Peering or TGW - which are direct network connections. Enterprise uses Private Link or Private Network Interface - which are endpoint-based connections."*

**Analogy:**

*"Peering is like building a private road between two buildings. Private Link is like having a secure doorway in your building that leads to ours. Different architecture, same goal - private connectivity."*

**Why it matters:**

*"This especially affects **connectors** - those tools that move data between Kafka and databases, S3, etc. They need to be reconfigured for the new networking model."*

**Ask:**

*"Are you currently using VPC Peering or Transit Gateway?"*

**[If yes]:** *"Okay, we'll need to plan for networking changes. Your network team will need to be involved."*

**[If no]:** *"Great, one less thing to worry about."*

---

### ITEM 4: KSQL USAGE

**[Point to bullet 4]**

*"Fourth: **Are you using KSQL?**"*

**The situation:**

*"KSQL isn't available in Enterprise. The replacement is Flink - which is newer, more powerful, and the strategic direction even for Dedicated."*

**Ask:**

*"Do you have KSQL queries running in production?"*

**[If yes]:** *"How many queries are we talking about? Simple or complex?"*

**[Listen, then respond]:**

*"The migration from KSQL to Flink ranges from straightforward to moderate effort, depending on complexity. Confluent has migration guides and can help with conversion. We should budget time for this in the project plan."*

**[If no]:** *"Perfect, that's one less complexity."*

---

### Transition

*"So that's your checklist: **multi-tenant okay, features available, networking change acceptable, KSQL migration plan**. If all four check out, you're good to proceed. Any questions on these prerequisites?"*

**[Pause for questions]**

**[Advance slide]**

---

## SLIDE 5: MIGRATION WATCH-OUTS - NETWORKING & FLINK (5 minutes)

### Introduction

*"Now let's talk about **watch-outs** - things that have tripped up other customers that you can avoid by planning ahead. These are a bit more technical, so let me know if I should slow down or clarify anything."*

---

### SECTION 1: NETWORKING / DNS

**[Point to "Networking/DNS" section]**

*"First watch-out: **networking and DNS changes**. This is often the most complex part of the migration."*

**Explain Enterprise networking:**

*"Enterprise uses something called **Ingress Private Link Gateways** and **Access Points**. Think of these as secure entry gates to your cluster. Each service - Kafka, Flink, Schema Registry - gets its own specific endpoint URL."*

**Contrast with Dedicated:**

*"Dedicated uses simpler routing - one main connection, everything flows through it. Enterprise is more modular - separate endpoints for each service, which gives you more flexibility but requires more configuration."*

**What you need to do:**

**[Point to bullets]**

1. *"**Validate connectivity**: Make sure your applications can reach all the new Access Point endpoints from your VPC"*
2. *"**Design DNS**: Set up private hosted zones - basically internal DNS records - for the new endpoint patterns"*
3. *"**Update Terraform**: If you use Terraform for infrastructure-as-code, make sure you're on version 2.60 or higher and use the new resource types"*

**Simplify:**

*"In practice, this means: your network team sets up the Private Link connections, creates DNS entries, and your app teams update connection URLs in their configs. It's configuration changes, not code changes."*

**Ask:**

*"Do you use Terraform or other infrastructure-as-code tools?"*

**[Listen and note]**

---

### SECTION 2: FLINK

**[Point to "Flink" section]**

*"Second watch-out: **Flink networking**."*

**Background:**

*"If you're using Flink for stream processing, you need to make sure Flink compute pools can reach Enterprise Kafka and Schema Registry through Private Link and Access Points."*

**The gotcha:**

*"Flink private networking is newer and might not be Generally Available in all cloud providers or regions yet. You need to check your specific cloud/region before committing to migration dates."*

**Action item:**

*"We should verify Flink private networking GA status for:"* **[ask]** *"Which cloud and region are you running in?"*

**[Note their response]**

*"I'll check the current GA status for that and follow up after this call."*

---

### Transition

*"Those are the two big networking watch-outs. Any questions on this before we talk about Tableflow and Schema Registry?"*

**[Pause for questions]**

**[Advance slide]**

---

## SLIDE 6: MIGRATION WATCH-OUTS - TABLEFLOW & SCHEMA REGISTRY (4 minutes)

### SECTION 1: TABLEFLOW

**[Point to "Tableflow" section]**

*"Next watch-out: **Tableflow** - which is Confluent's service for exporting Kafka data to data lakes like S3, Iceberg, or Snowflake."*

**Typical flow:**

*"If you're using Tableflow, your data usually flows like this: **Kafka → Flink → Tableflow → Iceberg/S3**. All these pieces need to stay connected and compatible."*

**Watch-out #1:**

*"Tableflow still reaches Schema Registry via **public endpoint** - not private. This is by design for now. If you have strict requirements about all traffic being private, this might be a discussion point."*

**Watch-out #2:**

*"Enterprise has **per-topic pricing** for Tableflow in some cases, vs. cluster-wide pricing in Dedicated. If you're exporting lots of topics with sensitive data, costs might differ. We should check this in the cost estimate."*

**Watch-out #3:**

*"**Schema evolution** - when you change your data structure, you need to make sure Kafka, Schema Registry, Flink, Tableflow, and Iceberg all stay in sync. Plan for schema compatibility testing."*

**Ask:**

*"Are you currently using Tableflow or exporting to data lakes?"*

**[If yes]:** *"Okay, we'll need to include that in the migration plan and cost estimate."*

**[If no]:** *"Great, one less component to migrate."*

---

### SECTION 2: SCHEMA REGISTRY

**[Point to "Schema Registry" section]**

*"Last watch-out: **Schema Registry endpoint changes**."*

**For Dedicated users:**

*"If you're on Dedicated with private networking, you already have a private Schema Registry endpoint. You just need to update app configs from the public URL to the private URL."*

**For Enterprise users:**

*"Enterprise uses **Access-Point-specific Schema Registry endpoints**. That means different URLs per Access Point."*

**The important note:**

**[Read carefully]**

*"Internal Confluent components - like Flink, connectors, Tableflow, and ksqlDB - still use the **public Schema Registry** and ignore IP filters. So if you're setting IP filters thinking everything goes through them, be aware that internal services bypass them."*

**Clarify:**

*"This is by design for operational reasons, and traffic is encrypted. But if your security model requires all Schema Registry access to go through IP filters, we need to discuss."*

---

### Transition

*"Those are the main watch-outs: **networking setup, Flink compatibility, Tableflow connections, and Schema Registry endpoints**. The good news is - none of these are blockers, they're just things to plan for. Questions?"*

**[Pause for questions]**

**[Advance slide]**

---

## SLIDE 7: MIGRATION PATH - DISCOVERY & PROVISIONING (6 minutes)

### Introduction

*"Alright, now the fun part - **how do we actually do this migration?** Let's walk through the seven-step process, starting with discovery and sizing."*

---

### STEP 1: DISCOVERY & SIZING

**[Point to Step 1]**

*"**Step 1 is all about measuring your current state** so we know how big to make the Enterprise cluster."*

**Sub-step 1: Capture current usage**

*"You'll pull these metrics from your Dedicated cluster:"*

**[Point to each]:**
- *"**Peak ingress/egress MBps** - how much data you're reading and writing at peak times"*
- *"**Partition count** - how many parallel data streams you have"*
- *"**Storage** - how much data you're retaining"*
- *"**Cluster load metric** - how hard your cluster is working"*

**Practical tip:**

*"This is all available in the Confluent Cloud console under Metrics. Look at the last 30-90 days to understand patterns - don't just take a snapshot from today."*

---

**Sub-step 2: Choose SLA → minimum eCKU**

*"Based on those metrics, you choose two things: **SLA and minimum eCKU**."*

**Explain SLA:**

*"SLA is Service Level Agreement - basically, uptime guarantee. Enterprise offers two options:"*

**[Point to bullets]:**

1. *"**99.9% SLA with 1 eCKU minimum** - This means ~43 minutes of downtime allowed per month. Good for non-production or cost-sensitive workloads."*

2. *"**99.99% SLA with 2+ eCKU minimum** - This means ~4 minutes of downtime allowed per month. Recommended for production."*

**Key concept:**

*"This minimum is the **floor, not the ceiling**. Enterprise auto-scales above that as your workload grows. Unlike Dedicated where you pre-size for peak capacity, Enterprise you size for baseline and let it scale."*

**Analogy:**

*"It's like minimum staffing at a restaurant. You always have at least 2 servers (minimum eCKU), but more show up automatically during dinner rush (auto-scale)."*

**Ask:**

*"Do you know your current peak vs. average throughput? Are your workloads spikey or pretty steady?"*

**[Listen and note]**

---

### STEP 2: PROVISION THE ENTERPRISE CLUSTER

**[Point to Step 2]**

*"**Step 2 is building the new Enterprise cluster** while your Dedicated cluster keeps running."*

**Sub-step 1: Create resources**

*"Using either Terraform or the Confluent Cloud UI, you create:"*

**[Point to bullets]:**
- *"The **Environment** (if you need a new one) - think of this like a project folder"*
- *"The **Enterprise cluster** with the eCKU and SLA you chose"*
- *"**Private networking** - matching whatever pattern you use: Private Link, PNI, Peering, or PSC"*

---

**Sub-step 2: Reuse or create security configs**

*"You also set up:"*
- *"**Service accounts** - credentials for applications to connect"*
- *"**RBAC and ACLs** - who can do what, permissions"*
- *"**Schema Registry private endpoints** - if you're using private networking"*

**Important note:**

*"You can often **reuse** service accounts and roles from Dedicated - you don't always have to recreate everything from scratch. Terraform makes this easier to template out."*

---

### Transition

*"So after Steps 1 and 2, you have: **Dedicated cluster still running, Enterprise cluster ready and configured**. Two clusters, side by side. Questions so far?"*

**[Pause for questions]**

**[Advance slide]**

---

## SLIDE 8: MIGRATION PATH - CLUSTER LINKING & APP MIGRATION (7 minutes)

### STEP 3: ESTABLISH CLUSTER LINKING

**[Point to Step 3]**

*"**Step 3 is the magic: Cluster Linking**. This is what makes zero-downtime migration possible."*

**Explain Cluster Linking:**

*"Cluster Linking is a live synchronization bridge between your Dedicated and Enterprise clusters. Data flows from Dedicated to Enterprise in real-time, automatically."*

**Analogy:**

*"Think of it like setting up two mirrors that sync continuously - whatever appears in the first mirror instantly appears in the second."*

---

**Sub-step 1: Create the link**

*"From the Enterprise cluster (destination), you create a cluster link to the Dedicated cluster (source). This needs to use private networking if both clusters support it."*

---

**Sub-step 2: Configure mirroring**

**[Point to bullets]:**

*"You configure:"*
- *"**Mirror topics** - choose which topics to copy (all or selected)"*
- *"**Consumer offset sync** - this copies not just the data, but also where consumers left off reading, so they can resume seamlessly"*

---

**Sub-step 3: Let it backfill and monitor**

*"The link starts copying historical data (backfill) and then stays live, continuously copying new data as it arrives."*

*"You monitor **link lag** - which tells you how far behind the mirror is. When lag approaches zero, the clusters are in sync."*

**Important:**

*"During this entire time, your Dedicated cluster is still serving production traffic. Nothing has changed for your applications yet. Enterprise is just a passive copy."*

---

### STEP 4: MIGRATE CONSUMERS & PRODUCERS

**[Point to Step 4]**

*"**Step 4 is the actual cutover** - moving applications from Dedicated to Enterprise."*

**Explain Producers vs. Consumers:**

*"Quick vocab: **Producers** write data to Kafka (like an order service). **Consumers** read data from Kafka (like an analytics dashboard)."*

---

**Sub-step 1: Pilot cutover**

*"Start small with a **pilot**:"*

**[Point to bullets]:**

1. *"Pick one consumer group (one application)"*
2. *"Stop it on Dedicated"*
3. *"Point it to the Enterprise mirror topic"*
4. *"Resume and validate it works correctly"*

**Why pilot?**

*"This lets you find any configuration issues, connectivity problems, or gotchas with ONE application, not all of them. Low risk, high learning."*

---

**Sub-step 2: Full cutover**

*"Once the pilot succeeds, you do the full cutover:"*

**[Point to bullets, read in order]:**

1. *"**Stop all remaining producers and consumers** on Dedicated - freeze writes"*
2. *"**Wait for cluster link lag to hit zero** - confirms all data copied over"*
3. *"**Promote mirror topics on Enterprise** - this makes them writable (before promotion they're read-only mirrors)"*
4. *"**Reconfigure all clients** to Enterprise bootstrap URLs and credentials"*

**Emphasize:**

*"The order matters. If you promote topics while producers are still writing to Dedicated, the two clusters will diverge and you'll have data in two places. That's why you stop, wait for sync, THEN promote."*

---

**Sub-step 3: Keep the link alive**

*"After cutover, keep the cluster link running for a few days or weeks as a safety net. If something goes wrong, you can point apps back to Dedicated quickly."*

---

### Timeline Reality Check

*"How long does this take?"*

**Typical timeline:**
- *"Establishing link and backfill: **Hours to 1 day** (depends on data volume)"*
- *"Pilot cutover: **1-3 days** (testing and validation)"*
- *"Full cutover: **1-5 days** (phased rollout of all apps)"*

---

### Transition

*"So with Cluster Linking, you migrate with zero downtime. Dedicated stays up the whole time. Any questions on the cluster linking or cutover process?"*

**[Pause for questions]**

**[Advance slide]**

---

## SLIDE 9: MIGRATION PATH - FLINK & CONNECTORS (6 minutes)

### STEP 5: MIGRATE FLINK JOBS

**[Point to Step 5]**

*"**Step 5 is migrating Flink jobs** - if you're using Flink for stream processing."*

**Quick check:**

*"Are you using Flink currently?"*

**[If no]:** *"Great, skip this step. If yes, here's the approach:"*

---

**Sub-step approach:**

**[Point to bullets]:**

1. *"**Duplicate** Flink tables and jobs pointing to Enterprise topics - don't delete the old ones yet"*
2. *"**Run in shadow mode** - the new job reads from Enterprise mirrors and produces output, but you compare output against the Dedicated job to validate they match"*
3. *"**Switch downstream consumers** - once validated, point consumers to Flink outputs from Enterprise instead of Dedicated"*
4. *"**Decommission old jobs** - shut down the Flink jobs reading from Dedicated"*

**Analogy:**

*"It's like running two assembly lines in parallel, making sure the new line produces identical products, then shutting down the old one."*

---

### STEP 6: MIGRATE MANAGED CONNECTORS

**[Point to Step 6]**

*"**Step 6 is migrating connectors** - those pre-built integrations that move data between Kafka and other systems."*

**Explain connectors:**

*"Connectors come in two types:"*
- *"**Source connectors** pull data INTO Kafka (like from MySQL or Salesforce)"*
- *"**Sink connectors** push data OUT of Kafka (like to S3 or Snowflake)"*

**Quick check:**

*"How many connectors are you running?"*

**[Note the number]**

---

**Step 6.1: Import existing connectors**

*"First, you import/define your existing connectors in Terraform or UI:"*

**[Point to options]:**

- *"**Option A**: Use the Resource Importer tool - auto-generates Terraform config from your existing connectors"*
- *"**Option B**: Manually define them and import into Terraform state"*

*"This creates a blueprint of your current connector setup."*

---

**Step 6.2: Cutover connectors**

*"Then you cutover:"*

**[Point to bullets]:**

1. *"**While Cluster Link is mirroring**, run `terraform apply` to create identical connectors on Enterprise"*
2. *"**Validate**: For sink connectors, check data arrives correctly at destinations. For source connectors, check topics populate on Enterprise."*
3. *"**Once confirmed**, remove Dedicated connector configs and run `terraform apply` to tear down old ones"*

**Important:**

*"You'll briefly run duplicate connectors during the transition - one on Dedicated, one on Enterprise. Once you validate Enterprise connectors work, remove the Dedicated ones."*

---

### Timeline & Effort

*"Connector migration is usually pretty quick - hours to a couple days, depending on how many you have. The tedious part is validating each one works correctly."*

---

### Transition

*"Questions on Flink or connector migration?"*

**[Pause for questions]**

**[Advance slide]**

---

## SLIDE 10: MIGRATION PATH - VALIDATION & DECOMMISSION (3 minutes)

### STEP 7: VALIDATION, TUNING, AND DECOMMISSION

**[Point to Step 7]**

*"**Final step: validate everything works, tune if needed, and decommission the old cluster**."*

---

**Sub-step 1: Monitor**

*"Watch Enterprise cluster metrics:"*
- *"**eCKU utilization** - is it scaling properly?"*
- *"Performance metrics - latency, throughput, error rates"*

**What you're looking for:**

*"You want to see stable performance, no errors, and appropriate eCKU scaling (not constantly maxed out or way under-utilized)."*

---

**Sub-step 2: Confirm migration complete**

*"Verify:"*

**[Point to bullets]:**
- *"All applications using Enterprise endpoints"*
- *"All Flink jobs running on Enterprise"*
- *"All connectors connected to Enterprise"*
- *"All schemas pointing to Enterprise Schema Registry"*

**Practical check:**

*"Look at Dedicated cluster metrics - ingress/egress should be zero or near-zero if everything migrated."*

---

**Sub-step 3: Decommission Dedicated**

*"When you're comfortable - usually after 1-4 weeks of stable Enterprise operation - you shrink or delete the Dedicated cluster."*

**Important note:**

*"Follow Confluent's CKU resize and deletion rules - there may be minimum notice periods or contract terms. Check with your account team."*

**Caution:**

*"Don't rush this step. The cost of running both clusters for an extra week is small compared to the pain of discovering an issue AFTER you've deleted Dedicated and lost your rollback option."*

---

### Transition

*"And that's the full seven-step process. Any questions on the migration path?"*

**[Pause for questions]**

**[Advance slide]**

---

## SLIDE 11: ESTIMATING COSTS (5 minutes)

### Introduction

*"Now let's talk money - how do you estimate what Enterprise will cost?"*

---

### GOOD NEWS

**[Point to first bullet]**

*"First, the good news: **Throughput limits per CKU and eCKU are the same**."*

*"This means you can use your current Dedicated usage to estimate Enterprise usage pretty directly. You don't need to do complex conversion math."*

---

### GENERAL GUIDANCE

**[Point to "General guidance"]**

*"Here's the step-by-step approach:"*

**Step 1: Gather usage data**

*"Create an estimate based on your **last 3 months** of usage - not just one month, so you capture seasonal variations."*

**Step 2: Match throughput**

*"Use your **average throughput**, not peak throughput."*

**Why?**

*"Enterprise auto-scales for peaks, so you size for average. You'll pay hourly for peak capacity only when it's actually used."*

**Step 3: Match SLA**

*"Choose the SLA based on your current Dedicated setup:"*

**[Point to bullets]:**

- *"If Dedicated runs on **1 CKU**, estimate Enterprise at **99.9% SLA** (1 eCKU minimum) - cost-effective option"*
- *"If Dedicated runs on **2+ CKU** (multi-AZ), estimate Enterprise at **99.99% SLA** (2+ eCKU minimum) - production-grade"*

**Explain the logic:**

*"Single-CKU Dedicated is typically non-production, so 99.9% SLA is appropriate. Multi-CKU Dedicated is usually production, so match that with 99.99% SLA on Enterprise."*

---

### EXAMPLE

*"Let's say you run a 2-CKU Dedicated cluster with average throughput of 50 MBps. You'd estimate Enterprise with:"*
- *"50 MBps average throughput"*
- *"99.99% SLA"*
- *"Let auto-scaling handle spikes above 50 MBps"*

*"The estimator will spit out a monthly cost, which you compare to your current Dedicated spend."*

---

### WHERE TO ESTIMATE

*"You can use:"*
1. *"Confluent's pricing calculator on their website"*
2. *"Your account team (they can run estimates for you)"*
3. *"Terraform plan with pricing (if you're using Terraform)"*

---

### Transition

*"Questions on cost estimation before we talk about special cases?"*

**[Pause for questions]**

**[Advance slide]**

---

## SLIDE 12: VARIABLES TO THINK OF (4 minutes)

### Introduction

*"Cost estimation gets trickier if your cluster is **heavily provisioned** or has special characteristics. Let's talk about variables that affect sizing..."*

---

### VARIABLE 1: HEAVILY PROVISIONED CLUSTERS

**[Point to bullet 1]**

*"**If your Dedicated cluster has more than 2 CKUs**, there's usually a specific reason:"*

**[Point to sub-bullets]:**
- *"High **partition count** needs"*
- *"High **throughput** needs"*

*"You need to figure out which one applies to your cluster, because it affects Enterprise sizing differently."*

---

### VARIABLE 2: PARTITIONS

**[Point to "Partitions"]**

*"Here's a gotcha: **Enterprise has 1,500 FEWER partitions per eCKU compared to Dedicated CKU**."*

**The numbers:**
- *"Dedicated: ~4,500 partitions per CKU (example - check docs for exact)"*
- *"Enterprise: ~3,000 partitions per eCKU (example - check docs for exact)"*

**What this means:**

*"If you have a LOT of partitions, you might need more eCKUs on Enterprise just to accommodate partition count, even if throughput is low."*

**Action item:**

*"Count your total partitions across all topics. Match that partition count when sizing Enterprise, not just throughput."*

**Quick check:**

*"Do you know roughly how many partitions you have across all topics?"*

**[Listen and note]**

---

### VARIABLE 3: THROUGHPUT

**[Point to "Throughput"]**

**Scenario A:**

*"If your cluster has >2 CKU and throughput **actually IS high** (legitimately needs that many CKUs), use that high throughput in your Enterprise estimate."*

**Scenario B:**

*"If your cluster has >2 CKU but average throughput is **low** (doesn't need that much capacity), investigate why:"*
- *"Over-provisioned for safety?"*
- *"Provisioned for rare peak events?"*
- *"Historical reasons (was busy in the past, not anymore)?"*

*"Size Enterprise based on **actual usage**, not overprovisioned capacity."*

**Analogy:**

*"If you rented a 5-ton truck but usually only haul 1 ton, buy your new vehicle based on the 1 ton actual need, not the 5 ton you rented 'just in case.'"*

---

### Transition

*"So the key: **Look at partitions AND throughput, not just one or the other**. Questions on these sizing variables?"*

**[Pause for questions]**

**[Advance slide]**

---

## SLIDE 13: EDGE CASES (3 minutes)

### Introduction

*"Last slide on costs: **edge cases** - specifically, highly spiky workloads."*

---

### THE QUESTION

*"If your traffic spikes a lot, do you size Enterprise for **average** throughput or **peak** throughput?"*

**Answer:**

*"It depends on **how often** and **how long** the spikes last."*

---

### CASE 1: FREQUENT, LONG SPIKES

**[Point to first scenario]**

*"**If spikes happen daily and last for hours:**"*

**Example:**

*"E-commerce site busy 10 AM - 10 PM every day, quiet at night."*

**Sizing guidance:**

*"Size Enterprise close to **peak throughput**, not average."*

**Why?**

*"You're hitting peak 12 hours a day, every day. That's not really a 'spike' - that's your normal operational state. The low period is the anomaly."*

---

### CASE 2: INFREQUENT OR SHORT SPIKES

**[Point to second scenario]**

*"**If spikes happen occasionally (weekly) or last minutes, not hours:**"*

**Example:**

*"Payroll system spikes on Friday afternoons only."*

**Sizing guidance:**

*"Size Enterprise based on **average throughput**."*

**Why?**

*"Enterprise auto-scales for brief spikes - you pay hourly for that extra capacity only when used, not all month. It's cheaper to let it auto-scale than to pre-provision."*

---

### RULE OF THUMB

*"If you're above baseline more than 30-40% of the time, include spikes in your baseline estimate. If you're above baseline less than 10% of the time, let auto-scaling handle it."*

---

### Transition

*"Questions on spiky workloads or cost estimation?"*

**[Pause for questions]**

**[Advance to closing slide]**

---

## SLIDE 14: CLOSING (3 minutes)

### Summary

*"Alright, let's recap what we covered:"*

**[Summarize key points]:**

1. *"**Why migrate**: Cost savings, auto-scaling for spiky workloads, networking savings (AWS)"*
2. *"**Prerequisites**: Multi-tenant okay, features available, networking changes, KSQL migration"*
3. *"**Watch-outs**: Networking/DNS, Flink, Tableflow, Schema Registry endpoints"*
4. *"**Migration process**: 7 steps, zero-downtime using Cluster Linking, 4-11 weeks typical timeline"*
5. *"**Cost estimation**: Use last 3 months usage, size for average throughput with auto-scaling"*

---

### CALL TO ACTION

*"So, next steps:"*

**Option 1 - Interested:**

*"If this sounds like something you want to pursue, here's what I recommend:"*

1. *"I'll send you:"*
   - *"Link to the feature comparison matrix"*
   - *"Cost estimation worksheet"*
   - *"FAQ document"*

2. *"You review with your team and gather:"*
   - *"Last 3 months usage metrics from Dedicated"*
   - *"Partition count"*
   - *"List of features you depend on"*

3. *"We schedule a follow-up in 1-2 weeks to:"*
   - *"Review cost estimate together"*
   - *"Discuss any technical blockers"*
   - *"Build a migration project plan if you decide to proceed"*

**Option 2 - Need more info:**

*"If you need more information first, happy to:"*
- *"Arrange a technical deep-dive with Confluent architects"*
- *"Set up a pilot/POC to test Enterprise before committing"*
- *"Connect you with a customer who's done this migration for a reference call"*

**Option 3 - Not ready:**

*"If now isn't the right time, no problem. I'll:"*
- *"Send you the materials to review when you're ready"*
- *"Check in quarterly to see if timing improves"*
- *"Keep you updated on new Enterprise features as they launch"*

---

### ASK

*"Which sounds most appropriate for your situation?"*

**[Listen - tailor next steps to their response]**

---

### FINAL QUESTION

*"Any final questions before we wrap up?"*

**[Answer questions]**

---

### THANK YOU

*"Thank you all for your time today. I'll send a follow-up email with:"*
- *"This presentation deck"*
- *"Links to resources we discussed"*
- *"Proposed next steps based on our conversation"*

*"Looking forward to working with you on this. Have a great rest of your day!"*

---

## POST-PRESENTATION CHECKLIST

**Within 24 hours, send:**
- [ ] Thank you email
- [ ] Presentation deck (PDF)
- [ ] FAQ document
- [ ] Feature comparison matrix link
- [ ] Cost estimation template
- [ ] Calendar invite for follow-up (if scheduled)
- [ ] Internal notes on customer concerns/objections

**Within 1 week:**
- [ ] Cost estimate prepared (if they provided data)
- [ ] Technical questions researched and answered
- [ ] Engage Confluent resources if needed (SA, CSM, etc.)

---

**Good luck with your presentation!**
