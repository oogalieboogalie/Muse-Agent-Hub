# Ghost Architect — Reflection Document
## Post-Engagement Analysis & Knowledge Capture

*Copy this file into `reflections/` as `<target>-<date>.md` and fill it in after every hunt.*

---

## Session Metadata

| Field | Value |
|---|---|
| Target / Program | |
| Date Range | |
| Total Time Invested | |
| Surface Area Covered (subdomains, endpoints, repos) | |
| Primary Technology Stack | |
| Specialist Module(s) Activated | |
| Session Objective (initial recon / API deep dive / primitive scaling) | |

---

## 1. Executive Summary

**One-sentence outcome:**

**Key findings (if any):**
-
-

**Overall assessment:**
- [ ] Fruitful — findings generated
- [ ] Negative but informative — no findings, but architecture mapped
- [ ] Incomplete — need to return
- [ ] Blocked — scope/technical limitation encountered

---

## 2. Reconnaissance Review (Rosen Loop)

### Infrastructure Graph

```
Registrar → NS → CDN → Origin → Internal Services
Third-party integrations:
Trust boundaries:
```

### Technology Fingerprint

| Component | Technology | Version (if known) | Notes |
|---|---|---|---|
| Web Server | | | |
| Framework | | | |
| CDN | | | |
| Auth Provider | | | |
| Database (inferred) | | | |
| Other | | | |

### Attack Surface Inventory

| Asset Type | Count | Key Assets | Notes |
|---|---|---|---|
| Subdomains | | | |
| API Endpoints | | | |
| Mobile Apps | | | |
| Cloud Assets | | | |
| Repositories | | | |
| Other | | | |

### What I Got Wrong
What assumptions about the architecture proved incorrect?

### What Surprised Me
What architectural quirk or design choice was unexpected?

---

## 3. Surface Sweep Review (Lopez Loop)

### "Boring" Bugs Checked
- [ ] Exposed .env / config files
- [ ] Exposed .git directories
- [ ] Public GitHub/GitLab repos
- [ ] Stale subdomains (takeover potential)
- [ ] Default credentials
- [ ] Misconfigured CORS headers
- [ ] Exposed admin panels
- [ ] Directory listing enabled
- [ ] Backup files (.bak, .old, .zip)
- [ ] robots.txt / sitemap.xml analysis
- [ ] Other:

### Shallow Findings (even if not reportable)

| Finding | Location | Why It Matters | Follow-up Needed? |
|---|---|---|---|
| | | | |

### Statistical Notes
- Total requests made:
- Unique endpoints touched:
- Anomalies flagged:
- Time per endpoint (average):

### The Lopez Question
Did I check where others wouldn't? Did I hunt the boring surface with relentless consistency?

---

## 4. Deep Investigation Review (Curry Loop)

### Curiosity Threads Spawned

| Thread ID | Trigger (what was weird?) | Path Followed | Outcome | Time Spent |
|---|---|---|---|---|
| | | | | |

### Anomalies That Went Uninvestigated

| Anomaly | Why I Skipped It | Should I Return? | Priority |
|---|---|---|---|
| | | | |

### Specialist Insights
What domain-specific knowledge proved valuable? What would a general scanner have missed?

### The Curry Question
Did I follow my curiosity? Did I investigate the weird, or did I stick to a checklist?

---

## 5. Primitive Discovery & Scaling (DeVoss Loop)

### New Primitives Discovered

| Primitive ID | Category | Description | Target Conditions | Mutations Generated |
|---|---|---|---|---|
| | | | | |

### Existing Primitives Replayed

| Primitive ID | Target Endpoint | Result | Notes |
|---|---|---|---|
| | | | |

### Cross-Pollination Attempts

| Primitive | Original Class | Tested Against | Result | Notes |
|---|---|---|---|---|
| | | | | |

### Scaling Assessment
Did I find one thing and test it everywhere? Or did I find many things and test each once?

### The DeVoss Question
If I found a bypass or trick, did I apply it across ALL endpoints? Did I generate mutations?

---

## 6. Findings Deep Dive

*Repeat this section for each confirmed finding.*

### Finding: [NAME]

| Attribute | Detail |
|---|---|
| Finding ID | |
| Severity | |
| Vulnerability Class | |
| Primitive Used | |
| Location | |

**How I Found It:**
Narrative of the discovery path. What led you here?

**Why Others Missed It:**
What specific combination of factors made this findable by you but not by others?

**Architectural Context:**
How does this vulnerability fit into the broader infrastructure graph?

**Scaling Potential:**
Where else could this primitive or approach work?

**Impact (Real World):**
Beyond CVSS — what is the actual consequence?

**Remediation (Win-Win):**
How would you fix this? Frame as collaborative design improvement.

**Reflection:**
What did this finding teach you? How will you hunt differently because of it?

---

## 7. Missed Opportunities

### What I Suspect Exists But Didn't Find

| Suspected Vulnerability | Why I Think It Exists | Why I Didn't Find It | Return Plan |
|---|---|---|---|
| | | | |

### What Blocked Me

| Blocker | Type (Scope / Technical / Knowledge / Time) | Workaround? | Escalation Needed? |
|---|---|---|---|
| | | | |

### The Honesty Check
What did I miss because of bias, assumption, or laziness?

---

## 8. Knowledge Capture

### New Tools / Techniques Used

| Tool / Technique | Purpose | Effectiveness | Will Reuse? |
|---|---|---|---|
| | | | |

### Updated Primitive Library Entries
*Copy updated primitive entries here (then merge into `primitive-library.md`).*

### Updated Target Memory
*Key facts about this target to remember for next time.*

### New Questions to Research
-
-

---

## 9. Strategic Assessment

### What Worked
Which loop, technique, or mindset produced the most value?

### What Didn't Work
What consumed time without producing results?

### What to Change Next Time
Specific behavioral or methodological adjustments.

### ROI Assessment

| Metric | Value |
|---|---|
| Time invested | |
| Findings produced | |
| Primitives discovered | |
| Knowledge gained (1-10) | |
| Would take this target again? | Yes / No / Maybe |

---

## 10. Next Actions

### Immediate (Next 24 Hours)
- [ ]

### Short-Term (This Week)
- [ ]

### Long-Term (This Month)
- [ ]

### Return Targets

| Target | Reason to Return | Priority | Estimated Effort |
|---|---|---|---|
| | | | |

---

## 11. Final Reflection

**The one thing I learned in this session that makes me a better hunter:**

**The one thing I will do differently next time:**

**My confidence level in this target's security posture (1-10, with rationale):**

**Closing thought:**

*"Every target makes me smarter. Every primitive makes me more dangerous. Every reflection makes me more precise."*
