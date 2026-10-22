# System Health Monitoring

> **Topic 1 of 4 · Module 3: Verticals of Monitoring**  
> Source: [Opswerks Academy — System Health Monitoring](https://academy.werkslearning.opswerks.com/pluginfile.php/2759/mod_scorm/content/3/01-system-health-monitoring.html)

## What Is System Health Monitoring?

System health monitoring continuously checks server and infrastructure performance and availability to identify problems **before users experience them**. It is distinct from security monitoring: health monitoring focuses on CPU, memory, disk, networking, and uptime; security monitoring focuses on threats, attacks, and intrusion attempts. The two can overlap when unusual infrastructure behavior is an early indicator of a security incident.

## Four Pillars of Structural Monitoring

- **Performance:** CPU utilization, memory utilization, and load average.
- **Availability:** ping/server response, uptime checks, and latency.
- **Storage and data integrity:** filesystem utilization, database volume, and log partitions.
- **Security and traffic:** firewall logs, network traffic, and packet loss.

## Seven Core KPIs

### 1. CPU Utilization

CPU utilization shows the percentage of processor capacity in use. Sustained high utilization can indicate excessive traffic, expensive queries, runaway processes, or resource contention. Investigate workload and process behavior rather than treating every short spike as an incident.

### 2. Server Ping

Ping checks reachability and can reveal packet loss or an unreachable server. Investigate network paths, host availability, and whether ICMP is allowed before interpreting failed ping as definitive service failure.

### 3. Load Average

Load average reflects tasks waiting for or using system resources. Compare it with the server's CPU capacity and observe whether pressure persists.

### 4. File System

Monitor free disk space and utilization. Full filesystems can prevent applications and databases from writing logs, temporary files, or new data.

### 5. Memory / RAM

Track memory utilization and signs of swapping or exhaustion. Severe memory pressure can slow services or cause processes to fail.

### 6. Network

Monitor packet loss, latency, and bandwidth utilization. Network degradation can interrupt application-to-database communication and user requests.

### 7. Firewall

Monitor denied connections, unexpected open ports, suspicious source IPs, and connection floods. Firewall signals can point to misconfiguration, port scanning, or malicious traffic.

## Alert Severity and Response

The course uses **Warning → Critical → Incident** to show escalating urgency. Warnings call for investigation and trend monitoring; critical conditions call for prompt action; incidents require escalation under the organization's runbook. Example thresholds in the course are **illustrative**, not universal operational limits.

## Why Acting Early Matters

Infrastructure issues cascade: high CPU can delay requests; failed ping can indicate an outage; exhausted memory can cause crashes; network packet loss can break transactions. Proactive monitoring helps reduce mean time to detect (MTTD) and mean time to resolve (MTTR).

## Tools and Key Takeaways

The course names tools such as Grafana, Dynatrace, Zabbix, Nagios, Splunk, and SolarWinds. Tool selection depends on the environment. Record observations, actions, timestamps, and outcomes during investigation.

> **Source limitation:** Several pages in the supplied PDF export are blank. The detailed KPI examples, monitoring-tool comparison, and some response procedures cannot be fully recovered from those pages; the sections above cover material visible in the export.

# Application Health Monitoring

> **Topic 2 of 4 · Module 3: Verticals of Monitoring**  
> Source: [Opswerks Academy — Application Health Monitoring](https://academy.werkslearning.opswerks.com/pluginfile.php/2759/mod_scorm/content/3/02-application-health-monitoring.html)

## What Is Application Health Monitoring?

Healthy servers do not guarantee healthy applications. Application health monitoring combines **performance data**, **availability checks**, and **runtime health indicators** to determine whether an application is responding correctly and quickly.

## Two Dimensions of Application Health

### Performance Monitoring

Measures how quickly and reliably users' requests are processed. Signals include latency, throughput, error rate, resource utilization, queue length, and dependency latency.

### Runtime Health Monitoring

Measures whether application processes are alive, reachable, and ready to handle requests. Signals include HTTP health endpoints, port checks, database and external API connectivity, runtime state, and Kubernetes probes.

Both dimensions matter: an application may be reachable but slow, or fast when available but frequently unreachable.

## Real-Time Performance Metrics

- **Response time / latency:** Rising latency may indicate database, network, or application processing bottlenecks.
- **Error rate:** Spikes can indicate API failures, new code defects, or dependency outages.
- **Throughput:** Drops can indicate resource exhaustion or reduced incoming requests; correlate with traffic.
- **Transaction duration:** Increases may indicate downstream service degradation.
- **Queue length:** Growth suggests work is arriving faster than it can be processed.
- **Dependency latency:** Slow database or third-party API calls can delay an entire request.

### Percentiles Versus Averages

Monitor **p95** and **p99** latency because averages can hide the experience of the slowest requests. A small subset of users can experience severe delays while the mean still looks acceptable.

### Response Time Components

`Total response time = application processing + database time + external API calls + network latency`

### End-User Experience

The course shows example targets for Largest Contentful Paint (LCP), First Input Delay (FID), and page load time. Treat these as the course's reference values, not as automatically applicable production thresholds; follow the organization's defined SLOs.

## Application Health Indicators

| Indicator | What it checks |
| --- | --- |
| HTTP health endpoint | Application process is responding |
| Port check | Expected service port is reachable |
| Synthetic transaction | Simulated user action succeeds |
| Dependency check | Database or external API is connected |
| Runtime metrics | Memory, garbage collection, thread pool, and related process state |

### Kubernetes Health Endpoints

- **`/health`:** General operational status, as presented in the course.
- **`/ready`:** Whether the instance can receive traffic now; readiness failure can remove it from load balancing.
- **`/liveness`:** Whether the process is alive; a failing liveness probe may cause a container restart.

These paths are course examples; actual application endpoints depend on configuration.

### Common Health Scenarios

- **Application crash:** Check process logs, restart events, and out-of-memory termination.
- **Database connection exhaustion:** Check connection pool settings, query performance, and active sessions.
- **Memory leak:** Review heap trends, garbage collection frequency, and profiling.
- **External API outage:** Check dependency health and third-party service status.

## Garbage Collection Monitoring

Excessively long or frequent garbage collection pauses can degrade application responsiveness. The course gives an illustrative rule combining a long GC pause with high heap usage as a trigger for investigation.

## Monitoring Tools

The course identifies **Azure Monitor**, **Dynatrace**, and **Grafana** as application health monitoring tools.

> **Source limitation:** Several pages of this PDF export are blank. The notes preserve the visible sections; missing sections and diagrams have not been reconstructed as if they were present.

# Application Functionality Monitoring

> **Topic 3 of 4 · Module 3: Verticals of Monitoring**  
> Source: [Opswerks Academy — Application Functionality Monitoring](https://academy.werkslearning.opswerks.com/pluginfile.php/2759/mod_scorm/content/3/03-application-functionality-monitoring.html)

## Why Functional Monitoring Is Essential

**Infrastructure health is not application functionality.** Servers may have normal CPU and memory while checkout, authentication, or database writes fail. Functional monitoring checks whether business logic and service interactions work correctly across workflows, APIs, and user-facing operations.

### Examples of Hidden Functional Failures

| Scenario | Infrastructure | User-visible problem |
| --- | --- | --- |
| Payment gateway failure | Servers healthy | Checkout fails |
| Deployment logic bug | CPU and memory normal | Orders stop processing |
| Database schema mismatch | Database server running | API write transactions fail |

## Application Flow Monitoring

Flow monitoring verifies that an entire business workflow completes from start to finish, not just that each component responds independently.

### Example: Order Checkout

`Cart → Payment → Inventory → Confirmation`

### Example: User Login

`Authentication → Session → Profile Load → Access Granted`

### Alert Triggers and Error Severity

Alerts may fire when workflow success rates fall, transactions exceed latency baselines, or a critical transaction fails entirely. The course illustrates these patterns:

- **Minor increase in 4xx errors:** Often lower urgency; investigate the client-side pattern and context.
- **Sustained 5xx errors:** High urgency; investigate server-side failures.
- **Critical dependency error:** Critical urgency; escalate to the service owner.
- **Authentication failures across services:** High urgency; investigate authentication services and recent changes.

## API Response Monitoring

APIs connect applications to databases, services, and third-party systems. A single failing endpoint can affect multiple clients and workflows.

### Latency Percentiles

- **p95 latency:** 95% of requests complete at or below the measured time.
- **p99 latency:** 99% of requests complete at or below the measured time; useful for exposing tail latency.

The course's example shows p95 at **320 ms** and p99 at **900 ms**.

### Failure Rate

`Failure rate = failed requests ÷ total requests`

The course provides illustrative severity ranges, including **below 1% as normal** and **1–3% as warning**. Production thresholds must come from the application's SLO and runbook.

## Incident Investigation Approach

Identify the failed business capability, determine the failing service or dependency, correlate the failure with recent deployments and other signals, and escalate to the responsible service owner.

## Monitoring Tools

The course identifies **Dynatrace**, **Amazon CloudWatch**, and **Splunk** for functionality monitoring.

> **Source limitation:** Some pages are blank in the supplied PDF. Later sections beyond the visible material are not represented as complete course notes.

# Database Monitoring

> **Topic 4 of 4 · Module 3: Verticals of Monitoring**  
> Source: [Opswerks Academy — Database Monitoring](https://academy.werkslearning.opswerks.com/pluginfile.php/2759/mod_scorm/content/3/04-database-monitoring.html)

## Why Database Monitoring Matters

Databases support the layers above them. Slow or unavailable databases can cause application latency, failed transactions, and customer-facing outages. Monitoring should detect performance degradation, capacity problems, and data-integrity risks before they cascade.

## The Seven Critical Database Monitoring Signals

### 1. Replication Lag

The delay between a change being committed on the primary database and appearing on a replica. Large lag can cause users to read stale data. The course identifies disk I/O, network delay, long-running transactions, CPU contention, and write-heavy workloads as potential causes.

The course's **illustrative** bands are: **under 5 seconds** normal, **5–30 seconds** warning, **30–60 seconds** critical, and **minutes or longer** a possible replication failure.

### 2. Open Connections

Active client sessions compared with the configured connection limit. Approaching the limit can prevent new work from connecting and may indicate leaks or overloaded connection pools.

### 3. IOPS

Input/output operations per second on database storage. Disk I/O saturation can become a bottleneck for reads, writes, and replication.

### 4. Long-Running Queries

Queries exceeding normal execution time can hold locks, block other transactions, and create a queue of waiting requests.

**Potential causes:** missing indexes, inefficient joins, unnecessarily broad scans, poor query plans, and lock contention.

**Response:** identify the query and execution plan, determine whether it is blocking other work, stop it if appropriate and authorized, escalate for optimization, and consider indexing changes to prevent recurrence.

### 5. Throughput — QPS / TPS

- **QPS:** Queries per second.
- **TPS:** Transactions per second.

Interpret changes against normal traffic. A sudden spike may reflect increased demand or a runaway process; a sudden drop may indicate an outage; a gradual rise may indicate growing capacity pressure.

**Investigation:** correlate throughput with deployments and application traffic. A database throughput drop without a corresponding traffic drop can indicate an internal database issue.

### 6. Space Usage

Storage utilization of database volumes and tablespaces. Approaching capacity limits threatens the ability to write new data and maintain normal operations.

### 7. Errors

Database-level errors such as connection failures, query errors, replication problems, and disk failures. Investigate patterns, affected services, and whether errors coincide with resource exhaustion or deployments.

## Four Areas of Effective Database Monitoring

- **Performance:** Query slowdown and bottlenecks.
- **Health:** Database errors and infrastructure condition.
- **Capacity:** Storage and connection limits.
- **Error signals:** Specific failure patterns.

All four are needed for complete coverage.

## Database Monitoring Tools

The course names **Oracle Enterprise Manager**, **Splunk**, and **Amazon CloudWatch**.

> **Source limitation:** The PDF export contains blank pages and some cut-off tables. Only thresholds and procedures visible in the supplied source are recorded here; do not treat omitted thresholds as verified course content.
