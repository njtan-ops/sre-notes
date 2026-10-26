# Understanding Risk

## What Is Risk?
In incident management, **risk** is a condition that *could* cause service disruption, data loss, or degraded user experience if left unaddressed. It is a warning sign, not necessarily an active incident.

- **Risk:** Something could go wrong.
- **Problem:** Something is wrong, but users are not yet affected.
- **Incident:** Something is wrong and users are already affected.

**Bridge analogy:** A crack in a bridge support is a risk even while traffic continues normally. Likewise, high disk utilization or a deployment without rollback planning can be warning signs before an outage.

## The Two Parts of Every Risk
### Likelihood
How probable is the risk to become a real problem? A server at 99% disk utilization is more likely to fill soon than one at 60%. Consider trends, time, traffic, and recent changes.

### Impact
How severe would the consequences be? Assess affected users, revenue, data integrity, and reputation. A database outage affecting the entire application has greater impact than an internal reporting tool outage.

**Risk Level = Likelihood × Impact**

| Likelihood | Impact | Risk level | Response |
|---|---|---|---|
| High | High | Critical | Act immediately; escalate if needed |
| High | Low | Medium | Fix soon; repeated occurrence is likely |
| Low | High | Medium | Prepare a contingency plan |
| Low | Low | Low | Record and monitor |

*Source: Understanding Risk, page 3. This is the course's qualitative matrix, not a calibrated numerical scoring model.*

## Five Common Sources of Risk
1. **Deployments and changes:** Code, configuration, and infrastructure changes can introduce unexpected failures.
2. **Traffic and load:** Traffic spikes and product launches can overwhelm capacity.
3. **Age and drift:** Aging systems, expiring certificates, and unmaintained dependencies.
4. **Dependencies:** External APIs, databases, and services may fail even when your own code is healthy.
5. **Human error:** Incorrect commands, misconfigurations, skipped steps, and miscommunication.

## Risk Awareness in Daily Work
- **Monitoring:** Distinguish a one-time CPU spike from sustained deterioration.
- **Deployments:** Ask what could fail, whether rollback exists, and whether the change has been tested.
- **Investigations:** Check for affected dependent services, not just the first visible failure.
- **Normal operation:** Watch slowly worsening disk, memory, queue, and capacity trends.

**Key lesson:** You cannot eliminate all risk. Recognize it early, communicate it to the right people, and act in proportion to likelihood and impact.

## Knowledge Check
- Disk utilization is 78% and rising 2% daily. Is this a risk, problem, or incident? How would you estimate urgency?
- What risks would you identify before a Friday evening deployment?
- Why is eliminating every risk neither possible nor the objective?

**Source:** [Understanding Risk](https://academy.werkslearning.opswerks.com/pluginfile.php/2762/mod_scorm/content/3/01-understanding-risk.html), pp. 2–5.

# Immediate vs Potential Risk

## Two Types of Risk
- **Immediate risk:** Already causing, or about to cause, harm; respond within minutes.
- **Potential risk:** Not yet causing harm but may do so later; record, estimate, plan, and prevent.

**Emergency-room analogy:** A broken arm needs immediate treatment; a fever might indicate a developing condition that also requires investigation.

## Immediate Risk: What Is Hurting Users Now?
### Signs
- Errors or incorrect responses from a service.
- A critical incident alert.
- Users actively reporting failures.
- A key metric crossing a critical threshold.
- A deployment followed immediately by breakage.

### Response
1. Acknowledge the alert and open a ticket.
2. Determine scope and user impact.
3. **Mitigate first** to stop the harm; investigate root cause after stabilizing service.
4. Communicate early, even without all the answers.
5. Escalate if the priority-specific threshold is reached.

**Example:** Payment-service errors jump from 0.2% to 18% in five minutes. Customers cannot pay, so this is immediate risk. Open the ticket, inspect recent deployments, consider rollback, and alert the on-call team.

## Potential Risk: What Could Hurt Users Tomorrow?
### Signs
- Metrics trending badly but below alert thresholds.
- Disk, memory, or CPU steadily climbing at warning level.
- Intermittent dependency errors.
- Untested deployment or missing rollback plan.
- Certificate or license nearing expiration.
- Repeated minor incidents.

### Response
1. Log a ticket, even if there is no current outage.
2. Estimate *when* the condition becomes critical at the current trend.
3. Notify the responsible team and schedule prevention.
4. Set a lower-threshold warning alert.
5. Check for related risks that could compound the issue.

**Example:** Production log partition is 76% full and grows 3 percentage points per day. At that rate, 95% is about six days away. Schedule cleanup or capacity expansion before it becomes an incident.

## The Two-Question Check
**Q1: Are users being affected right now?**
- **Yes:** Immediate risk → incident response, mitigation, communication, escalation as needed.
- **No:** Ask Q2.

**Q2: Could users be affected soon if nothing changes?**
- **Yes:** Potential risk → ticket, trend estimate, owner notification, monitoring.
- **No:** Low/negligible risk → record for awareness and move on.

| Immediate                      | Potential                                      |
| ------------------------------ | ---------------------------------------------- |
| Users affected now             | No user impact yet                             |
| Action in minutes              | Action in hours or days                        |
| Incident response process      | Planned preventive work                        |
| Mitigation first               | Prevention first                               |
| Escalate on response threshold | Escalate if prevention is not acted on in time |
|                                |                                                |

## Avoid the Common Mistake
Clearing an immediate incident does not eliminate the potential risk that caused it. After a disk-full outage, freeing space restores service, but an ongoing growth trend still requires capacity planning.

**Source:** [Immediate vs Potential Risk](https://academy.werkslearning.opswerks.com/pluginfile.php/2762/mod_scorm/content/3/02-immediate-vs-potential-risk.html), pp. 2–5.

# Response vs Recovery

## Two Different Phases
**Response** stops the immediate harm, restores service, and unblocks users, even through a temporary workaround. Its goal is **speed**.

**Recovery** happens after service returns: understand the cause, implement a lasting fix, validate stability, and prevent recurrence. Its goal is **completeness**.

**House-fire analogy:** Response extinguishes the fire. Recovery repairs the damage, investigates the cause, and makes the building safe again.

## Four Risks During Response
### R1 — Changes Without a Rollback Plan
A rushed production fix can turn a partial outage into a complete outage. Define rollback criteria before changing a live system.

### R2 — Skipping Change Approval
Even emergency changes require appropriate approval or authorization under the team's incident process.

### R3 — Uncoordinated Simultaneous Changes
Multiple engineers modifying the same system can interfere with one another and obscure what helped or harmed. Assign a clear change owner and coordinate.

### R4 — Declaring Resolution Too Early
A brief return to normal metrics is not proof of recovery. Verify functionality before announcing that the incident is resolved.

**Response discipline:** Move quickly, but obtain approval, coordinate, prepare rollback, and validate.

## Four Risks During Recovery
### V1 — Mistaking a Workaround for a Permanent Fix
Rollback or restart may restore availability without removing the underlying cause.

### V2 — Not Verifying From the User's Perspective
Healthy infrastructure metrics do not prove that users can log in, buy products, or retrieve their data. Test the affected business workflow.

### V3 — Skipping the Postmortem
Without structured learning and follow-up actions, the same failure can recur. The lesson emphasizes postmortems for major incidents.

### V4 — Not Monitoring After the Fix
Watch closely after restoration, especially under real traffic. The course recommends at least 1–4 hours of close observation and checks again at 24–72 hours.

## What True Recovery Looks Like
| Response — stop the harm | Recovery — prevent recurrence |
|---|---|
| Bring service online, possibly with workaround | Replace workaround with proper root-cause fix |
| Tell stakeholders the team is responding | Confirm normal user experience |
| Contain spread to other services | Monitor stability after the fix |
| Escalate when priority threshold is reached | Hold postmortem and implement preventive actions |
| Minimize time users are affected | Reduce likelihood of repetition |

A service being **back up** is only the first step; **fully recovered** means stable, validated, and with corrective work identified or completed.

## Knowledge Check
- A database fills its disk; the team deletes logs and service returns. What still needs to happen?
- Two engineers deploy competing fixes without coordination. Which response risk applies?
- Why are normal CPU and error-rate charts insufficient proof of full recovery?

**Source:** [Response vs Recovery](https://academy.werkslearning.opswerks.com/pluginfile.php/2762/mod_scorm/content/3/03-response-vs-recovery.html), pp. 2–5.

# Escalation Delay Risk

## What Is Escalation Delay Risk?
**Escalation delay risk** is the additional harm between the point when escalation was needed and when it actually occurred. Delay can increase user impact, revenue loss, recovery time, and loss of trust.

**Ambulance analogy:** Waiting to call for help during an emergency worsens the outcome even when the intention is to solve the problem independently.

The lesson presents illustrative figures of approximately **$5,600 per minute** of enterprise IT downtime, a **15-minute maximum** for a P1 to remain at L1 before escalation, and **3× faster resolution** when useful context is handed to specialists. Treat these as *course-presented figures and a course-specific escalation rule*, not universal industry guarantees.

## Four Reasons Engineers Delay
1. **“I want to figure it out myself.”** You need to recognize your limit, not know the root cause before escalating.
2. **“I don't want to wake someone if I'm wrong.”** The on-call engineer exists to respond; fear of inconvenience must not outweigh user impact.
3. **“It might fix itself.”** If P1 criteria or escalation triggers are met, escalate even if the system might self-heal.
4. **“Just five more minutes.”** Repeated extensions compound delay. Time thresholds prevent this trap.

Escalation does **not** mean abandoning investigation. Continue gathering context and mitigating in parallel.

## P1 Timeline: Immediate vs Delayed Escalation
| Milestone | Immediate escalation | Delayed escalation |
|---|---|---|
| T+0 | Alert fires, acknowledged | Alert fires, acknowledged |
| T+5 | Investigation and ticket | Investigation; engineer works alone |
| T+15 | Threshold reached; L2 paged | Still investigating alone |
| T+18 / T+48 | L2 joins with context at T+18 | L2 joins at T+48 and starts again |
| T+35 / T+65 | Root cause identified; rollback | Root cause identified; rollback |
| T+45 / T+75 | Service restored at T+45 | Service restored at T+75 |

In the course's example, users are affected for **75 instead of 45 minutes**—about **67% longer**. The delayed path also forces the specialist to repeat investigation.

## How Delay Compounds
- **0–15 minutes:** Ideal escalation window; context is fresh.
- **15–30 minutes:** Delay cost begins accumulating.
- **30–60 minutes:** Dependent services, support queues, and business effects may expand.
- **60+ minutes:** Severe crisis and potential SLA consequences.

These windows illustrate the course's P1 scenario, not a universal timer for every organization.

## Five Non-Negotiable Escalation Triggers
1. **P1 time threshold reached — 15 minutes** under this course's rule.
2. **Impact is growing:** More users or services affected, or rising error rate.
3. **Required expertise is missing:** Database, networking, security, or another specialty.
4. **Potential data loss or security incident:** Immediately involve relevant senior/security teams.
5. **Available troubleshooting options are exhausted:** Do not repeatedly retry the same unsuccessful actions.

## Effective Escalation Handoff
Provide the alert and start time, severity, user/business impact, affected services, timeline, recent changes, actions already attempted and their results, current metrics, and what assistance is needed. A good handoff makes your investigation the specialist's starting point.

**Key lesson:** Early escalation is a sign of competence. The goal is to minimize user harm, not to prove that one engineer can solve the entire incident alone.

**Source:** [Escalation Delay Risk](https://academy.werkslearning.opswerks.com/pluginfile.php/2762/mod_scorm/content/3/04-escalation-delay-risk.html), pp. 2–5.
