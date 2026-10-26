# Structured Incident Updates

> **Module 6: Basic Communication · Topic 1 of 4**  
> **Source:** [Opswerks Academy — Structured Incident Updates](https://academy.werkslearning.opswerks.com/pluginfile.php/2763/mod_scorm/content/3/01-structured-incident-updates.html) · PDF 6-1, especially pp. 2–5.

## Why Structured Updates Matter

Incident responders are not the only people who need information. Managers, support teams, on-call engineers, and sometimes customers need to know what is happening, how serious it is, and what comes next. Vague or irregular updates lead to repeated requests for status, which interrupt the engineers investigating the incident.

### Goal of Every Update

A useful update answers three questions:

1. **What is happening?**
2. **Who is affected, and how badly?**
3. **What is being done about it?**

A structured update should also tell readers **when the next update will arrive**.

**Without structure:** A manager asks for a Slack update, another person requests a Teams update, and support contacts engineers separately. With a shared incident channel and predictable cadence, stakeholders can find an authoritative current update without repeatedly interrupting the response team.

## Update Cadence — How Often to Communicate

The course assigns the following communication cadence by incident priority:

| Priority | Update frequency | When to stop |
|---|---|---|
| **P1** | Every **15 minutes** | Until resolved or downgraded |
| **P2** | Every **30 minutes** | Until resolved or downgraded |
| **P3** | Every **2 hours** | Or when status changes |
| **P4** | **At resolution** | One update when fixed |

These are the **course's prescribed intervals**; use your organization's incident runbook if its requirements differ.

### Even “No News” Is an Update

If the next scheduled update is due but no new root cause or mitigation is known, **still send it**. Example:

> Still investigating. Root cause has not been identified yet. The incident remains active; the next update will be in 15 minutes.

Silence can be mistaken for inactivity or abandonment. An update can be useful without a technical breakthrough.

## What Every Update Must Include — PQRST

PQRST, introduced in Module 2, is the standard structure for reporting incident facts:

| Field | Question | Example |
|---|---|---|
| **P — Provocation** | What triggered the response? | HTTP 500 error spike in checkout |
| **Q — Quality** | What is broken from the user's perspective? | Customers cannot complete purchases |
| **R — Region** | Where is the issue occurring? | Globally, or only in a particular region |
| **S — Severity** | How large is the impact? | 12% of checkout attempts are failing |
| **T — Time** | When did it start and how long has it lasted? | First detected at 14:32 UTC; ongoing 22 minutes |
| **Status / Next action** | What is the team doing, and when is the next update? | Rollback underway; next update 15:10 UTC |

The five PQRST fields supply the structured incident description; **status and next update** make the message actionable for stakeholders.

### Reusable Incident Update Template

> **[Priority] Incident Update — [Time, time zone]**  
> **P — Provocation:** [Trigger, alert, or signal]  
> **Q — Quality:** [Plain-language customer-facing failure]  
> **R — Region:** [Affected service, region, or user population]  
> **S — Severity:** [Quantified user/business impact]  
> **T — Time:** [Start time and duration so far]  
> **Current status:** [Investigation, mitigation, rollback, monitoring]  
> **Next update:** [Specific time and time zone]

### Bad vs Good: Course Example

**Bad:** “Hey everyone, checkout is having issues. The team is looking into it.”

**Better:**

> **P1 Update — 14:47 UTC**  
> Checkout errors detected. Approximately **12% of purchase attempts** have failed globally since **14:32 UTC**. Root cause is being investigated; deployment **v2.4.1** is suspected. A rollback has been initiated. **Next update: 15:02 UTC.**

The improved version gives timestamps, quantified impact, current working hypothesis, action underway, and a clear expectation of the next update. A hypothesis should be identified as a hypothesis, not declared a confirmed cause.

## Five Writing Habits Under Pressure

### 1. Use the Template Every Time

Keep the PQRST template pinned or bookmarked. Filling fields is faster and more consistent than writing a new message during an emergency.

### 2. Lead With User Impact

“Customers cannot complete purchases” is more useful to most readers than a detailed error message about a database connection pool. Put technical detail after the business impact.

### 3. Always Commit to the Next Update Time

Say when the next message is due, even if the incident has not changed. This reduces unnecessary “Any news?” interruptions.

### 4. Use Numbers Instead of Vague Adjectives

“About **40% of login attempts** are failing” is more informative than “many users are having trouble.” Quantified statements also help track whether conditions are improving.

### 5. Separate the Official Update From Discussion

Post one formatted status message in a dedicated incident channel and make it easy to find. Do not bury the authoritative update inside an extended troubleshooting thread.

## Key Takeaways

- Scheduled updates protect responders' focus by reducing interruptions.
- **PQRST + status + next update time** is a repeatable framework.
- Specific scope, numbers, and timestamps build confidence.
- **No change** is still important information when an update is due.

## Knowledge Check

1. A P2 incident has been active 45 minutes, and 30 minutes have passed since the last update. There is no new finding. What should be sent now?
2. Rewrite “The API is down and we're working on it. We'll update when we know more” using PQRST.
3. Why should an update start with customer impact rather than stack-trace details?
4. Identify the missing scope, severity, timeline, action, and next-update fields in a vague incident message.

# Escalation & Handover Communication

> **Module 6: Basic Communication · Topic 2 of 4**  
> **Source:** [Opswerks Academy — Escalation & Handover Communication](https://academy.werkslearning.opswerks.com/pluginfile.php/2763/mod_scorm/content/3/02-escalation-handover-communication.html) · PDF 6-2, especially pp. 2–5.

## Escalation and Handover — Two Sides of the Same Skill

**Escalation** brings a specialist or more senior responder into an active incident. **Handover** transfers responsibility and context to an incoming engineer, often at a shift change.

Both succeed when the receiving person can **start working immediately**, without first reconstructing what happened or repeating completed checks.

**Relay-race analogy:** Fast investigation can still lose time when the baton — incident context — is dropped during transfer.

### Core Principle

The person taking over should not need to ask basic questions that the current responder could have answered upfront. Transferring knowledge is part of responding to an incident, not a separate administrative extra.

## Escalation Communication — Briefing a Specialist

The purpose of an escalation briefing is **not** to send every detail you have. It is to give the right person precisely the context needed to help without delay.

### Six-Part Escalation Briefing

| Element | What to communicate |
|---|---|
| **What broke** | Failed service/component and observable symptom |
| **User impact** | How many users or transactions are affected, and what they cannot do |
| **What I've checked** | Diagnostics, dashboards, logs, attempted fixes, and results |
| **What I think it is** | Best current hypothesis, clearly marked as unconfirmed |
| **What I need from you** | Specific expertise, action, access, or decision requested |
| **Ticket link** | Incident record containing timeline and investigation details |

### Example — Escalating Checkout Failures

> **What broke:** Payment service returning HTTP 500 errors.  
> **User impact:** Approximately 12% of checkout attempts failing globally.  
> **Checked:** CPU and memory normal; error increase followed deployment v2.4.1; application logs show database connection errors.  
> **Hypothesis:** New deployment may have changed database connection-pool behavior.  
> **Need:** DBA to review connection-pool configuration and active connections on the production database.  
> **Ticket:** [Insert incident ticket URL].

The example illustrates the briefing structure; substitute real measurements and links during an actual incident.

## Verbal vs Written Escalation

### Verbal — Phone or War Room

The lesson favors verbal escalation for **P1**, when speed is critical:

1. Lead with the urgency: “P1 active — [service] is down; I need help with [specific issue].”
2. Deliver the six-part briefing succinctly.
3. Confirm the specialist has the ticket link.
4. Follow up immediately with a written summary in the incident channel.

### Written — Slack or Teams

For **P2/P3** incidents, written escalation may be sufficient when a live call is not required:

1. Paste the briefing into one message.
2. Tag the intended responder directly.
3. Include the incident ticket link in the main message, not only as a reply.
4. Request acknowledgment of receipt.

**Common failure:** “Checkout is throwing errors; need your help.” This forces several rounds of follow-up questions about scope, symptoms, actions already tried, and the required expertise.

## Shift Handover Communication

Shift transitions are risky because open incidents, monitoring concerns, and incomplete tasks can be lost when the previous engineer signs off.

Poor handovers arise when responders rush, omit structure, or **assume the incoming engineer already knows**.

### Five-Part Shift Handover Template

| Field | Include |
|---|---|
| **Active incidents** | Incident IDs, priorities, current status, owner, last update, next update time, active investigation |
| **Potential risks** | Trends/conditions that could become incidents; tickets and owners |
| **Ongoing actions** | Deployments, mitigations, scheduled changes, and work to monitor or follow up |
| **Watch items** | Anomalies or unusual metrics that have not yet triggered a ticket |
| **Context / notes** | Team availability, planned maintenance, recent changes, escalation contacts |

### Example — Clear Handover

> **Active incident:** INC-2847 — P2 payment latency. L2 investigating; next stakeholder update 16:30 UTC.  
> **Potential risk:** prod-db-01 disk 84% and growing; ticket TKT-0093 raised, owner informed.  
> **Ongoing action:** Deployment v2.5.0 scheduled for 17:00 UTC; monitor checkout errors afterward.  
> **Watch items:** API response times in APAC slightly elevated; no threshold breach yet.  
> **Context:** Senior DBA unavailable today; contact L3 team for urgent database support.

Contrast with the unhelpful “Nothing major happening; you should be fine.” That omits actionable information, leaving the next engineer to rediscover live issues.

## Four Principles of an Effective Transfer

1. **Context is the currency:** Good context reduces the time the next responder spends getting oriented.
2. **Lead with what has already been done:** Do not make specialists repeat previous investigation.
3. **Never assume they already know:** Write down even information that feels obvious to you.
4. **Written beats verbal alone:** A call is useful for speed, but the written record preserves the handover.

## Knowledge Check

1. Write an escalation briefing for a P1 database incident using all six fields.
2. Prepare a handover with one active P2 incident, one potential risk, and one scheduled deployment.
3. Why does omitting “what I've checked” increase time to resolution?
4. What can be lost when a handover occurs verbally with no written record?

# Stakeholder Communication

> **Module 6: Basic Communication · Topic 3 of 4**  
> **Source:** [Opswerks Academy — Stakeholder Communication](https://academy.werkslearning.opswerks.com/pluginfile.php/2763/mod_scorm/content/3/03-stakeholder-communication.html) · PDF 6-3, especially pp. 2–5.

## Who Are Stakeholders?

Stakeholders are people who need to understand or act on an incident's **business impact**, even if they are not diagnosing the technical issue.

| Audience | What they need |
|---|---|
| **Management and leadership** | Business impact, SLA exposure, decisions needed |
| **Customer support teams** | What to tell customers, affected features, expected duration, workarounds |
| **Business partners** | Effects on their operations and advance notice of relevant disruption |
| **End users / customers** | Clear description of the issue, affected experience, and updates through appropriate channels |

**Core distinction:** Engineering updates are written for people who can act on technical detail. Stakeholder updates explain **impact, status, and timeline** in understandable language.

## Technical vs Stakeholder Language

Translate the *consequence*, not merely the internal failure mechanism.

| Technical description | Stakeholder-oriented version |
|---|---|
| Payment service returns HTTP 500 due to connection-pool exhaustion | Customers are currently unable to complete purchases; the team is working on a fix |
| Deployment v2.4.1 is being rolled back | The likely cause has been identified and a fix is being applied |
| Replication lag on prod-db-02 is 47 seconds and rising | Some users may temporarily see outdated account information |
| OOM kill on api-prod-01 resolved; service restarted | The service is restored; a follow-up explanation will be shared |

**Test:** Could a nontechnical manager read the message and understand what is happening and what action, if any, is needed? If not, simplify the jargon without hiding relevant facts.

## Stakeholder Updates at Three Stages

The lesson prescribes a different template depending on the incident stage.

### 1. Opening Notification — When the Incident Is Declared

Send promptly so stakeholders know the problem is recognized.

> **Incident Notification — [Service] | [Date and time]**  
> **What:** We are experiencing an issue affecting [service/feature]. [Describe what users cannot do.]  
> **Who is affected:** [User group, region, percentage/number affected.]  
> **Since:** [Start time; duration if known.]  
> **What we're doing:** Our team is investigating and working to restore service.  
> **Next update:** [Specific date/time/time zone.]

### 2. Progress Update — While Active

Continue at the incident's prescribed communication cadence.

> **Incident Update — [Service] | [Date and time]**  
> **Status:** [Investigating / Fix in progress / Monitoring recovery.]  
> **Current impact:** [Same / Expanding / Reducing, with updated figures.]  
> **What's happened:** [Verified changes, confirmed findings, or investigation status, in plain language.]  
> **Expected resolution:** [Evidence-based estimate, or explicitly state that no ETA is available.]  
> **Workaround:** [Practical workaround, or state that none is available.]  
> **Next update:** [Date/time/time zone.]

Do **not** invent or promise an ETA when the team cannot support one.

### 3. Resolution Notification — After Confirmed Restoration

> **Incident Resolved — [Service] | [Date and time]**  
> **Status:** Full service has been restored.  
> **Timeline:** Incident began [time], service restored [time], duration [length].  
> **What happened:** [One or two plain-language sentences about the issue and fix.]  
> **Impact:** [Users, regions, or services affected.]  
> **Next steps:** We will review the incident and identify actions to reduce recurrence.

## Tone — How You Say It Matters

### 1. Be Calm, Not Dismissive

Do not minimize real disruption with wording like “just a minor glitch.” Acknowledge it accurately and respectfully.

### 2. Be Confident, Not Arrogant

Commit to actions and updates you can deliver. Do not claim the incident will be resolved in five minutes without adequate evidence.

### 3. Acknowledge Impact Without Dramatizing

Say “Some users cannot complete purchases,” rather than describing the entire platform as catastrophically failing if that is not the scope.

### 4. Always Close With What Happens Next

End each message with the next update time, expected restoration time if defensible, or a confirmed resolution statement. Do not leave the audience uncertain whether another update is coming.

## Key Takeaways

- Match the message to the **audience**, not the engineering conversation.
- Use plain language to communicate real facts, not to obscure them.
- Send an **opening notice, progress updates, and a resolution notice** at the appropriate stages.
- Calm, precise, accountable tone helps sustain trust.

## Knowledge Check

1. Translate “connection-pool exhaustion causing HTTP 500 responses” into a message for customers.
2. Write an opening notification with an unknown restoration time.
3. Explain why an uncertain ETA should not be presented as a promise.
4. Rewrite a dismissive or exaggerated incident message in calm, precise language.

# Post-Incident Communication

> **Module 6: Basic Communication · Topic 4 of 4**  
> **Source:** [Opswerks Academy — Post-Incident Communication](https://academy.werkslearning.opswerks.com/pluginfile.php/2763/mod_scorm/content/3/04-post-incident-communication.html) · PDF 6-4, especially pp. 2–5.

## Why Post-Incident Communication Is Its Own Phase

Restoring service and clearing alerts does not finish the communication work. People who were affected still want to know that service is stable, what happened, and whether the issue will recur.

The course describes **three distinct post-incident messages**:

| Message | Timing | Purpose |
|---|---|---|
| **All Clear** | Immediately after confirmed restoration | Confirm that the service is operating normally |
| **Incident Summary** | **Within 24 hours** of resolution | Explain what happened, duration, impact, and resolution |
| **Postmortem Communication** | After the postmortem review | Share what caused the incident and the changes planned to prevent recurrence |

These timelines are from the course; actual organizational policy may prescribe different deadlines.

## Message 1 — The All Clear

The **All Clear** is short, immediate, and unambiguous. It is **not** a substitute for the fuller incident summary.

### All Clear Template

> **All Clear — [Service]**  
> **Status:** Resolved. [Service] is fully restored and operating normally.  
> **Resolved at:** [Exact date/time/time zone.]  
> **Duration:** Approximately [X minutes/hours], from [start] to [end].  
> **Impact:** [Who was affected and how.] We apologize for the disruption.  
> **Next steps:** A full incident summary will be shared within 24 hours. [Optional: Postmortem review for P1/P2 incidents.]

### Weak vs Strong All Clear

**Weak:** “Issue resolved. Back to normal.”

**Strong example from the lesson:**

> **Resolved — 15:42 UTC.** The checkout service is fully restored. The issue lasted approximately 70 minutes, from 14:32 to 15:42 UTC, affecting about 12% of purchase attempts. We apologize for the disruption. A full summary will be shared by the end of the day.

The strong version provides the exact resolution timestamp, duration, affected users, acknowledgment of disruption, and a follow-up commitment.

## Message 2 — The Incident Summary

The course expects a written **plain-language incident summary within 24 hours**. It serves two audiences:

- Stakeholders, who need a clear account of impact and recovery.
- Internal engineers, who can use the documented sequence as an input to the postmortem.

### Seven-Part Incident Summary Template

| Field | Content |
|---|---|
| **Incident ID** | Linkable ticket or incident identifier |
| **Summary** | One or two sentences describing the disruption in plain language |
| **Timeline** | Start → detected → escalated → fix applied → resolved, with timestamps |
| **Impact** | Users, services, regions, data loss, or SLA breaches, where applicable |
| **Root cause** | What caused it; if still under investigation, state this explicitly |
| **Resolution** | How service was restored, including timing and verification |
| **Next steps** | Postmortem, fixes, preventive actions, owners, and references |

### Example Outline

> **Incident:** INC-2847  
> **Summary:** Following a configuration change, checkout requests failed for a subset of users.  
> **Timeline:** [Exact start, detection, escalation, rollback, and confirmed recovery timestamps.]  
> **Impact:** [Measured scope and business effect.]  
> **Root cause:** [Confirmed cause or “investigation ongoing.”]  
> **Resolution:** [Rollback or fix, with recovery verification.]  
> **Next steps:** [Postmortem date and links to owned action items.]

**Distribution:** The lesson identifies the management chain, customer support, engineering responders, and — for P1 incidents — broader leadership; a public status page may also be appropriate under organizational policy.

## Message 3 — Communicating Postmortem Findings

A postmortem document should not simply be filed and forgotten. Communicating its findings shows that the organization learned from the incident and is taking concrete preventive action.

### Four Practices for Sharing Postmortem Results

#### 1. Share Key Findings, Not the Entire Document

Give most stakeholders a concise explanation of what caused the incident, what restored service, and what will change. Link to the full postmortem for readers who need the detailed analysis.

#### 2. Name the Action Items Publicly

Specific **ticket references, owners, and delivery targets** build confidence. “Platform team will improve payment-service monitoring (TKT-1043, due next sprint)” is more accountable than “steps will be taken.”

#### 3. Be Honest Without Blame

Describe process and system weaknesses clearly, without framing the postmortem as personal fault-finding. Example: An alert threshold was too high, delaying detection by eight minutes; the team will change that threshold and test it.

#### 4. Close the Loop With the Original Stakeholders

People who received the initial incident notification should also receive the relevant findings and preventive follow-up. They should not have to wonder whether anything changed.

## Why This Builds Long-Term Trust

Silence after an incident suggests uncertainty or lack of accountability. A clear summary, transparent findings, and tracked improvements show that the response did not end the moment service returned.

**Communication is part of recovery:** confirm the service is back, explain the impact, and show how recurrence risk will be reduced.

## Key Takeaways

- **Three messages, not one:** All Clear → Incident Summary → Postmortem Findings.
- The All Clear sets the expectation of further communication.
- Exact times, measurements, and meaningful next steps are more credible than vague assurances.
- Postmortem action items should have **identifiers, owners, and follow-up**, not just intentions.

## Knowledge Check

1. Rewrite “Issue resolved; all good” into an effective All Clear.
2. What information belongs in the within-24-hours incident summary but not necessarily in the immediate All Clear?
3. How would you communicate an unresolved root-cause investigation without speculation?
4. Why should the original stakeholders receive postmortem findings and owned action items?
