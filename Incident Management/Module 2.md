# When Systems Fail: Understanding Incident Management

![[Pasted image 20261022034644.png]]


## What Is an Incident?
- is any unplanned interruption or degradation in the quality of an IT service that impacts or has potential to impact users, systems, or business operations

#### The ITIL Concept Hierarchy - From Signal To Solution
- Event
	- Any Observable Occurrence in the System
		- events are raw signals:
			- log entries
			- metric data points
			- system state changes
		- not every event is a problem
		- most events are completely normal
- Alert
	- Notification of an Abnormal Condition
		- alert is triggered when an even crosses a defined threshold
		- alerts require human attention but are not automatically incidents
		- alerts need to be assessed first
- Incident
	- A User-Impacting Service Disruption 
		- declared when an alert or observation is confirmed to be causing or likely to cause disruption to users or business operations
		- triggers full incident response process
- Problem
	- The Underlying Root Cause of One or More Incidents
		- problem is identified once the incident is resolved
		- represents the deep cause that allowed the incident to happen
		- solving the problem prevents the incident from recurring
- Service Request
	- A User-Initiated Request - Not a Failure
		- a formal request for something to be provided or changed
		- not caused by a failure and does not follow the incident management process


##### Why the Distinction Matters
> Treating as Service Request as an Incident wastes your team's emergency response capacity. Treating an Incident as just an Alert means users suffer longer than necessary. Knowing which category you are dealing with determines who gets paged, how fast you respond, and what process you follow



## What Is Incident Management - and Why Does It Matter?
- incident management is the structured process of detecting, responding to, resolving, and learning from incidents in order to restore service as quickly as possible and minimize impact on users and the business 

#### Four Core Pillars
- Respond Effectively
	- Have a clear process so the team can act immediately - not spend the first 20 minutes figuring out who should do what
- Communicate Clearly 
	- Keep customers, stakeholders, and leadership informed timely, accurate updates - without engineers having to stop their investigation to write messages
- Collaborate Effectively
	- Break down silos between teams. A structured process ensures the right people are in the room, tasks are assigned without confusion, and blockers are removed quickly
- Continuously Improve
	- Every incident is a learning opportunity. A proper process captures what happened, why it happened, and what changes will prevent it from happening again

##### The Incident Management Lifecycle
1. Detection 
	- An alert fires or a user reports an issue
	- The on-call engineer is notified
2. Triage
	- Assess severity
	- Confirm the incident is real
	- Decide who needs to be involved
3. Response
	- Mobilize the team
	- Assign roles
	- Begin investigation and mitigation in parallel
4. Mitigation
	- Apply a fix or workaround to restore service even if the root cause isn't yet known
5. Resolution
	- Service is fully restored
	- Incident is officially closed 
	- Stakeholders are notified
6. Postmortem
	- Analyze what happened
	- Identify the root cause
	- Implement permanent fixes

##### Mitigation vs Resolution - What's The Difference?
> **Mitigation**
> means the pain stops- service is restored for users, even if you've used a temporary fix like a rollback or a restart
> **Resolution**
> mens the problem is fully addressed, including the root cause. You should always aim for mitigation first to reduce user impact, then pursue proper resolution afterward


## Key Incident Management Roles
### Role 1 - Incident Manager (IC) | Incident Commander | Incident Commander

- The central decision-maker and coordinator during an incident
- Their job is to make sure everyone else can do their job effectively


- Coordination
	- Organize the response process
	- Establish communication channels
	- Bring the correct teams into the investigation
- Decision-Making
	- Assess and assign incident severity
	- Prioritize actions and next steps
	- Determine when to escalate
- Delegation
	- Assign investigation tasks to team membvers
	- Engage specialists when needed
	- Remove blockers preventing the team from progressing
- Stress Management
	- Keep the team calm and focused 
	- Prevent unncessary distractions
	- maintain operational discipline under pressure
- Escalation
	- Escalate to senior engineers or leadership when required
	- Request additional resources during critical incidents
- Post-Incident
	- Facilitate the postmortem review 
	- Ensure lessons learned are captured and acted upon

- Strong Communication
- Calm Under Pressure
- Decision-Making
- Situational Awareness
- Leadership
- Prioritization


### Role 2 - Technical Lead | On-Call Engineer | Subject Matter Expert (SME) | Senior Responder

- The primary technical responder
- Focuses entirely on investigating, diagnosing, and fixing the problem
- The person with the deepest technical knowledge in the area that is failing and are responsible for driving all technical decisions during the response

- Investigation
	- Identify what is failing and where
	- Form hypothesis about root causes
	- Analyze logs, metrics, traces and alerts
- Technical Decisions
	- Recommend mitigation strategies
	- Evaluate rollback or failover options
	- Decide on the safest and fastest path to resolution
- Team Guidance
	- Direct other technical responders
	- Engage subject matter experts when the problem spans multiple systems
- Documentation
	- Record investigation findings in real time
	- Track action taken and their outcomes
	- Feed findings to the Scribe for the official timeline

- Deep Technical Knowledge 
- Log & Metric Analysis 
- Systematic troubleshooting
- Hypothesis-Driven Thinking


### Role 3- Communications Manager | Comms Lead - Customer Comms - Stakeholder Liaison

- The voice of the incident response team to the outside world.
- Engineers investigating an incident should never have to stop and write stakeholder updates
- Absorbs all communication responsibilities so the technical team can stay focused on the fix

- Stakeholder Updates
	- Send regular, structured incident updates to relevant teams
	- Communicate customer impact accurately and clearly
	- Manage and update the public or internal status page
- Executive Coordination
	- Provide leadership with high-level summaries
	- Manage upward communication to avoid escalation through wrong channels
- Customer Messaging
	- Ensure messaging is clear, accurate, and avoids unnecessary alarm
	- Coordinate with support and PR teams on customer-facing language

##### Why This Role Changes Everything
> In incidents without a dedicated Comms Manager, Engineers are constantly interrupted with "what's the status?" questions. Studies show this context-switching can add 30-60 minutes to resolution time. Separating communication from investigation is one of the highest-value structural decisions a team can make



### Role 4 - Customer Support Lead | Support Coordinator | Customer Experience Lead

- Manages the front lien: incoming surge of customer tickets, live chat messages, and support calls that accompany any significant incident

- Customer Coordination
	- Manage the surge in incoming support tickets and inquiries
	- Respond to customers with accurate, approved messaging
	- Provide customer-facing ETAs and updates
- Intelligence Gathering
	- Share customer-reported symptoms with the technical team
	- Help validate and expand the known scope of impact
	- Flag new symptom patterns that may indicate a wider problem


### Role 5 - The Scribe | Incident Recorder | Timeline Keeper | Documentation Lead

- The official memory of the incident
- Creates an accurate, timestamped record that becomes the foundation of the postmortem review and future prevention efforst

- Live Documentation
	- Maintain a real-time, timestamped incident timeline
	- Record every action taken and by whom
	- Capture key decisions and the reasoning behind them
- Postmortem Support
	- Provide the authoritative record for the postmortem review
	- Ensure escalations, handoffs, and role changes are logged
	- Preserve the details - people forget what happened surprisingly fast once the pressure is off

##### The Scribe's Golden Rule
> Write what happened, not what you think happened. Record actions and observations factually and with exact timestamps. The Scribe's notes will be reviewed days later by people who weren't in the room - clarity and accuracy matter more than speed


### Role 6 - Problem Manager | RCA Lead | Root Cause Analyst | Postmortem Owner

- Takes over after service is restored
- Ensure the same incident never happens again
- Lead the root cause analysis
- Drive corrective action items
- Own the postmortem process

- Root Cause Analysis
	- Investigate the underlying cause of the incident
	- Identify all contributing factors - not just the surface trigger
	- Use structured techniques like asking "why" repeatedly until you reach the real root cause 
- Preventive Improvements
	- Tract remediation action items to completion
	- Coordinate long-term fixes with engineering teams
	- Validate that fixes actually prevent recurrence
- Postmortem management
	- Facilitate the post-incident review meeting
	- Ensure the postmortem document is completed and shared
	- Follow up on open action items

##### Incident vs. Problem Management - The Key Difference
> Incident management asks: "How do we get the service back up right now?" Problem management asks: "Why did this keep happening and what do we change so it never happens again?" Example: fixing a crashed server is incident management. Figuring out why it keeps crashing every Tuesday and permanently patching it is problem management


## How the Roles Work Together
- roles operate simultaneously and in coordination during a live incident
- together they create a system where nothing falls through the cracks

### During A Major Incident - Who Does What At The Same Time
- Incident Manager
	- Coordinates the response bridge, assigns tasks, manages escalations, and keeps the team focused and aligned throughout the entire incident
- Technical Lead
	- Drives the technical investigation - analyzing logs, forming hypotheses, testing mitigations, and communicating findings to the Incident Manager
- Communications Manager
	- Handles all external and internal updates - status pages, stakeholder emails, executive summaries - so engineers never have to stop working to write a message
- Customer Support Lead
	- Manages the surge of customer tickets, responds with approved messaging, and feeds user-reported symptoms back to the technical team as additional diagnostic data
- Scribe
	- Maintains the live incident timeline with timestamps - capturing every action, decision, and observation so nothing is lost and the postmortem has an accurate foundation
- Problem Manager
	- Observes the incident response and begins preparing for the root cause analysis - ensuring the full postmortem process kicks off immediately after service is restored

### Incident: Customers Cannot Complete Online Purchases
- Incident manager
	- Opens the incident bridge (video/Slack war room), declares a Severity 1 incident, assigns the Technical Lead to investigate the payment service, and requests the Communications Manager begin drafting the first stakeholder update
- Technical Lead
	- Pulls logs from the payment service and identifies a spike in database connection errors correlating with the deployment made 20 minutes ago. Recommends an immediate rollback to the Incident Manager
- Communications Manager
	- Posts an initial update to the status page: "We are aware of an issue affecting checkout. Our team is actively investigating. We will provide an update in 15 minutes." Sends a summary to the VP of Engineering and the Support team lead
- Customer Support Lead
	- Sees as surge of 47 checkout-related tickets in the last 10 minutes. Applies a bulk response template. Reports to the Incident manager that customers are also seeing "session expired" errors - a new symptom not yet in the investigation scope
- Scribe
	- Logs: "14:32 - Incident declared SEV-1. 14:34 - Technical Lead identified DB connection errors. 14:37 - Rollback recommended. 14:39 - Customer Support reports session expiry errors. 14:41 - Rollback initiated by DevOps." Every entry timestamped and attributes
- Problem manager
	- After service is restored via rollback, schedules a postmortem for the following day. Begins drafting the RCA document based on the Scribe's timeline. Identifies that the database connection pool limit was not increased when the new feature was deployed - the root cause to investigate

##### Notice What Didn't Happen
> No engineer had to stop investigating to answer a stakeholder question. No one had to remember what happened after the fact - the Scribe captured it live. No one was confused about who owned what - the Incident Manager had already assigned roles. this is what structured incident management looks like in practice


## Key Takeaways
- Incidents Are Inevitable
	- No system is immune to failure. Success is measured not by avoiding incidents, but by how effectively and quickly you respond to them
- Terminology Precision Matters
	- Event -> Alert -> Incident -> Problem
	- These are not synonyms
	- Using the right word routes the right people to the right problem at the right time
- Roles Remove Chaos
	- Clear ownership means no duplication, no gaps
	- Every role covers a dimension of the response that would otherwise go unmanaged under pressure
- Technical & Comms are Separate
	- Engineers who have to write stakeholder updates while debugging are slower at both
	- Separating these responsibilities is one of the most impactful structural decisions a team can make
- Documenting Enables Learning
	- What doesn't get written down doesn't get get learned from
	- The Scribe's timeline and the Problem Manager's RCA turn every incident into as system improvement
- Improvement Is Continuous 
	- Incident management is not just about fixing today's problem
	- Its about building a system that makes tomorrow's incidents shorter, rarer, and less painful

# Incident Response Lifecycle & Severity Levels
- Topic 2 of 4 - Incident Management Fundamentals
- An incident response plan gives teams a shared procedure for handling incidents under pressure
- The response lifecycle organizes work into three phases: Prepare, Respond, and Learn
- Severity levels determine how urgently the team responds and who needs to be involved

## The Incident Response Plan
- An incident response plan (incident response playbook) is a documented set of procedures for detecting, responding to, and recovering from incidents
- Establishes roles, escalation paths, communication templates, and step-by-step actions before an incident happens
- Reduces confusion and prevents teams from inventing procedures during an outage

### Five Components of a Good Incident Response Plan
- Incident Identification & Classification
	- Define criteria for recognizing incidents and assigning severity levels
	- Prevent under-reacting to critical events or over-escalating minor issues
- Communication & Escalation Procedures
	- Prepare communication templates, escalation paths, and contact lists
	- Specify who gets notified, through which channels, and when
- Containment & Eradication Strategies
	- Define tactical steps to limit impact and stop an incident from spreading
	- Examples include isolating affected systems, traffic rerouting, service restarts, and disabling compromised components
- Recovery & Restoration Processes
	- Document how to return services to full operation safely
	- Include validation steps to confirm service is restored before closing the incident
- Post-Incident Analysis & Improvement
	- Define how postmortems are conducted
	- Record lessons learned and track preventive actions to completion

#### A Plan No One Has Read Is No Plan At All
> Writing an incident response plan is only half the job. Teams need training, tabletop exercises, and regular updates so the plan is usable during a real outage.

## The Incident Response Lifecycle
- Three connected phases
	1. Prepare - Before an incident occurs
	2. Respond - During an incident
	3. Learn - After the incident
- Every incident, regardless of size or complexity, moves through these phases

### Phase 1 - Prepare
- Build the foundation for a fast, coordinated, and effective response
- Preparation is ongoing rather than a one-time checklist

#### Properties of a Good Alerting Mechanism
- Timely
	- Alert early enough to enable action before user impact becomes severe
	- Avoid unnecessary delays in notification
- Complete Coverage
	- Monitor critical functionality and meaningful user-facing failures
	- Avoid blind spots where failures go unnoticed
- Symptom-Based, Not Cause-Based
	- Alert on customer-visible problems, such as failed requests, rather than every possible internal cause
	- Focus on actionable symptoms
- Actionable
	- Every alert should require a real human decision or response
	- Avoid noisy alerts that distract responders without providing useful action

#### Preparation Activities
- Document your incident response plan
	- Roles, escalation paths, communication templates, and runbooks
- Run tabletop exercises
	- Simulate realistic incidents before they occur
- Define your on-call rotation
	- Identify who is paged first, who provides backup, and how handoffs work
- Connect alerting tools to one place
	- Centralize signals and context so responders share the same information

### Phase 2 - Respond
- Active incident management when an incident is happening
- Organize responders, establish ownership, and coordinate action instead of letting chaos determine the workflow

#### Step 1 - Detect the Incident
- Detection often begins with monitoring or alerting tools
- Incidents may also be reported by customers or internal teams
- A unified response process helps turn scattered signals into a coordinated investigation

#### Step 2 - Triage - Assess Impact & Severity
- Confirm that an incident exists and determine its scope and user impact
- Ask which services and users are affected and how urgently the team needs to act
- Assign a severity level to determine the response expectations
- Use severity to guide escalation, responder involvement, and communications

#### Step 3 - Mitigate & Resolve
- Reduce user impact as quickly as possible, even if the root cause is not yet understood
- Potential mitigations
	- Rollback a faulty deployment
	- Restart a failing service
	- Fail over to healthy infrastructure
	- Reroute traffic away from an unhealthy region
- Continue investigation after mitigation until the incident is properly resolved
- Document actions and outcomes during the response

### Phase 3 - Learn
- Turn every incident into a permanent improvement opportunity
- The primary tool is a postmortem or post-incident review (PIR)
- A postmortem is a structured analysis of what happened, why it happened, and what changes should prevent recurrence

#### Blameless Postmortems
> A blameless postmortem focuses on systems and processes, not individuals. The goal is to find the systemic weaknesses that allowed an incident to occur. When engineers fear punishment, they withhold information. Blameless culture produces better data, better learning, and better outcomes.

#### What Does a Postmortem Cover?
- Incident Timeline
	- Chronological record of events, actions, and decisions from first alert to full resolution
- Impact Summary
	- Who was affected, for how long, and what was the quantified business and user impact
- Root Cause Analysis
	- Underlying causes and contributing conditions that allowed the incident to happen
- What Went Well
	- Successful detection, coordination, communication, and mitigation practices worth repeating
- Action Items
	- Concrete improvements such as code fixes, monitoring improvements, process changes, and documentation updates
- Follow-Up & Tracking
	- Assign owners, prioritize actions, and track completion so lessons turn into actual improvements

#### The Postmortem Feeds the Plan
- Incident -> Resolve -> Postmortem -> Action Items -> System Improves
- The findings should update the response plan, monitoring, runbooks, and training
- Repeated incidents indicate that lessons or action items may not have been fully addressed

## Understanding Incident Severity Levels
- Not all incidents demand the same urgency
- Severity levels standardize how an incident's impact on users, systems, and business operations is classified
- Usually a lower SEV number means a more critical incident
- The course uses a five-level scale from SEV-1 (most critical) to SEV-5 (least critical)
- Exact definitions and response targets may differ by organization

### SEV-1 - Critical Incident | Immediate Response Required
- Catastrophic failure with very high business and user impact
- Service completely unavailable or a critical data/security event
- Examples
	- Complete production outage affecting all users
	- Customer data loss or confirmed security breach
	- Payment processing completely down
	- Critical system crash with no failover
- Response expectations
	- Response time: Within 15 minutes
	- On-call engineer paged immediately
	- Incident Manager and Technical Lead engaged
	- Stakeholder updates every 15-30 minutes
	- Leadership notified
	- After-hours page: Yes, always

### SEV-2 - Major Incident | Urgent Response Required
- Significant impact to users or a critical function is severely degraded
- Service may be partially available, but key workflows are broken for a large subset of users
- Examples
	- Service down for a specific region or user segment
	- Critical feature non-functional for a large portion of users
	- Significant performance degradation affecting most users
	- A key connected service stops working
- Response expectations
	- Response time: Within 30 minutes
	- On-call engineer paged immediately
	- Incident Manager engaged
	- Stakeholder updates every 30-60 minutes
	- After-hours page: Yes, always

### SEV-3 - Minor Incident | Prompt Response
- Non-critical issue causing limited inconvenience
- Service remains functional overall, but a feature is degraded or unreliable for some users
- Examples
	- Minor feature not working for a small user subset
	- Intermittent errors with low frequency
	- Slight performance degradation on non-critical pages
	- UI bug causing confusion but no data loss
- Response expectations
	- Response time: Within 2-4 hours
	- Handle during business hours if possible
	- No after-hours paging required
	- Assign to on-call queue for the next available engineer
	- After-hours page: Case by case

### SEV-4 - Low-Impact Issue | Scheduled Fix
- Cosmetic or minor functional issue with minimal user impact
- Primary tasks can continue without interruption
- Examples
	- Cosmetic UI bug such as wrong color or misaligned element
	- Tooltip not appearing correctly
	- Minor reporting discrepancy without business impact
- Response expectations
	- Response time: Next business day
	- Logged as a ticket in the backlog
	- No on-call paging required
	- Addressed in regular sprint planning
	- After-hours page: No

### SEV-5 - Informational / Enhancement | Planned Work
- No current impact
- May be an improvement suggestion or known issue being monitored
- Examples
	- Proactive monitoring flag without impact yet
	- Feature enhancement request
	- Known code or system issue that has not caused problems and is tracked for later work
- Response expectations
	- Response time: Planned sprint cycle
	- No escalation required
	- Reviewed in regular team meetings
	- After-hours page: No

### Severity At A Glance
| Level | User Impact | Response Time | After-Hours Page? |
| --- | --- | --- | --- |
| SEV-1 | Critical / all users / complete outage | Within 15 minutes | Yes - always |
| SEV-2 | Major / large user segment / critical feature down | Within 30 minutes | Yes - always |
| SEV-3 | Minor / small subset / degraded feature | Within 2-4 hours | Case by case |
| SEV-4 | Low / minimal or cosmetic | Next business day | No |
| SEV-5 | Informational / no current impact | Planned sprint | No |

#### Avoid Two Common Mistakes
- Severity Inflation
	- Calling everything SEV-1 to get faster attention
	- Desensitizes the team, creates alert fatigue, and burns out on-call engineers
- Severity Deflation
	- Underreporting an incident to avoid escalation
	- Delays response and allows minor issues to grow into major outages

## Putting It All Together
- The lifecycle determines what to do at each stage of the response
- Severity determines how fast to respond and who must be involved
- Severity classification happens during the Triage step of the Respond phase
- Classification triggers predefined actions such as paging, escalation, communication channels, and update frequency
- Getting severity right early prevents wasted response capacity and delayed action

### Knowledge Check
- An alert fires for 15% of users: Which response phase are you in, and what should happen next?
- What is the difference between symptom-based and cause-based alerting?
- Why should a postmortem be blameless?
- A SEV-2 incident is resolved by a rollback in 45 minutes: What still needs to happen?
- Classify these examples:
	- Entire login system is down and nobody can authenticate
	- Export to PDF fails for mobile users, while other features work
	- Checkout fails for users in Europe, while the rest of the platform works
- Why is labeling every issue SEV-1 harmful?

### Source
- OpsWerks - Incident Management Fundamentals, Module 2, Topic 2: Incident Response Lifecycle & Severity Levels
- https://academy.werkslearning.opswerks.com/pluginfile.php/2760/mod_scorm/content/3/02-incident-response-lifecycle-severity-lev.html

# Common KPIs of an Incident Management
- Topic 3 of 4 - Incident Management Fundamentals
- Key Performance Indicators (KPIs) turn incident response from a subjective activity into a measurable, continuously improving process
- Outages cause financial and reputational damage; teams need to measure how well they detect, acknowledge, mitigate, resolve, and prevent recurring incidents
- The five KPIs form a sequence: **TTD -> TTA -> TTM -> TTR -> TBF**
- Each metric reveals a different potential weakness in the incident response process

## Why KPIs Matter
- Establish structured and measurable incident management capabilities
- Reveal delays in monitoring, on-call responsiveness, operational mitigation, full restoration, and longer-term reliability
- Help prioritize improvements based on evidence instead of intuition
- A delay at an early stage can add to total downstream incident duration

## The Five KPIs
### 1. TTD - Mean Time to Detect
- Measures the average time between the start of service degradation and when the team first becomes aware of the problem
- Timeline: **First product impact -> First detection / alert / ticket**
- Indicates monitoring coverage, observability quality, and signal accuracy
- Why it matters
	- Customers may be impacted before responders know an incident exists
	- A high TTD suggests monitoring blind spots or weak alerting
- Common pitfalls
	- Measuring from when the ticket was created rather than when degradation began
	- Silent failures that never trigger alerts
	- Alert suppression or maintenance windows masking real issues
- Improvement strategies
	- Monitor customer-facing symptoms and service-level signals
	- Use synthetic monitoring that simulates real user journeys
	- Correlate related alerts and improve tracing across services

### 2. TTA - Mean Time to Acknowledge
- Measures the average time between an alert firing and an engineer claiming or acknowledging the incident
- Timeline: **Alert fires -> Engineer acknowledges**
- Indicates on-call responsiveness and routing effectiveness
- Why it matters
	- Delayed acknowledgment delays investigation, mitigation, and communication
	- A fast acknowledgment does not necessarily mean the incident is fixed
- Common pitfalls
	- Auto-acknowledgment without a real responder
	- Alerts routed to the wrong team
	- Alert fatigue caused by excessive low-signal notifications
- Improvement strategies
	- Keep alerts actionable and high signal
	- Define clear ownership and escalation paths
	- Remove duplicate or flaky alerts
	- Test 24/7 on-call coverage and escalation chains

### 3. TTM - Mean Time to Mitigate
- Measures the time from the first product impact until a mitigation reduces or stops user-facing impact
- Timeline: **First product impact -> First effective mitigation**
- Indicates the team's ability to restore usable service quickly, even before the root cause is fully corrected
- Possible mitigations
	- Roll back a deployment
	- Fail over to healthy infrastructure
	- Restart a failing service
	- Reroute traffic
- A mitigation can restore service without permanently eliminating the underlying cause

### 4. TTR - Mean Time to Resolve
- Measures the total time required to fully diagnose, recover, fix, test, and close the incident
- Timeline: **First product impact -> Full recovery / resolution**
- Represents the total business impact duration of an incident
- Depends on earlier stages, plus the speed of investigation and final restoration
- TTR and TTM are not interchangeable
	- TTM ends when impact is mitigated
	- TTR ends when the incident is fully resolved

### 5. TBF - Mean Time Between Failures
- Measures the average time between failures or incidents
- Indicates longer-term system reliability and resilience
- Higher TBF generally indicates a more resilient system with fewer recurring failures
- Key dependencies
	- Quality of root cause analysis
	- Completion and permanence of corrective actions
	- Code and testing rigor
	- Technical debt and system architecture quality
- Improvement strategies
	- Shift from reactive fixes to proactive infrastructure hardening
	- Enforce completion of postmortem action items
	- Improve automated chaos engineering and reliability testing
	- Reduce technical debt in unstable or incident-prone modules
- A fast response alone is not enough if the same incidents keep happening

## KPI Timeline
- First product impact
	- **TTD begins**
- Operator records an alert / first detection
	- **TTD ends**
	- **TTA begins**
- Engineer acknowledges alert
	- **TTA ends**
- First effective mitigation
	- **TTM ends**
- Full recovery / resolution
	- **TTR ends**
- Next failure
	- **TBF** measures the interval between failures
- The course timeline illustrates that TTM and TTR start from **first product impact**, not from alert acknowledgment

## KPI Quick Reference
| KPI | Full Name | Measures | Higher Value Usually Means |
| --- | --- | --- | --- |
| TTD | Time to Detect | Observability quality | Monitoring gaps / late detection |
| TTA | Time to Acknowledge | On-call responsiveness | Alert fatigue / poor routing |
| TTM | Time to Mitigate | Operational agility | Slow workarounds / weak playbooks |
| TTR | Time to Resolve | End-to-end lifecycle speed | Delayed recovery / technical debt |
| TBF | Time Between Failures | System reliability | Greater resilience / fewer failures |

### Mean vs Individual Measurement
- The **M** in TTD, TTA, TTM, and TTR stands for **Mean**
- Teams track both individual incident durations and averages across multiple incidents
- Averages show overall patterns, but individual incidents may reveal outliers needing investigation
- An average alone can hide unusually slow responses to specific incidents

## KPI Interdependencies & Bottleneck Patterns
- The five KPIs are connected rather than isolated
- A delay in one phase can cascade into downstream incident duration
- Example from the course
	- If TTD is 30 minutes, the incident remains undetected for half an hour
	- That time contributes to the overall mitigation and resolution duration
	- Faster acknowledgment alone cannot recover time lost before detection

### Bottleneck Pattern - Silent Failures
- Signal: High TTD
- Root cause: Poor monitoring coverage
- Improvement
	- Expand alert coverage to customer-facing flows
	- Add synthetic monitoring
	- Audit monitoring blind spots after incidents

### Bottleneck Pattern - Alert Chaos
- Signal: Good TTD, poor TTA
- Root cause: Alert fatigue and noise
- Improvement
	- Audit and prune low-value alerts
	- Enforce actionability standards
	- Remove duplicate alerts
	- Route alerts to the correct owner

## Key Takeaways
- **TTD:** How quickly do we notice?
- **TTA:** How quickly does someone respond?
- **TTM:** How quickly do we reduce impact?
- **TTR:** How quickly do we fully recover?
- **TBF:** How long before the next failure?
- Improving one KPI does not automatically improve the others
- Use the combination of metrics to identify the real bottleneck

### Knowledge Check
- An incident starts at 2:00 PM, an alert fires at 2:25 PM, an engineer acknowledges at 2:30 PM, service is restored by rollback at 2:45 PM, and the incident closes at 3:30 PM. Identify TTD, TTA, TTM, and TTR.
- If TTD is 2 minutes but TTA averages 18 minutes, what should the team improve first?
- Why should TTM and TTR be tracked separately?
- If TBF is only 3 days while TTR is 20 minutes, what does that imply about system reliability?
- What is alert fatigue, and how does it affect acknowledgment time?

### Source
- OpsWerks - Incident Management Fundamentals, Module 2, Topic 3: Common KPIs of an Incident Management
- https://academy.werkslearning.opswerks.com/pluginfile.php/2760/mod_scorm/content/3/03-common-kpis-of-an-incident-management.html
- **Source limitation:** Some PDF pages (especially pages 5-6 and 9) are blank in the supplied export; detailed examples and additional bottleneck patterns from those pages could not be verified.

# SLI, SLO, SLA, Error Budget & PQRST Framework
- Topic 4 of 4 - Incident Management Fundamentals
- These concepts give engineering, SRE, product, and business teams a shared vocabulary for reliability
- They help teams measure service quality, manage reliability risk, and communicate during incidents
- Two related systems
	- **SLI -> SLO -> SLA -> Error Budget**: measure reliability and make tradeoffs
	- **PQRST**: structure incident communication

## Why This Vocabulary Matters
- Different teams may describe the same incident using different terms
- Shared reliability definitions reduce confusion about performance, promises, and acceptable risk
- Clear incident communication avoids vague updates and helps stakeholders make decisions

## SLI, SLO, SLA & Error Budget
### SLI - Service Level Indicator
- Question: **How did we do?**
- A metric that measures actual system behavior from the user's perspective
- An SLI is a measurement, not a target
- A useful SLI
	- Measures something users directly experience, not just internal server behavior
	- Is quantifiable (percentage, latency, value, or count)
	- Can be measured continuously and automatically
	- Maps to a specific user journey or system capability
- Examples
	- Request Success Rate - percentage of HTTP requests returning successful responses
	- Latency (p95) - 95th percentile response time in milliseconds
	- Availability - percentage of time the service responds to requests
	- Error Rate - percentage of requests that fail
	- Throughput - successful requests per second
- Course example
	- Request success rate: **99.2%** of API requests returned a 2xx response in the last 30 days
	- Latency (p95): **180 ms**, meaning 95% of requests finished within 180 ms
	- Availability: **99.05%** uptime over a 30-day window
- SLIs provide raw signals used to define SLOs

### SLO - Service Level Objective
- Question: **What is acceptable?**
- An internal reliability target that defines the performance the system is expected to meet
- SLOs are deliberately set stricter than SLAs to create a safety buffer
- Three parts of an SLO
	- Metric (SLI) - what is measured
	- Target - threshold the metric must meet
	- Time Window - period over which the target is evaluated
- Example
	- Metric: Request success rate
	- Target: At least 99.9%
	- Window: Over 30 days
- Additional course examples
	- Availability SLO: 99.9% of requests must succeed over 30 days
	- Latency SLO: 95% of requests must complete under 200 ms
	- Error Rate SLO: Error rate must remain below 0.1%
- Above SLO
	- Performance is acceptable; keep shipping features and the system is healthy
- Below SLO
	- Pause or restrict risky changes, investigate, and restore reliability before continuing
- SLOs are the engineering team's internal compass

### SLA - Service Level Agreement
- Question: **What is contractually promised?**
- A formal, legally binding commitment between a service provider and its customers
- Specifies the minimum service level customers can expect and what happens if the provider fails to meet it
- Typical SLA components
	- Reliability Guarantee - committed uptime or availability
	- Penalty / Credits - compensation if the guarantee is breached
	- Measurement Window - period over which compliance is evaluated
	- Exclusions - conditions not covered by the agreement, such as specified maintenance or external events
- Course example
	- Internal SLO: **99.9%**
	- External SLA: **99.5%**
	- The 0.4 percentage-point difference creates a safety buffer
	- If performance falls below the SLO but remains above the SLA, engineering has time to improve reliability before a contractual breach
- The course frames the SLA as a ceiling that must not be breached and the SLO as the stricter internal target

### Error Budget
- Question: **How much failure is allowed?**
- The amount of unreliability a system can accumulate before crossing its SLO target
- Error Budget = **100% - SLO target**
- Example
	- SLO = 99.9% availability
	- Error budget = 0.1% of the measurement window
	- For a 30-day window: 30 x 24 x 60 = 43,200 minutes
	- Allowed unavailable time = 43,200 x 0.001 = **43.2 minutes**
- Error budgets balance innovation and reliability
	- Teams must ship new features, but releases and experiments introduce risk
	- The budget shows how much unreliability remains before the internal target is violated
- Practical decisions
	- Healthy remaining budget: consider deployments and experimentation within agreed risk controls
	- Rapidly consuming or exhausted budget: investigate reliability issues and reconsider risky changes
- Error budget policies turn reliability targets into operational decisions

## The Reliability Pipeline
- **SLI:** Measures real system behavior
- **SLO:** Defines acceptable performance
- **SLA:** Establishes the external contractual commitment
- **Error Budget:** Quantifies how much deviation is allowed
- Together, these guide deployments, risk-taking, and incident response urgency

## PQRST Framework for Incident Communication
- A structured framework for communicating during an incident
- The course presents PQRST as five pieces of information that every incident update should answer
- Purpose
	- Replace vague statements with factual, timely, and unambiguous updates
	- Keep engineers, stakeholders, and customers aligned
	- Reduce repeated questions and avoid creating additional confusion

### P - Provocation
- Question: **What triggered this?**
- Identify the specific alert signal, monitoring trigger, or event that first revealed the issue
- Focus on exact technical telemetry
	- Name of the alert
	- Metric that spiked
	- Log pattern that appeared
- Do not confuse the detection trigger with the confirmed root cause
- Why it matters
	- "The system feels slow" is too vague to direct a response
	- A precise trigger helps route the incident to the correct service and error type

### Q, R, S, T - Source Export Incomplete
- The provided PDF contains blank pages where the remaining PQRST pillars and worked communication examples appear to be located
- Their exact course definitions cannot be verified from this export
- **To complete these sections faithfully, obtain a readable export or screenshots of the missing lesson pages**

## Key Takeaways
- SLIs tell you what is actually happening
- SLOs define what the team aims to deliver
- SLAs define the external promise to customers
- Error budgets quantify allowable unreliability and inform risk decisions
- PQRST structures incident updates so they are precise and actionable
- A team needs both measurable reliability targets and effective communication

### Knowledge Check
- Explain the difference between an SLI, an SLO, and an SLA using a speed-limit analogy
- An SLA guarantees 99.5% availability, the SLO is 99.9%, and the SLI reports 99.91% over 30 days. Is the team meeting its SLO? Its SLA? How much error budget remains?
- If an SLO is 99.9% over 30 days, how many minutes of downtime are allowed?
- Why should an SLO be stricter than an SLA?
- A team's error budget is 90% exhausted with 10 days left in the month. What deployment decision should they consider?
- How does a precise incident trigger improve PQRST communication?

### Source
- OpsWerks - Incident Management Fundamentals, Module 2, Topic 4: SLI, SLO, SLA, Error Budget & PQRST Framework
- https://academy.werkslearning.opswerks.com/pluginfile.php/2760/mod_scorm/content/3/04-sli-slo-sla-error-budget-pqrst-framework.html
- **Source limitation:** PDF pages 6 and 8-10 are blank in the supplied export, preventing verification of the complete PQRST framework and some error-budget examples.





