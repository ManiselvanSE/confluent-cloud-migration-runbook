# Customer Presentation Kit
## Confluent Dedicated to Enterprise Migration

**Created:** April 20, 2026  
**Purpose:** Help you successfully present and guide customers through Dedicated → Enterprise migration

---

## 📦 WHAT'S IN THIS KIT

This kit contains **4 comprehensive resources** to prepare you for customer conversations:

### 1️⃣ **Customer Objections & Response Guide**
📄 `01_Customer_Objections_Response_Guide.md`

**What it is:** A playbook for handling every common objection customers raise

**Includes:**
- 19 major objections across cost, technical, business, and migration categories
- Proven responses with examples and supporting data
- Framework for objection handling (acknowledge → clarify → educate → reframe → validate)
- When to walk away guidance

**Use it when:**
- Preparing for customer meetings (read in advance)
- Customer raises an objection (reference during call)
- Coaching team members on objection handling

---

### 2️⃣ **Presentation Delivery Script**
📄 `02_Presentation_Delivery_Script.md`

**What it is:** A slide-by-slide script for delivering the PowerPoint presentation

**Includes:**
- Word-for-word talking points for each slide
- Discovery questions to ask customers
- Timing guidance (45-60 minutes total)
- Transition phrases between slides
- Post-presentation follow-up checklist

**Use it when:**
- Practicing your presentation delivery
- Delivering the presentation to customers (open on second screen)
- Training new team members on presentation flow

**Tip:** Don't read it verbatim - internalize the key points and speak naturally

---

### 3️⃣ **Sample Cost Calculations**
📄 `03_Sample_Cost_Calculations.md`

**What it is:** 6 realistic cost scenarios with detailed calculations

**Includes:**
- Small steady workload (23% savings)
- Medium spiky workload (45% savings)
- Large partition-heavy workload (44% savings)
- Over-provisioned legacy cluster (59-84% savings!)
- High-throughput steady workload (43% savings - marginal case)
- AWS with high networking costs (61% savings)
- Cost formulas and estimation methods
- Prioritization matrix for which customers to target first

**Use it when:**
- Customer asks "how much will I save?"
- Building custom cost estimates for prospects
- Prioritizing which customers to approach first
- Validating assumptions with Confluent pricing team

**Note:** Numbers are illustrative - always use Confluent's pricing calculator for real quotes

---

### 4️⃣ **Customer FAQ Document**
📄 `04_Customer_FAQ_Document.md`

**What it is:** A comprehensive 35-question FAQ you can send to customers

**Includes:**
- General questions (What is Kafka? Why migrate?)
- Technical questions (Cluster Linking, networking, features)
- Migration process (rollback, validation, tools)
- Cost & pricing (estimates, overlap costs, hidden costs)
- Security & compliance (multi-tenancy, BYOK, data residency)
- Performance (latency, auto-scaling, partition limits)
- Support & troubleshooting (common issues, getting help)

**Use it when:**
- Customer requests written materials to review internally
- Following up after presentation (send as reference)
- Customer has technical team asking detailed questions
- Building internal knowledge base for your team

**Format:** Professional, customer-ready document (can be sent as-is or customized)

---

## 🎯 HOW TO USE THIS KIT

### **BEFORE the Customer Meeting:**

**1 week before:**
- [ ] Read the **Objection Guide** cover-to-cover (internalize common objections)
- [ ] Review the **Delivery Script** and practice presenting (out loud!)
- [ ] Study the **Cost Calculations** to understand pricing scenarios
- [ ] Familiarize yourself with the **FAQ** so you know what's covered

**1 day before:**
- [ ] Review the customer's current Dedicated setup (CKU count, workload type)
- [ ] Identify which **Cost Scenario** most closely matches their situation
- [ ] Prepare 3-5 **Discovery Questions** from the script to ask them
- [ ] Print or have open on second screen: **Delivery Script** + **Objection Guide**

**30 minutes before:**
- [ ] Open all 4 documents for quick reference
- [ ] Open Confluent pricing calculator in background tab
- [ ] Have notepad ready for capturing their responses

---

### **DURING the Customer Meeting:**

**Opening (5 min):**
- Use **Discovery Questions** from the Delivery Script
- Understand their motivation, concerns, experience level
- Tailor technical depth accordingly

**Presentation (30-40 min):**
- Follow the **Delivery Script** structure (but speak naturally!)
- Use **analogies** from the script to simplify complex concepts
- Pause for questions after each section

**Objection Handling (as they arise):**
- Reference the **Objection Guide** for responses
- Follow the framework: Acknowledge → Clarify → Educate → Reframe → Validate

**Cost Discussion (10 min):**
- Reference the **Cost Scenarios** that match their situation
- Use calculator to build rough estimate on the spot
- Promise detailed estimate as follow-up

**Closing (5 min):**
- Recap key benefits for THEIR situation
- Propose next steps (from Delivery Script)
- Schedule follow-up meeting

---

### **AFTER the Customer Meeting:**

**Within 24 hours:**
- [ ] Send thank-you email
- [ ] Attach the **FAQ Document** for their reference
- [ ] Include presentation PDF
- [ ] Propose next steps with calendar invite

**Within 1 week:**
- [ ] Build detailed cost estimate using their actual metrics
- [ ] Research any technical questions they raised
- [ ] Coordinate with Confluent SA/account team if needed
- [ ] Follow up on next steps

---

## 💡 PRO TIPS

### **For New Presenters:**

1. **Practice out loud** - Present to a colleague or record yourself
2. **Use analogies** - The script has great non-technical comparisons
3. **Ask questions** - Discovery questions help you tailor the message
4. **Don't oversell** - Be honest if Enterprise isn't right fit
5. **Reference the FAQ** - If you don't know an answer, "Let me check the FAQ and follow up"

---

### **For Experienced Presenters:**

1. **Customize the script** - Adapt to your personal style
2. **Build your own scenarios** - Add cost calculations for your customer base
3. **Update the FAQ** - Add questions you frequently encounter
4. **Share learnings** - What objections are you seeing? Add to the guide

---

### **For Sales/Account Managers:**

1. **Use Cost Scenarios for prioritization** - Target Tier 1 customers first (biggest ROI)
2. **Lead with value** - "I've identified potential $50K/year savings for you"
3. **Offer pilot** - "Let's test with one non-prod cluster to prove it out"
4. **Bring in technical help** - Use these docs to prep SAs/SEs before calls

---

### **For Technical/Solutions Architects:**

1. **Deep-dive technical sections** - Networking, Flink, connectors
2. **Validate assumptions** - Use FAQ to set realistic expectations
3. **Build custom runbooks** - Adapt migration process for specific customers
4. **Document lessons learned** - Update these docs based on real migrations

---

## 📚 QUICK REFERENCE

### **Most Common Objections (Be Ready!):**

1. **"Too risky / Can't afford downtime"**
   → Response: Page 4 of Objection Guide (Cluster Linking = zero-downtime)

2. **"Too expensive / Overlap costs"**
   → Response: Page 2 of Objection Guide + Cost Scenarios (show payback)

3. **"Multi-tenant security concerns"**
   → Response: Page 11 of Objection Guide (certifications, isolation)

4. **"Don't have time / Too busy"**
   → Response: Page 14 of Objection Guide (phased approach, ROI of waiting)

5. **"We use KSQL heavily"**
   → Response: Page 5 of Objection Guide (Flink migration options)

---

### **Best Cost Scenarios to Lead With:**

**For Spiky Workloads (e-commerce, retail):**
→ Scenario 2: 45% savings, auto-scaling benefit

**For Over-Provisioned Clusters:**
→ Scenario 4: 59-84% savings, fastest payback

**For AWS Customers:**
→ Scenario 6: 61% savings, PNI networking benefit

**For Steady Workloads:**
→ Scenario 1 or 5: 20-40% savings, set realistic expectations

---

### **Key Numbers to Remember:**

- **Typical savings:** 20-60%
- **Typical timeline:** 4-11 weeks
- **Typical overlap:** 2-6 weeks
- **Typical payback:** 2-6 months
- **Partition difference:** eCKU has 1,500 FEWER partitions than CKU
- **SLA options:** 99.9% (1 eCKU min) or 99.99% (2 eCKU min)

---

## 🔄 KEEPING THIS KIT UPDATED

**This kit should evolve based on:**

✅ New objections you encounter → Add to Objection Guide  
✅ New cost scenarios from real customers → Add to Cost Calculations  
✅ New questions customers ask → Add to FAQ  
✅ Presentation improvements → Update Delivery Script

**Suggested review cycle:** Quarterly

**Owner:** [Your Name/Team]  
**Last Updated:** April 20, 2026  
**Next Review:** July 2026

---

## 📞 GETTING HELP

**If you need support using this kit:**

- **Internal questions:** [Your team lead/manager]
- **Confluent product questions:** Your Confluent SA or account team
- **Pricing/quoting questions:** Confluent sales operations
- **Technical deep-dives:** Engage Confluent Solutions Architecture team

---

## ✅ PRE-CALL CHECKLIST

Print this and use before every customer meeting:

**Preparation:**
- [ ] Read customer's current Dedicated setup (CKU, workload, region)
- [ ] Identify which cost scenario matches their profile
- [ ] Review relevant sections of Objection Guide
- [ ] Prepare 3-5 discovery questions
- [ ] Open Delivery Script on second screen
- [ ] Have FAQ ready to reference
- [ ] Pricing calculator loaded in background

**Materials to Share:**
- [ ] Presentation deck (PDF)
- [ ] FAQ document
- [ ] Feature comparison matrix link
- [ ] Confluent pricing calculator link

**Follow-Up:**
- [ ] Thank-you email template ready
- [ ] Cost estimate worksheet prepared
- [ ] Calendar invite for next meeting ready

---

## 🎬 EXAMPLE FLOW (30-MINUTE PITCH)

**Minutes 0-5:** Discovery
- "Tell me about your current Kafka setup - how many clusters, what workload types?"
- "What's driving your interest in Enterprise?"
- "What concerns you most about a migration?"

**Minutes 5-10:** Why Migrate (Slide 3)
- Focus on THEIR pain point (cost, spikes, or networking)
- Show relevant cost scenario
- "Companies like yours typically save 30-50%"

**Minutes 10-15:** Migration Process (Slides 7-10)
- "Here's how we'd do this with zero downtime..."
- Emphasize Cluster Linking (safety net)
- "Typical timeline is 4-8 weeks"

**Minutes 15-20:** Watch-Outs (Slides 5-6)
- "A few things to plan for: networking changes, Flink validation..."
- Address their specific concerns proactively

**Minutes 20-25:** Cost Estimate
- Pull up cost scenario that matches
- "Based on similar customers, here's what I'd estimate..."
- "We can build a detailed estimate with your actual metrics"

**Minutes 25-30:** Next Steps
- "Does this sound like something you want to explore?"
- Propose: Discovery call → Cost estimate → Pilot migration
- Schedule follow-up

---

## 🚀 SUCCESS METRICS

Track these to measure effectiveness:

**Presentation Metrics:**
- % of customers who agree to next steps
- % of customers who request cost estimate
- % who proceed to pilot migration

**Objection Metrics:**
- Most common objections encountered
- Objections that killed deals (update guide!)
- Objections successfully overcome

**Migration Metrics:**
- Time from first presentation to completed migration
- Actual savings vs. estimated savings
- Customer satisfaction post-migration

---

**Good luck with your customer presentations!**

**Remember:** Your goal isn't just to "sell Enterprise" - it's to help customers make the right decision for their situation. If Enterprise isn't right, say so. Integrity builds trust.

---

**Questions or feedback on this kit?** Contact [your email/team]
