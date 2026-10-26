# Alert Detection & Intake

> **Topic 1 of 7 · Module 4: Structured Investigation Process**  
> Source: **4-1.pdf**, pp. 1-6 · [OpsWerks Academy - Alert Detection & Intake](https://academy.werkslearning.opswerks.com/pluginfile.php/2761/mod_scorm/content/3/01-alert-detection-intake.html)

## What Is Alert Detection & Intake?

- The first phase of incident handling: identify a potential service disruption, confirm that an engineer has received it, and formally record it with enough context for coordinated response.
- **Detection:** monitoring or users identify an anomaly, failure, or threshold breach.
- **Acknowledgment:** the on-call engineer claims the alert, stopping unnecessary repeat paging and duplicate escalations.
- **Intake:** create or correlate an incident record in an IT service management (**ITSM**) tool.
- The incident record becomes the shared reference for investigation, ownership, response targets, and later review.
- The emergency-room analogy: check the patient in, record symptoms, and assess urgency before treatment.

### The Three-Step Detection Cycle

1. **Detected** - a monitoring system or user reports an abnormal condition.
2. **Acknowledged** - an engineer confirms receipt and ownership.
3. **Recorded** - a contextualized incident ticket is available for tracking and resolution.

## Common Alert Sources & Alert Noise

### Sources Shown in the Lesson

| Source           | Course figure | Examples                           |
| ---------------- | ------------- | ---------------------------------- |
| Monitoring tools | 52%           | Datadog, Splunk, CloudWatch        |
| Health checks    | 18%           | Synthetic checks, API probes, ping |
| User reports     | 15%           | Service desk, chat, phone          |
| Third parties    | 10%           | Vendors, ISPs, SaaS alerts         |

- User reports provide direct evidence of experienced impact and deserve at least the same attention as automated alerts.
- These are the lesson's figures, not independently verified industry measurements. The listed source percentages total **95%**; the export does not explain the remainder.

### Why Correlation Comes Before Human Escalation

| Alert outcome                        | Course figure | Operational implication                        |
| ------------------------------------ | ------------- | ---------------------------------------------- |
| Noise / false positives              | 38%           | Tune rules and thresholds                      |
| Duplicate alerts                     | 21%           | Merge related signals                          |
| Self-healed by automation            | 13%           | Check recovery before unnecessary intervention |
| Genuine human investigation required | 28%           | Direct response capacity here                  |

- In this illustration, **72%** do not require human investigation.
- Repeated low-value notifications produce **alert fatigue**: engineers become less responsive, including to real incidents.
- Aggregate related alerts under a parent incident and suppress duplicate or child events rather than paging separately for every symptom.

## Four Initial Response Actions

### 1. Acknowledge the Alert

- Confirm receipt immediately in the monitoring or paging system.
- Establish that a human is handling the issue and prevent unnecessary re-paging.

### 2. Create or Correlate an Incident Record

- Open a ticket in ServiceNow, Jira Service Management, BMC Remedy, or the designated ITSM platform.
- If the alert belongs to an existing incident, link it instead of creating another disconnected investigation.
- Investigation needs an accountable record and audit trail.

### 3. Record Key Details

The lesson identifies four minimum fields:

| Required field | Why it matters |
| --- | --- |
| Alert ID | Cross-reference the signal across tools |
| Exact timestamp in UTC | Establish the timeline and measure response targets |
| Affected system / service | Direct responders to the correct component |
| Trigger condition | Identify the rule, threshold, or symptom to investigate |

- The sample record also includes priority, source, assigned team, and ITSM ticket number.
- Example: Payment Gateway API; response time greater than **5,000 ms**; Datadog Synthetic source; assigned on-call team.
- Record the actual trigger rather than a vague description such as "service issue."

### 4. Perform Initial Triage

- Assign a preliminary priority from available evidence.
- Notify relevant stakeholders if the initial assessment suggests P1 or P2.
- Begin a preliminary impact assessment before deeper investigation.
- Treat the first classification as provisional: update it when evidence changes.

## Best Practices & Tools

### Automate and Prepare

- Auto-create incident tickets for P1-P4 alerts to remove a manual intake bottleneck.
- Maintain 24/7 on-call coverage, backups, and defined escalation paths.
- Rotate schedules to manage burnout and test escalation paths regularly.
- Maintain runbooks and standard operating procedures for known alert types; update them after major incidents.

### Tool Categories Named in the Source

- **ITSM:** ServiceNow, Jira Service Management, BMC Remedy / Helix.
- **Observability / APM:** Datadog, Dynatrace, New Relic, AppDynamics.
- **Logs:** Splunk, Elastic / ELK, Sumo Logic, Graylog.
- **Infrastructure:** Prometheus with Grafana, Nagios / Zabbix, CloudWatch, Azure Monitor.
- **Correlation / AIOps:** Moogsoft, BigPanda, IBM Watson AIOps, Splunk ITSI.
- **On-call:** PagerDuty, OpsGenie, VictorOps, xMatters.

## Key Takeaways & Knowledge Check

- Fast acknowledgment, automated intake, reliable on-call coverage, and complete records accelerate every later phase.
- An alert fires at 02:17 UTC: describe the four response actions in order.
- Fifteen alerts point to the same payment service: how should correlation prevent fifteen separate investigations?
- Name the four minimum record fields and explain how each helps the next responder.

# Initial Investigation

> **Topic 2 of 7 · Module 4: Structured Investigation Process**  
> Source: **4-2.pdf**, pp. 1-6 · [OpsWerks Academy - Initial Investigation](https://academy.werkslearning.opswerks.com/pluginfile.php/2761/mod_scorm/content/3/02-initial-investigation.html)

## Purpose: Validate First, Then Gather Context

- After acknowledgment and ticket creation, answer:
  - **Is this actually broken?**
  - **If it is, what do we know about it?**
- Validation prevents unnecessary mobilization and investigation of the wrong problem.
- Structured context makes specialist escalation faster and reduces repeated work.
- The lesson presents approximately **67% MTTR reduction** for structured P1 investigation, **40% false alarms**, and **3x faster specialist escalation** with context. These are course claims; supporting study details are not supplied.
- The 40% figure here and 38% noise figure in Topic 1 are separate lesson illustrations, not a reconciled dataset.

## First: Is the Alert Real?

### Check Common False-Positive Explanations

- **Scheduled maintenance:** a planned restart or update may legitimately trigger alerts.
- **Expected traffic spike:** launches or seasonal events can temporarily exceed thresholds.
- **Configuration change:** thresholds may no longer match normal operating ranges.
- **Auto-scaling:** new instances starting and warming up can cause temporary metric spikes.
- **Load / stress testing:** scheduled tests can alter production monitoring signals.

### Document the Validation Decision

- Record both confirmed incidents and false-positive findings.
- Include evidence, such as the relevant maintenance window.
- A false-positive decision is useful operational data for alert tuning; it should not disappear without explanation.

## The Four-Step Investigation Framework

### 1. Alert Review - Re-read the Alert Carefully

- Identify the exact threshold, observed value, error codes, anomaly, and stated priority.
- Confirm that the alert was interpreted correctly before pursuing a theory.

### 2. Log Check - Inspect Logs and Dashboards

- Look for error spikes, latency changes, and resource pressure.
- Align timestamps with the alert onset.
- Use relevant log searches, cloud logs, APM traces, and dashboards.
- If supporting signals are absent, reconsider whether this is a false alarm.

### 3. Impact Identification - Determine What Is Affected

- Identify hosts / VMs, application services, network segments, and database instances.
- Distinguish one component from multiple components, and regional impact from global impact.
- A precise affected-component inventory helps the next responder begin in the correct place.

### 4. Change Correlation - Ask What Changed Recently

- Check deployments, patches, configuration changes, scheduled jobs, and maintenance windows.
- Review deployment history for the previous **2-4 hours**.
- Correlate change timestamps with symptom onset.
- The course emphasizes recent changes as a common starting point.
- A matching timeline suggests a hypothesis; it does not by itself prove root cause.
- Where evidence supports it, propose rollback through the incident and change process.

## Practical Checks by Layer

### Infrastructure Health

- Test affected-server reachability using ping and SSH where appropriate.
- Check CPU utilization and runaway processes.
- Review memory consumption and swap.
- Check disk I/O and available space on all mounts.

### Network & Connectivity

- Check throughput and packet loss between important nodes.
- Review firewall rules and security-group changes.
- Inspect paths using traceroute or mtr.
- Check load-balancer health and target-group status.

### Application & Dependencies

- Test endpoints and HTTP status codes.
- Run synthetic transactions representing user flows.
- Review application errors and stack traces.
- Check service dependencies and downstream APIs.

## Time-Box the Investigation & Document Live

### When to Stop Investigating Alone

- The lesson recommends **15-30 minutes** for initial investigation.
- If the problem remains unclear, escalate with the evidence already collected.
- Apply the more specific priority escalation triggers in Topic 5; a P1 should not wait 30 minutes merely because this general investigation window permits it.

### Investigation Record Checklist

- Exact event timestamps.
- Alert threshold, metric name, and observed value.
- Affected-component inventory.
- Dashboard screenshots and relevant log excerpts.
- Recent deployments, patches, and configuration changes.
- False-positive assessment and supporting evidence.
- Current status, next actions, escalation contacts, and P1/P2 stakeholder updates.

### Worked Example from the Lesson

- Alert: HTTP 5xx rate above **5% for 10 minutes** on api-prod-01 and api-prod-02.
- **09:44 UTC:** CPU normal; memory at **92%**; OOM killer triggered on api-prod-02.
- **09:48 UTC:** deployment dc3a1f2 at **09:30** correlated with error onset at **09:32**.
- Next action: propose rollback to the Incident Manager, pending approval.
- Keep observed facts separate from the suspected explanation.

## Knowledge Check

- What should you check before treating high memory as a confirmed incident?
- At 20 minutes into an unresolved P2 investigation, what evidence should be ready for escalation?
- Why is a deployment shortly before an alert a lead rather than proof?
- How do live notes support both handoff and postmortem analysis?

# Impact Analysis

> **Topic 3 of 7 · Module 4: Structured Investigation Process**  
> Source: **4-3.pdf**, pp. 1-6 · [OpsWerks Academy - Impact Analysis](https://academy.werkslearning.opswerks.com/pluginfile.php/2761/mod_scorm/content/3/03-impact-analysis.html)

## What Is Impact Analysis?

- A structured assessment of **who and what is affected and the business consequence**.
- Healthy infrastructure metrics do not guarantee that customers can complete business transactions.
- Identify affected stakeholders, estimate business cost, and provide the evidence needed to set priority.
- Include internal staff, external customers, teams, and business units.
- Consider lost revenue, productivity, reputation, and service commitments.

## Five Assessment Criteria

### C1 - How Many People Are Affected?

- Quantify affected users rather than saying "many."
- Separate internal users from customers.
- Ten inconvenienced employees and 10,000 customers unable to log in require different responses.

### C2 - Is It a Critical Business Service?

- Identify the service and business workflow.
- Billing, payments, trading, and customer portals may require immediate escalation.
- Internal productivity services can have different tolerance for disruption.

### C3 - How Widespread Is It?

- Record site, region, or global scope.
- The same technical fault can have very different consequences depending on its reach.

### C4 - Are Real Customers Affected?

- Confirm whether external users experience the failure.
- Customer checkout failure affects revenue and reputation differently from an internal dashboard slowdown.

### C5 - Is a Service Contract at Risk?

- Identify the applicable service tier and SLA.
- The lesson distinguishes 24/7 critical, business-hours, and best-effort service.
- Record potential breach exposure and use it in escalation decisions.

## High, Medium & Low Impact

### High Impact

- Revenue-generating or customer-facing outage with significant business consequences.
- Lesson response: immediate executive escalation, war room, and customer communications.
- Topic 3 associates this with a **P1 example resolution target of 4 hours**.
- Example: core payment gateway down, customers unable to purchase; the lesson illustrates revenue loss of **$50,000-$200,000 per hour**.

### Medium Impact

- Degraded service or internal productivity loss affecting teams, without immediate revenue impact.
- Notify the team lead, document a workaround, and watch for worsening impact.
- Topic 3 associates this with a **P2 example resolution target of 8 hours**.
- Example: slow internal reporting; provide and document an export-to-Excel workaround.

### Low Impact

- Minor issue with a viable workaround and no significant business disruption.
- Standard ticket process, communicate the workaround, and schedule the fix.
- Topic 3 associates this with a **P3 example target of the next business day**.
- Example: non-critical test environment offline; restart during the next maintenance window.
- The lesson advises choosing one impact level higher when uncertain, then revising with evidence.

### Course Impact Assessment Examples

| Scenario | Users | Scope | Customer-facing? | Impact | Topic 3 target |
| --- | --- | --- | --- | --- | --- |
| Payment gateway down | 10,000+ | Global | Yes | High | 4 hours |
| Trading platform latency | 2,500 | Regional | Yes | High | 4 hours |
| Billing service errors | 800 | Global | Yes | Medium | 8 hours |
| Reporting tool slow | 150 | Single site | No | Medium | 8 hours |
| Test environment offline | 12 | Single site | No | Low | Next day |

> **Source distinction:** These are illustrative classifications and targets. Topic 4 uses a different priority/SLA example table. Do not combine them into a single organizational policy or assume customer-facing always means High.

## Documentation & CMDB Integration

### Record the Full Scope

- Impacted services and user groups.
- Affected-user counts, with internal and external users separated.
- Geographic and business-region scope.
- SLA tier and risk of breach.
- Workaround steps for Low or Medium impact.
- Full detection, triage, and resolution timeline.

### Link the Correct Configuration Item

- A **CMDB** is a Configuration Management Database: a record of IT assets and their relationships.
- A **CI** is a Configuration Item such as a server, application, network device, database, or service.
- Linking the incident to the affected CI supports dependency analysis and service history.
- Examples shown: prod-web-01, PaymentGW_v3, fw-core-ny, oracle-billing, CustomerPortal.
- A plain-text description provides context; a CI link connects the incident to the asset and its dependency map.

## Knowledge Check

- Assess billing errors affecting 800 global customers using all five criteria.
- How do High and Medium impact differ in required response?
- What can a correct CMDB link reveal that an isolated text description cannot?

# Priority Check

> **Topic 4 of 7 · Module 4: Structured Investigation Process**  
> Source: **4-4.pdf**, pp. 1-5 · [OpsWerks Academy - Priority Check](https://academy.werkslearning.opswerks.com/pluginfile.php/2761/mod_scorm/content/3/04-priority-check.html)

## Priority = Impact × Urgency

- **Impact:** how many people are affected and how serious the damage is.
- **Urgency:** how quickly the situation could worsen or requires action.
- The formula is a classification framework; use the defined matrix rather than inventing numerical scores.
- Priority controls response speed, notification / escalation, and update cadence.
- Record the reasoning immediately so the classification can be assessed later.

## The P1-P4 Priority Matrix

| Priority | Impact | Urgency | Lesson examples |
| --- | --- | --- | --- |
| P1 - Critical | High | High | Production outage, security breach, data loss affecting all users |
| P2 - High | Medium / High | Medium / High | Major degradation, partial outage, VIP affected |
| P3 - Medium | Medium | Low / Medium | Minor degradation with workaround, non-critical feature broken |
| P4 - Low | Low | Low | Cosmetic issue, low-impact request, general information query |

### Example Response & Resolution Targets

| Priority | Description | Response SLA | Resolution SLA | After-hours page? |
| --- | --- | --- | --- | --- |
| P1 | Full outage / data loss | 5 minutes | 2 hours | Always |
| P2 | Major degradation | 15 minutes | 4 hours | Always |
| P3 | Partial impact | 1 hour | 8 hours | Case by case |
| P4 | Minimal disruption | 4 hours | 24 hours | No |

> **Course examples, not universal targets.** Follow the organization's approved runbook. Topic 3 uses different illustrative resolution targets; Topic 4's introductory prose also describes P4 response as "next business day," while its table says 4 hours. Both variations are preserved here rather than silently resolved. Module 2's SEV-1 to SEV-5 scale should not be assumed to map directly to this P1-P4 scale.

## Four Urgency Modifiers

### After-Hours Incidents

- Limited support availability may extend impact and justify a higher urgency rating.
- The lesson illustrates a login failure affecting 200 users at 23:00 Friday as P1 when both impact and urgency are High.

### Executive / VIP Impact

- The lesson treats C-suite, VIP, and board-member impact as an urgency modifier.
- Example: a reporting slowdown for 150 internal users might ordinarily be P3, but the CEO and CFO preparing a board presentation in two hours elevate the example to at least P2.
- Ask specifically who is affected, not only how many users.

### Regulatory / Compliance Risk

- The course lists GDPR, HIPAA, and PCI-DSS as examples of compliance exposure.
- Route potential exposure through the relevant specialist and escalation process.
- The lesson does not supply legal reporting deadlines.

### Customer SLA Penalties

- Risk of missing contracted response or resolution commitments can raise urgency.
- Record the applicable commitment and potential breach rather than applying an unexplained priority increase.

## Four Documentation Requirements

### 1. Assign the Priority in the Ticket

- Record P1-P4 immediately during triage.
- Include a plain-language reason, such as complete global checkout outage.

### 2. Record Impact, Urgency & Modifiers

- Explain the evidence and any after-hours, VIP, compliance, or SLA modifier.

### 3. Confirm the Response Timer Started

- Verify that the ITSM tool started the correct timer for the assigned priority.
- Confirm that later classification changes are reflected appropriately.

### 4. Log Every Priority Change

- Record the timestamp, person making the change, old and new priority, and reason.
- Lesson example: P2 to P1 at 14:47 by J. Smith because the affected population expanded from 150 to 3,000.

## Knowledge Check

- How can the same impact produce different priorities at different times?
- What must happen within the example P2 response SLA of 15 minutes?
- A P3 incident is discovered to affect executives: how would you reassess and document it?
- Why are response and resolution targets different measurements?

# Escalation

> **Topic 5 of 7 · Module 4: Structured Investigation Process**  
> Source: **4-5.pdf**, pp. 1-6 · [OpsWerks Academy - Escalation](https://academy.werkslearning.opswerks.com/pluginfile.php/2761/mod_scorm/content/3/05-escalation.html)

## Why Escalation Must Be Structured

- Escalation brings in assistance when the current responder cannot resolve the issue alone.
- Its purpose is to involve the right expertise and authority quickly while maintaining clear ownership.
- A structured process reduces delays, keeps stakeholders informed, and preserves an audit trail.
- Escalation does not mean abandoning accountability: the incident must always have a clear owner.

## Two Types of Escalation

### Functional - Technical Expertise

| Tier | Role                                 | Work handled                                                     |
| ---- | ------------------------------------ | ---------------------------------------------------------------- |
| L1   | Service desk / first responder       | Initial triage, known issues, basic fixes, false-positive checks |
| L2   | Application and infrastructure teams | Deeper diagnostics, memory leaks, application errors, scaling    |
| L3   | Senior engineering / architects      | Complex or recurring root causes, architectural problems         |
| EXT  | Vendor / third-party support         | External provider failures, SaaS defects, CDN problems           |

### Hierarchical - Authority & Visibility

- **Incident Manager:** coordinates the bridge, response, and stakeholder updates.
- **IT leadership:** involved in major outages affecting business operations.
- **Business stakeholders:** involved when customer-facing services are affected.
- **Crisis team:** engaged for enterprise-wide or regulatory incidents.
- A major incident may require functional and hierarchical escalation simultaneously.

## Four Escalation Triggers

### 1. Standard Options Are Exhausted

- L1 has used available tools, runbooks, and knowledge without resolving the issue.
- Escalate to L2 rather than repeating unsuccessful steps.

### 2. Specialist Expertise Is Required

- Engage a DBA, network engineer, cloud architect, security specialist, or other relevant expert.
- Escalate immediately when the problem is outside the responder's expertise.

### 3. The Time Threshold Is Reached

- The lesson requires escalation of unresolved P1 incidents after **15 minutes** and P2 after **30 minutes**.
- Do not continue alone indefinitely while the service remains affected.
- These are escalation thresholds, separate from acknowledgment / response and resolution targets.

### 4. The Incident Is Growing

- More users, services, regions, or revenue are affected.
- Reassess priority and escalate to match the expanded impact.

### Example Priority & SLA Escalation Matrix

| Priority | Escalation trigger | Response SLA | Resolution SLA |
| --- | --- | --- | --- |
| P1 | 15 minutes unresolved | 5 minutes | 2 hours |
| P2 | 30 minutes unresolved | 15 minutes | 4 hours |
| P3 | 1 hour unresolved | 1 hour | 8 hours |
| P4 | 2 hours unresolved | 4 hours | 24 hours |

- These are illustrative course thresholds; the organization's escalation policy controls real incidents.
- The general 15-30-minute investigation window in Topic 2 does not override earlier specialist or P1 escalation triggers.

## Immediate Escalation Actions

### Page the Appropriate Team

- Use the configured paging policy in PagerDuty, OpsGenie, VictorOps, or xMatters.
- Check that acknowledgment meets the relevant response expectation.

### Start the Incident Bridge / War Room

- The lesson makes a dedicated bridge mandatory for P1.
- Use Teams, Zoom, Slack, or the designated channel.
- Record the meeting link, participants, and joining times.

### Assign One Incident Commander

- One named commander is mandatory for each P1 in the lesson.
- The commander coordinates L2/L3 and leadership, owns communication cadence, and serves as the central decision authority.
- Two people informally coordinating can leave ownership and decisions unclear.

## Escalation Documentation

### Incident & Handoff Fields

- Incident ID and short title.
- Priority / severity and justification.
- Exact timeline: alert, acknowledgment, escalation, and actions.
- Errors, logs, and anomalies found.
- Customer impact: affected workflows, user count, scope, and duration.
- Actions already taken, who performed them, and outcomes.
- Reason for escalation.
- Use one standard timezone consistently.

### Bridge / War Room Record

- Link or dial-in details.
- Participants and timestamps.
- Communication cadence; the lesson uses **15-minute updates for P1**.
- Stakeholder notifications.
- Decisions made.
- Escalation chain: who was paged and when.

### Course Benchmarks

- P1 MTTR target with structured escalation: **less than 2 hours**.
- First-contact resolution target: **greater than 70%**.
- Escalation-rate benchmark: **less than 25%**; the lesson suggests higher rates may reveal L1 capability gaps.
- These are presented as course benchmarks, without underlying study details; they do not justify delaying a required escalation.

## Knowledge Check

- A P1 remains unresolved at 20 minutes: which time-based trigger applies, and what assistance is needed?
- Give an incident that requires both technical and management escalation.
- A P3 expands from 30 to 4,000 users: what must be reassessed, communicated, and recorded?
- Why does a major incident need one named commander?

# Resolution

> **Topic 6 of 7 · Module 4: Structured Investigation Process**  
> Source: **4-6.pdf**, pp. 1-6 · [OpsWerks Academy - Resolution](https://academy.werkslearning.opswerks.com/pluginfile.php/2761/mod_scorm/content/3/06-resolution.html)

## What Counts as Resolution?

- Deploying a fix alone is insufficient.
- Restore service, verify technical and user recovery, monitor stability, communicate restoration, and complete the record.
- Four outcomes:
  - Restore service quickly.
  - Verify the fix is effective and stable.
  - Monitor for recurrence.
  - Document the cause and preventive actions.
- The lesson presents **60% resolved within SLA** with a structured process, **3x faster resolution** with runbooks, and **40% reduction in repeat incidents** with root-cause documentation. Treat these as course claims rather than independently established measurements.

## Step 1 - Implement the Fix Safely

### Select the Fix

- Options include rollback, configuration change, code hotfix, or failover.
- If a recent deployment caused the incident, the lesson identifies rollback as often the fastest first move.
- Choose the action according to evidence, risk, and the approved response procedure.

### Obtain Emergency Change Approval

- Follow the organization's change process even during P1.
- Use the emergency approval route rather than making undocumented production changes.

### Prepare the Rollback Plan Before Execution

- Write how to undo the proposed change if it worsens the incident.
- Ensure required responders are available before starting.
- The lesson lists rollback planning after execution in its numbered explanation, but explicitly requires the plan **before** making the change.

### Execute & Record Every Step

- Follow the approved runbook or procedure.
- Record exact timestamps, affected systems, the person acting, and results.
- A record identifying deployment and hosts is more useful than "did rollback."

## Step 2 - Verify Recovery

### Technical Verification

- Confirm service accessibility and successful responses.
- Run smoke and functional tests on affected components.
- Confirm error rates returned to baseline.
- Confirm monitoring alerts cleared because the condition recovered, rather than because alerts were silenced.
- Review logs for residual errors.

### Business Verification

- Ask end users or business owners to confirm the affected function works.
- Verify dependent systems were not harmed by the fix.
- Record whether the SLA was breached.
- Capture the exact resolution time.

### Avoid Premature Recovery Announcements

- A few healthy minutes do not prove lasting stability.
- Verify before announcing restoration; a second failure after an early all-clear damages trust and the reliability of the timeline.

## Step 3 - Monitor Post-Fix

| Course monitoring window | Focus | Expected practice |
| --- | --- | --- |
| 0-1 hour | Immediate watch | Frequent checks; involved responders remain available; watch for recurrence and fix-induced problems |
| 1-4 hours | Key metrics | Closely watch errors, response time, and resource use; the whole team need not remain in the war room |
| 4-24 hours | Confirm stability | Reduce manual checking as stability holds; brief on-call on warning signs |
| 24-72 hours | Final watch | Look for slow recurrence and prepare the post-incident review before closure |

- These are the lesson's staged monitoring windows; they should be read alongside the team's approved incident and closure policy.
- Recovery verification and post-fix monitoring serve different purposes: one confirms that the service works now, the other checks that the recovery holds.

## Communication & Proper Incident Closure

### Send the Restoration Notice

- Notify affected stakeholders that service is restored.
- Include restoration time and a brief, non-technical cause summary.
- Use approved templates for consistency.

### Issue the Formal All-Clear

- Include incident ID, affected services, timeline, and whether recipients need to take further action.
- Archive the notification in the incident record.

### Required Incident Documentation

- Underlying cause, distinguished from the symptom.
- Resolution actions, actors, and timestamps.
- Detection, escalation, fix, and full-restoration times.
- Preventive actions and a linked Problem Record when needed.
- If the underlying cause remains unknown, record that explicitly rather than inventing one.

### Closure Checklist

- **Category:** choose the correct incident type, such as application or network.
- **CI linkage:** link the affected server, application, or service in the CMDB.
- **Resolution code:** accurately distinguish:
  - **Fixed:** underlying cause resolved.
  - **Workaround:** temporary recovery.
  - **No Fault Found:** false alarm.
- **Problem Record:** open a separate problem investigation for recurring incidents or unknown root causes.
- **Customer sign-off:** obtain confirmation from the reporting user or team that service is restored.

## Key Takeaways & Knowledge Check

- A workaround can restore service while a separate problem investigation pursues the permanent fix.
- Never describe an unknown root cause as confirmed merely to complete a closure field.
- A rollback appears successful, the ticket closes, and the fault returns 45 minutes later: which verification or monitoring work was missed?
- A recurring incident is fixed temporarily for the third time: why is a Problem Record required?
- What does customer sign-off establish that a healthy dashboard alone cannot?

# Best Practices & Summary

> **Topic 7 of 7 · Module 4: Structured Investigation Process**  
> Source: **4-7.pdf**, pp. 1-5 · [OpsWerks Academy - Best Practices & Summary](https://academy.werkslearning.opswerks.com/pluginfile.php/2761/mod_scorm/content/3/07-best-practices-summary.html)

## The Complete Six-Step Workflow

1. **Alert Detection & Intake** - acknowledge, create or correlate the ticket, and capture context.
2. **Initial Investigation** - validate the alert and gather technical evidence.
3. **Impact Analysis** - identify affected users, services, scope, and business consequences.
4. **Priority Check** - combine Impact × Urgency, apply modifiers, and assign P1-P4.
5. **Escalation** - engage technical tiers, specialists, management, and vendors when criteria require it.
6. **Resolution** - implement the fix, verify, monitor, communicate, document, and close.

- Topic 7 consolidates these six operational phases.
- Each phase supplies information for the next: impact informs priority; priority informs escalation and response expectations.
- Continue reassessing as the incident changes; expanding impact can require a priority change and fresh escalation.

## Key Decision Points

| Phase                 | Decision                 | Question to answer                                      |
| --------------------- | ------------------------ | ------------------------------------------------------- |
| Alert intake          | Is it real?              | Actual problem or false alarm?                          |
| Initial investigation | What is broken?          | Which system is failing, and what evidence explains it? |
| Impact analysis       | How bad is it?           | Who is affected, and is revenue or reputation at risk?  |
| Priority check        | How urgent is it?        | How fast must we act, and who needs to know?            |
| Escalation            | Can I handle this alone? | Have time or expertise limits been reached?             |
| Resolution            | Is it actually fixed?    | Does the service work, and is recovery stable?          |

- Checklists and runbooks make these decisions more consistent under pressure.
- Preserve uncertainty in the record: "suspected cause" and "confirmed cause" are different statements.

## Six Operational Best Practices

### 1. Automatic Ticketing

- Generate the incident record from the alert where configured.
- Avoid manual intake delays and loss of context during busy incidents.

### 2. Runbooks for Known Problems

- Maintain step-by-step procedures for common failure modes.
- Runbooks reduce guesswork, missed checks, and repeated diagnosis.
- Keep them ready before an incident and revise them after learning from incidents.

### 3. Clear On-Call Rotation

- Define primary coverage, backup coverage, and escalation timing.
- Test the paths before they are needed.
- The lesson repeats the example of paging L2 after an unresolved P1 reaches 15 minutes.

### 4. Review Every P1 / P2 Afterwards

- Discuss what happened, what worked, and what needs improvement.
- Focus on weaknesses in systems and processes rather than blame.
- Translate learning into preventive changes and tracked work.

### 5. Regularly Clean Up Noisy Alerts

- Review thresholds and recurring false positives.
- Remove or repair alerts that repeatedly fail to require useful action.
- Reduce alert fatigue so important signals receive attention.

### 6. Use a Shared Dashboard

- Give responders the same live view.
- Reduce repeated requests to compare screens and disagreements caused by inconsistent information.
- A shared view supports faster coordination and fewer misunderstandings.

## Module 4 Operational Reference

### Intake & Investigation

- Acknowledge quickly and correlate duplicate alerts.
- Record the alert ID, UTC timestamp, affected service, and trigger.
- Validate the signal before mobilizing unnecessarily.
- Review alert details, logs, affected components, and recent changes.
- Time-box independent investigation and document findings live.

### Impact, Priority & Escalation

- Assess user count, business criticality, geographic scope, customer impact, and SLA exposure.
- Assign priority from impact and urgency, documenting modifiers.
- Check response timers and log priority changes.
- Escalate for exhausted options, specialist needs, elapsed time, or growing impact.
- Maintain one named commander for P1 and a timestamped escalation record.

### Resolution & Learning

- Obtain required change approval and prepare rollback before execution.
- Verify both technical recovery and real user functionality.
- Monitor for recurrence through the relevant stability window.
- Send restoration and all-clear communications.
- Complete category, CI links, resolution code, cause status, and customer sign-off.
- Raise Problem Records for recurring or unexplained incidents and review P1/P2 events.

## Reading the Course Examples Correctly

- **Priority and severity are related but distinct labels:** this module uses P1-P4; [[Module 2]] contains a SEV-1 to SEV-5 example.
- **Response, escalation, and resolution timers measure different obligations.**
- **Topic 3 and Topic 4 use different illustrative targets.** Preserve their context and use the organization's approved SLA and escalation policy for operational decisions.
- Percentages and improvement figures in the lessons are recorded as course claims; the supplied PDFs do not establish their research provenance.
- These notes were read from the supplied PDFs' rendered lesson bodies. Standard text extraction misses much of that visible content.

## Final Knowledge Check

- Recite the six operational phases and the output required from each.
- A P1 alert goes unacknowledged for 25 minutes: what failed in on-call coverage and escalation?
- The same database connection issue recurs every two weeks: what post-incident and problem-management work is missing?
- Give three ways missing runbooks can lengthen recovery.
- Explain why a successful rollback does not by itself justify incident closure.
