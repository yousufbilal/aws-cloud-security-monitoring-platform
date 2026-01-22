
# Path B Plan (OpenSearch SIEM-lite + Dashboards + Correlation)

## Project Name (recommended)

✅ `aws-cloud-security-monitoring-platform`
Alt: `aws-siem-lite-platform`



## Repo Structure (set this up now before you start)

This will keep everything clean and non-embarrassing:

```
aws-cloud-security-monitoring-platform/
  README.md
  architecture/
    diagrams/
  terraform/
    modules/
    environments/
  detections/
    athena-sql/
    opensearch/
      queries/
      alerts/
  ingestion/
    opensearch/
    log-pipeline/
  incident-response/
    playbooks/
    case-files/
  screenshots/
    module-01/
    module-02/
    ...
  report/
    final-report.docx (or pdf)
```

---

# Module 1 (6–8h): Baseline AWS account security hardening (Production Quality)

You already did part of this. Now you make it **real**.

### Build:

* Root account locked down (MFA + no access keys)
* IAM admin role/user (not daily-use)
* Break-glass user (documented, monitored)
* Password policy
* CloudTrail log bucket protection:

  * Versioning ON
  * Lifecycle policy (archive/delete after x days)
  * Block public access ON
  * encryption ON

### Deliverable:

* `report/security-baseline.md`
* screenshots in `/screenshots/module-01/`

---

# Module 2 (8–10h): Centralized logging architecture (real design)

This is the plumbing of the SIEM-lite.

### Build:

* CloudTrail **multi-region**
* CloudTrail log file validation (if available)
* VPC Flow Logs at subnet/ENI level
* AWS Config enabled
* Logs delivered to S3 in clean structure:

  * `cloudtrail/`
  * `flowlogs/`
  * `config/`

### Deliverable:

* Architecture diagram v1 in `/architecture/diagrams/`
* S3 folder structure screenshot evidence
* Report section: `logging-design-decisions.md`

---

# Module 3 (10–15h): Athena Threat Hunting Pack (20–30 queries)

This is where your technical credibility comes from.

### Build:

Create a real query library with categories:

**IAM / Privilege Escalation**

* root usage
* admin policy attachment
* create access key
* assume role anomalies

**Persistence**

* new users created
* backdoor policies

**Defense Evasion**

* cloudtrail stopped/deleted
* logging disabled
* config disabled

**S3 / Data Exposure**

* PutBucketPolicy
* PutPublicAccessBlock changes
* ACL changes

**Network Recon (Flow logs)**

* scan patterns
* SSH brute-force indicators
* abnormal connection fan-out

### Deliverable:

* `/detections/athena-sql/` with 20–30 `.sql` files
* Each query must include header comment:

  * detection purpose
  * expected output fields
  * severity rating
* Evidence screenshots showing queries working
* Report section: `athena-threat-hunting.md`

---

# Module 4 (6–8h): GuardDuty + Correlation Logic

GuardDuty = detection source. Athena = evidence confirmation.

### Build:

* Enable GuardDuty
* Run controlled simulation:

  * port scan
  * SSH brute attempt
* Correlate:

  * GuardDuty finding details
  * CloudTrail evidence (if any)
  * Flow log evidence (source IP, dst port)

### Deliverable:

* `incident-response/case-files/case-01-guardduty-recon.md`
* “attack timeline” diagram/table in report
* screenshots in `/screenshots/module-04/`

---

# Module 5 (8–10h): Alerting Pipeline (Engineering level)

This is where you stop being “report guy” and become “platform guy”.

### Build (Path B recommended):

✅ **EventBridge → Lambda → SNS**

Events to alert on:

* Root usage
* CreateAccessKey
* AttachUserPolicy / PutUserPolicy
* PutBucketPolicy + public exposure
* SG opened to world (0.0.0.0/0)
* StopLogging/DeleteTrail

### Deliverable:

* `/detections/opensearch/alerts/` (even if OpenSearch not ready yet)
* `/automation/` OR `/ingestion/` folder with Lambda logic notes
* screenshots proving alerts fire

---

# Module 6 (12–16h): OpenSearch SIEM-lite (Core Path B Component)

This is the “elite” layer and the reason this is 70+ hours.

### Build:

* Provision OpenSearch (domain)
* Create ingestion pipeline (options):

  * CloudWatch Logs → subscription filter → Lambda → OpenSearch
  * OR Kinesis Firehose → OpenSearch
* Index strategy:

  * `cloudtrail-*`
  * `flowlogs-*`
  * `guardduty-*`

### Deliverable:

* `/ingestion/opensearch/` documentation
* OpenSearch index screenshots
* Index mappings + pipeline notes

---

# Module 7 (8–12h): Dashboards + Correlation Views (Make it Real)

This is where it becomes interview-ready.

### Build dashboards:

1. **Authentication Dashboard**

   * failed logins over time
   * unusual sources

2. **IAM Change Dashboard**

   * policy changes
   * role assumptions
   * access key creations

3. **Network Recon Dashboard**

   * top source IPs
   * top dst ports
   * scan spikes

4. **S3 Exposure Dashboard**

   * PutBucketPolicy events
   * public access changes

### Deliverable:

* `/detections/opensearch/queries/` with saved queries
* dashboard screenshots + short explanation per dashboard
* report section: `dashboards.md`

---

# Module 8 (8–10h): Incident Response “Quarantine Automation”

This makes you look advanced — because it shows “response”, not just “detection”.

### Build:

* Quarantine SG created (deny inbound, restrict outbound)
* Event-driven quarantine logic:

  * high severity GuardDuty OR detection event triggers Lambda
  * Lambda attaches quarantine SG or isolates instance
* Logging of response action (CloudTrail proof)

### Deliverable:

* playbook: `incident-response/playbooks/quarantine-procedure.md`
* demo evidence screenshots (before/after SG)
* `case-02-auto-quarantine.md`

---

# Module 9 (10–14h): Infrastructure-as-Code (Terraform) — FULL rebuild

This is required for Path B to not be “random console clicking”.

### Build:

Terraform modules for:

* CloudTrail + log bucket
* VPC Flow Logs
* Config
* SNS
* EventBridge rules
* Lambda functions
* OpenSearch domain (if feasible in Terraform)
* IAM roles/policies

### Deliverable:

* `/terraform/` fully structured
* `terraform apply` instructions
* `outputs.tf` showing important ARNs/resources

---

# Module 10 (10–16h): Red Team Simulation Pack (3–5 incidents)

You will create **real case files**.

### Simulate:

1. Port scan reconnaissance
2. SSH brute-force attempt
3. IAM privilege escalation attempt
4. S3 public exposure attempt
5. “Logging tampering attempt” (stop CloudTrail — only if safe)

Each case file must include:

* attack action
* detection (GuardDuty + OpenSearch dashboard + Athena)
* evidence screenshots
* response action

### Deliverable:

* `/incident-response/case-files/` contains `case-01` to `case-05`
* each written like a professional report

---

# Module 11 (8–12h): Final Packaging (CV-elite)

This is where we make it un-embarrassing and recruiter-proof.

### Deliverables:

* Polished GitHub README:

  * problem → architecture → features → demo evidence → results
* Final report: 10–20 pages
* Diagram pack (clean)
* Screenshot Index file (so it looks organised)
* Optional demo video (huge advantage)

---

## Total expected time

* **Minimum:** 70–80 hours
* **Strong version:** 90–110 hours

This is genuinely high value.

