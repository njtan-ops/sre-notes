# How an Application Works
## What is an Application?
- a system built to help users accomplish a specific goal
- in real word environment, its rarely just one program
- basic pattern:
	1. receive input
	2.  run process
	3.  return output

## Types of Applications

%% col-start %%

%% col-break:b:secondary %%

Static Application
- delivers fixed content
- no real-time processing
- no connection to database
- lower incident complexity

%% col-break:b:secondary %%

Dynamic Application
- generates content in real time
- higher incident complexity

%% col-end %%


## Application Architecture
- blueprint of how a software system is built
- describes how they fit together
	- how they communicate
- five key areas:
	- Application Components
		- individual building blocks
			- ui
			- business logic
			- data access layers
	- Application Interactions
		- how components talk to each other and exchange data
	- Data Architecture
		- how data is stored, organized, retrieved
	- Integration Architecture
		- how apps connects with external systems and services
	- Security Architecture
		- protect application and its data from threats


## Who Builds & Manages the Application?

| Role                            | Primary Focus                                                                                                                                  | Analogy                                                                    |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| Software Engineer (SWE)         | Write the functional code                                                                                                                      | Carpenters and electricians building the house                             |
| QA/Test Engineer                | Find bugs and verifies the app works as intended before it reaches real users                                                                  | The building inspector                                                     |
| DevOps Engineer                 | Automates the process of getting code from a developer's computer onto the live server - this automated path is called the deployment pipeline | The logistics team ensuring materials arrive on-site exactly when needed   |
| Site Reliability Engineer (SRE) | Monitors the live application, scaled it under load, and leads the response when it fails                                                      | The property manager and emergency services keeping the building safe 24/7 |

### How four roles work together during a real incident
Live incident: "Login Error" Spike Detected in Production
1. SRE: Detects a spike in login errors on the monitoring dashboard and immediately alerts the rest of the team
2. QA / Test Engineer: Checks the staging environment to reproduce the issue - this determines whether it is a code bug or a server-level problem
3. Software Engineer: Pinpoints the broken line of code and writes a hotfix as quickly as possible
4. DevOps Engineer: clears the deployment pipeline and pushes the hotfix to production servers without delay



## Knowledge Check - Test Yourself
1. In your own words, what is the difference between a static and a dynamic application? Give one example of each
	- static application delivers fixed content, all users will see the same thing, example is a documentation
	- a dynamic application generates content real time and differs for every user, example is a banking app
2. Name the five factors that an application architecture considers. Why do you think each one matters during an incident
	1. Application Components - This the main building blocks of the application, the UI and the business logic 
	2. Application Interaction - how the components talk to each other will tell the interconnectedness of the application components
	3. Data Architecture - how we store, retrieve and organize data
	4. Integration Architecture - how the application connects with external systems and services
	5. Security Architecture - the underlying securities implemented for the application



# Application Layers & error Indicators
## Thinking in Layers
1. Presentation Layer             (Frontent/UI)
2. Application / API Layer         (Backend/Brain)
3. Business Logic Layer            (Rules/Calculations)
4. Database & Cache Layer          (Memory/Storage)
5. Infrastructure / Hosting Layer  (Servers/Network/Cloud)


## The Five Layers - A Deep Dive
- Presentation Layer
	- What failure looks like
		- White screen - White Screen of Death
			- the page loads but renders nothing. 
			- almost always a javascript crash that prevents the page from building itself
		- Visual Corruption - Broken Layout
			- buttons overlap, images are missing or styles look completely wrong
			- usually a failed css or asset file
		- HTTP 404 - Page Not Found
			- The user navigated to a URL that no longer exists or was renamed
	- Typical Cause
		- A frontend developer pushed a code bug
		- Content Delivery Network (CDN) went down
- Application / API Layer
	- What failure looks like
		- HTTP 500 - Internal Server Error
			- backend code crashed
			- usually due to a bug or an edge case the developer didn't account for
		- HTTP 502 - Bad Gateway
			- server in front received a bad response from the server behind it
			- often means the backend service is down or crashed
		- HTTP 504 - Gateway Timeout
			- backend received the request but took too long to respond
			- often means it is overwhelmed with traffic
		- Dependency Failure - Third-party Service Unavailable
			- the backend is running but cannot reach an external service it depends on
	- Typical Cause
		- A bug in the backend code introduced by a recent deployment
		- the server is overloaded because too many requests arrived at the same time without enough capacity to handle them
- Business Logic Layer
	- What failure looks like
		- Logic Error - Wrong Calculation
			- app works technically but the numbers are wrong
		- Permission Error - Access Control Failure
			- regular user can suddenly see the admin panel
			- a user is blocked from a feature they should be able to access
		- Data Validation - Invalid State
			- system allows an order to be placed with negative quantity
			- field that should be required is accepted as blank
	- Typical Cause
		- misunderstanding between the developer and the business team during requirements gathering
		- QA "test gap" - the scenario was never tested before release
- Database & Cache Layer
	- What failure looks like
		- Connection Refused - Database Unreachable
			- backend cannot establish a connection to the database at all
			- entire app typically goes down because no data can be read or written
		- Extreme Slowness - Slow Query 
			- page takes 20-30 seconds to load because the database is running an inefficient search across millions of rows
		- Stale Data - Cache Not Updating
			- user updates their profile but the old information keeps appearing
			- cache is serving an outdated copy
	- Typical Cause
		- too many simultaneous connections exhausting the database's connection pool
		- poorly written query that wasn't caught during testing
		- cache expiry being set too long so old data lingers instead of being refreshed
- Infrastructure/ Hosting Layer
	- What failure looks like
		- Timeout - Connection Timed Out (browser-level)
			- server is physically unreachable
			- request never even makes it to the application
			- often indicates a full cloud outage or severed network link
		- OOM Kill - Out of Memory (OOM) Kill
			- server completely ran out of memory
			- operating system automatically shut down the app to protect itself - it disappears without warning with no graceful shutdown
		- DNS Error - Domain Not Resolving
			- internet cannot translate www.yourapp.com into a server IP address
			- Users see "site can't be reached" even though the servers are running fine
	- Typical Cause
		- cloud provider outage
		- SRE accidentally misconfiguring a network firewall rule during a routine change
		- server running out of disk/memory due to a runaway process

### Golden Rule of Triage
Always start at the bottom of the stack and work your way up.
Infrastructure -> Database -> Business Logic -< API Layer - > Frontend


## Reading the Language of Errors: HTTP Status Codes
- Success
	- 200 | OK      - Standard success. The page or data was returned as expected
	- 201 | Created - A new resource was successfully created
- Redirection
	- 301 | Moved Permanently          - This URL has been replaced by a new one forever. Browsers and search engines should update their records
	- 302 | Found (Temporary Redirect) - The resource is temporarily at a different URL. Often seen during maintenance pages
- Client / User Error
	- 400 | Bad Request       - The server couldn't understand the request because the data sent was malformed or missing required fields
	- 401 | Unauthorized      - The user is not logged in or their session has expired. They need to authenticate before accessing this resource
	- 403 | Forbidden         - The user is logged in, but they do not have permission to access this. This is a Business Logic / access control failure
	- 404 | Not Found         - The specific page or API endpoint does not exist. Either it was removed, renamed, or the URL was typed incorrectly
	- 429 | Too Many Requests - The user or system has sent too many requests in a short period. A rate-limit has been triggered to protect the server
- Server Error
	- 500 | Internal Server Error - The backend code crashed. Something unexpected happened that the developer didn't handle. Check application logs immediately
	- 502 | Bad Gateway - A server acting as a proxy or gateway received an invalid response from an upstream server. The backend service behind it may be down
	- 503 | Service Unavailable - The server is temporarily unable to handle requests, usually because it is overloaded or under maintenance
	- 504 | Gateway Timeout - The gateway or proxy waited too long for a response from an upstream server. The backend is alive but too slow to respond - often caused by a slow database query


## Database Failure Patterns & Symptoms vs Causes
- Connection Exhaustion
	- backend returning HTTP 500 or 503 errors. 
	- logs show "Connection Refused" or "max connections reached"
	- all users affected simultaneously
- Slow Query Kill
	- Pages taking 20-60 seconds to load or timing out entirely
	- HTTP 504 Gateway Timeout errors
	- Issue affects all users, not just one
- Locked rows
	- Specific features freeze or time out
	- Only users interacting with the same data are affected
	- Other parts of the app work normally 

### Symptom vs Cause
|**Symptom (What You See)**|**Root Cause (What Actually Broke)**|**Layer**|
|---|---|---|
|**HTTP 500 errors across all users**|Database servers are refusing connections (connection pool exhausted)|`DB LAYER`|
|**Responses are extremely slow (20s+)**|A slow or unoptimized database query is consuming 100% of CPU, or server CPUs are overloaded|`DB / INFRA`|
|**Specific users can't complete checkout**|Row-level locks in the database are causing write operations to queue and time out|`DB LAYER`|
|**Users in certain regions can't connect at all**|CDN or DNS misconfiguration blocking traffic from specific geographic IPs|`INFRA LAYER`|
|**Private data is visible to all users**|A recent code deployment reset access control lists (ACLs), allowing all requests through|`LOGIC LAYER`|
|**App works but prices are all wrong**|A bug in the Business Logic Layer is applying the wrong discount formula|`LOGIC LAYER`|
|**Page loads blank or shows old content**|Cache layer (Redis) is serving a stale copy and hasn't been invalidated after an update|`CACHE LAYER`|

> **Key Takeaway:** When you see a **500 error**, you do not call the UI team — you call the Backend and Database teams. When you see a **Connection Refused**, you check the Database and Infrastructure layers. Knowing the layers means knowing exactly who to call and what to check first, saving critical time during a live incident.


# Understanding Dependencies

## What is a Dependency? 
- any service, system, or component that your application relies on or that relies on your application to function correctly 

## Direction: Upstream vs Downstream
> **Downstream** = things your service needs to work (providers). 
> **Upstream** = things that need your service to work (consumers). 
> If you pour red dye into the river, it flows **downstream**. If you build a dam, water backs up **upstream**.

%% col-start %%

%% col-break:b:secondary %%

⬇️ Downstream
_Services your application **calls upon** to do its job. If they break, you break._

🏢 **Internal Downstream**
Other microservices within your company. Example: Your "Order Service" calls the "Inventory Service" to check if an item is in stock before confirming the order.

🗄️ **Infrastructure Downstream**
Your database (PostgreSQL), cache (Redis), file storage (AWS S3). These are the most critical — without them, your service has no memory.

🌐 **External Downstream (3rd-Party)**
Services you pay for but don't own: Stripe for payments, Twilio for SMS, Google Maps for address lookups, SendGrid for email.

🚨 **WHAT FAILURE LOOKS LIKE**
Your service is "up" and running normally, but it throws `504 GATEWAY TIMEOUTS` because it's waiting on a response from a provider that never replies. You'll see log entries like:
`Connection timed out → api.stripe.com`
`Could not connect to Redis at 10.0.5.2`

%% col-break:b:secondary %%

⬆️ Upstream
_Services and users that **rely on your application** to do their job. When you break, they break._

😊 **The End User**
The person at their laptop clicking buttons. They are the ultimate upstream consumer — everything you build ultimately serves them.

🤝 **Partner Applications**
Other companies that integrate with your API. If your service goes down, their product breaks too — creating external pressure to resolve quickly.

👥 **Internal Upstream Teams**
Other teams in your company. Example: If you own the "Login Service," the Checkout Team is upstream — they can't let users buy anything if users can't log in.

🚨 **WHAT FAILURE LOOKS LIKE**
When your service has a bug, upstream services start producing errors _without any bug in their own code_. Example: If your Login Service is slow, the Checkout Service will show high latency — even though the Checkout code is perfectly fine. The upstream team will file a ticket pointing at their own service, but the real cause is yours.

%% col-end %%

> 💡 **THE MOST COMMON MISTAKE**
> 
> Upstream teams often raise incidents against their own service when the real problem is a downstream dependency. As an incident responder, always ask: _"Did anything change in the services this team depends on?"_ before assuming the upstream team's code is at fault.


## Nature: Interval vs External Dependencies
Beyond direction, dependencies also differ by **ownership**. This matters enormously during an incident because it determines _who you call_ and _how quickly you can expect a fix_.

| **TYPE**   | **WHAT IS IT?**                                                                                                | **WHO DO YOU CONTACT?**                                                                                            | **FIX SPEED**                           |
| ---------- | -------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ | --------------------------------------- |
| `INTERNAL` | A service managed by another team _inside_ your company. You have direct access to the team and their systems. | The team's Slack channel, their on-call engineer, or your internal incident management tool.                       | **Fast** — direct escalation path       |
| `EXTERNAL` | A 3rd-party SaaS or cloud provider (Stripe, AWS, Twilio, GitHub). You use it but don't control it.             | The provider's official **Status Page** first. Then their support portal if the issue isn't publicly acknowledged. | **Slow** — you depend on their timeline |

> ⚠️ **IMPORTANT: WHAT TO DO WHEN AN EXTERNAL DEPENDENCY IS DOWN**
> You cannot fix a third-party outage. Your job is to:
> 1. **Confirm** it's them and not you by checking their status page,
> 2. **Communicate** clearly to stakeholders that the cause is external, and
> 3. **Activate** a fallback or degraded-mode plan if one exists. Never spend hours debugging your own code when the cause is a provider outage.

### 🔍 Bookmark These — Major Provider Status Pages

- ☁️ **AWS** — [health.aws.amazon.com →](https://health.aws.amazon.com/health/status)
- ☁️ **Google Cloud** — [status.cloud.google.com →](https://status.cloud.google.com/)
- ☁️ **Azure** — [status.azure.com →](https://status.azure.com/)
- 💳 **Stripe** — [status.stripe.com →](https://status.stripe.com/)
- 📱 **Twilio** — [status.twilio.com →](https://status.twilio.com/)
- 🐙 **GitHub** — [githubstatus.com →](https://www.githubstatus.com/)
- ✉️ **SendGrid** — [status.sendgrid.com →](https://status.sendgrid.com/)
- 🗺️ **Google Maps** — [Maps Platform Status →](https://status.cloud.google.com/)

## The Dependency Chain & Hidden Dependencies
### 🔴 Live Example: A Single Database Crash Cascades to the User

1. 💥 **ROOT CAUSE — LAYER 4 (DATABASE): The Database Crashes**
    - The PostgreSQL database runs out of connections and starts refusing all new requests. The database process is technically still running, but no one can talk to it.
2. ⚠️ **RIPPLE EFFECT — LAYER 2 (API): The API Layer Starts Throwing 500 Errors**
    - The backend code is healthy. It tries to query the database, gets "Connection Refused," and has no data to return — so it responds with an HTTP 500 Internal Server Error to anyone who asks.
3. ⏳ **RIPPLE EFFECT — UPSTREAM SERVICES: The Inventory & Order Services Begin Failing**
    - Other internal services that depend on the same API layer start receiving 500 errors too. Their own code hasn't changed. They are simply victims of the chain.
4. 😵 **USER-VISIBLE IMPACT — LAYER 1 (FRONTEND): The Mobile App Shows "Service Unavailable"**
    - The user sees a generic error screen. They file a complaint. The frontend team checks their code — it's perfectly fine. The API team checks their code — it's fine too. Only when someone checks the _database_ is the real cause found.
> 💡 **THE CASCADING FAILURE LESSON**
> The service that shows the error is almost never where the problem started. Always follow the dependency chain **DOWNWARD** until you find the first service that was actually healthy and then broke — that is your root cause.


### The "Hidden" Dependencies
_Some dependencies are so fundamental that engineers forget to list them — until they fail. These invisible foundations underpin every other service and every other dependency you've learned about._
- 📖 **DNS — The Internet's Phonebook**
    - **What it is:** DNS (Domain Name System) translates human-readable addresses like `api.stripe.com` into machine-readable IP addresses. Every external API call, every browser request, every service-to-service call begins with a DNS lookup.
    - 🚨 **WHEN IT BREAKS:** Every outbound connection fails silently. Your service can't reach its database by hostname, can't call external APIs, and users can't reach your site — even if every server is running perfectly.
- 🧱 **Network / ISP — The Bridge**
    - **What it is:** The physical and virtual network connecting your servers to each other, to your databases, and to the internet. Even if both servers are healthy, a misconfigured firewall rule, an overloaded switch, or a problem with your ISP (Internet Service Provider — the company that provides your internet connection) can silently drop data between them.
    - 🚨 **WHEN IT BREAKS:** Intermittent failures and partial packet loss. Services appear "up" but respond unpredictably — some requests succeed, some time out. Extremely difficult to diagnose without network-level monitoring.
- 🔑 **Authentication Service — The Gatekeeper**
    - **What it is:** The service responsible for validating user credentials and issuing session tokens. Almost every protected feature in every application depends on it. It is often treated as an afterthought because it is so reliable — until it isn't.
    - 🚨 **WHEN IT BREAKS:** No user can log in. Every feature behind a login wall becomes inaccessible simultaneously. Often misdiagnosed as a frontend or backend problem before the auth service is checked.
- ⏰ **Time Sync (NTP) — The Clock**
    - **What it is:** Servers coordinate using synchronized clocks. Security tokens, certificate validation, log correlation, and distributed transactions all rely on clocks being in sync across machines.
    - 🚨 **WHEN IT BREAKS:** SSL certificates appear invalid. Security tokens are rejected. Log timestamps become unreliable, making post-incident analysis extremely difficult. Often invisible in monitoring.
- 📦 **CDN — The Content Distributor**
    - **What it is:** A Content Delivery Network caches and serves your static files (JavaScript, CSS, images) from servers close to the user globally. Your backend may be healthy, but if the CDN is down, users see a broken or blank page.
    - 🚨 **WHEN IT BREAKS:** The Presentation Layer breaks for users in specific regions. Pages load without styling, JavaScript crashes, or images don't appear — even though your origin servers are completely healthy.
- 📜 **Certificate / TLS — The Trust Layer**
    - **What it is:** TLS certificates encrypt the connection between users and your service. They have an expiration date. When a certificate expires or is misconfigured, browsers block access entirely with an alarming security warning.
    - 🚨 **WHEN IT BREAKS:** All users are blocked with "Your connection is not private" — the entire site becomes inaccessible. Usually happens because certificate renewal wasn't automated or was forgotten.

> ⚠️ **REAL INCIDENT TIP**
> Before assuming your application code is broken, always run through the hidden dependencies checklist:
> **CAN DNS RESOLVE YOUR SERVICE ADDRESSES? ARE YOUR TLS CERTIFICATES VALID? IS THE NETWORK ROUTE BETWEEN SERVICES CLEAR? IS THE AUTH SERVICE RESPONDING?**


## The Detective's Cheat Sheet
_Now that you understand the full picture of dependencies — direction, ownership, chains, and hidden factors — this cheat sheet maps common incident symptoms to where you should look first. Use it as your opening playbook whenever an alert fires._

|**🔍 SITUATION (WHAT YOU SEE)**|**👉 LOOK HERE FIRST**|**💡 WHY**|
|---|---|---|
|**Everything is slow — all features, all users**|⬇️ **Downstream: Database**|A slow database makes every layer above it wait. All services reading from it will degrade simultaneously.|
|**Only one feature is broken (e.g., payments)**|🌐 **External: 3rd-Party API**|A specific feature usually maps to a specific dependency. Check the provider's status page before debugging your own code.|
|**My service is healthy, but users see errors**|⬆️ **Upstream: Frontend / Client**|The bug may be in the UI code consuming your API. Your service returned the right data; the frontend just isn't displaying it correctly.|
|**I can't reach my database**|🧱 **Infrastructure: Network / Cloud**|Check whether the network route is clear, the firewall rules are intact, and the cloud provider has no active incidents.|
|**An upstream team says my service is broken, but my monitors look fine**|⬇️ **Downstream: Hidden Dependency**|Your service may be up but unable to serve real traffic because a downstream dependency (auth, DNS, cache) is silently failing.|
|**Users in one region can't connect; others are fine**|🧱 **Infrastructure: CDN / DNS / Routing**|Regional failures almost always point to routing, DNS resolution, or a CDN point-of-presence (PoP) issue — not application code.|
|**Everything was fine, then broke after a deployment**|⬇️ **Downstream: Your Own App Layer**|A recent code change is the most likely cause. Roll back the deployment as a first step, then investigate the specific change.|
|**Private data is visible to the wrong users**|🧠 **Logic Layer: Access Controls**|A deployment may have reset permission rules or access control lists. This is a Business Logic failure, not infrastructure.|

> 🔑 **THE CORE PRINCIPLE**
> _Upstream dependencies_ tell you **who will complain** when you break. _Downstream dependencies_ tell you **what to check first** when your app starts acting up. Mastering this distinction is the difference between a 10-minute resolution and a 3-hour war room.