# Splunk Learning Guide

## What is Splunk?

**Simple explanation:** Splunk is a platform that turns machine data (logs, metrics, events) into searchable, analyzable insights. Think of it as "Google for your application's data."

**Technical explanation:** Splunk indexes time-series data from servers, apps, networks, security tools, etc. You query it with a language called SPL (Splunk Processing Language) to find patterns, detect anomalies, generate alerts, and visualize trends.

**Owned by:** Cisco (acquired 2024 for $28B)

**Core products:**
- **Splunk Cloud Platform** — SaaS version, hosted by Splunk
- **Splunk Enterprise** — Self-hosted, on your own servers
- **Splunk Observability Cloud** — APM/monitoring (like Datadog)
- **Splunk Enterprise Security** — SIEM for security teams
- **Splunk SOAR** — Security orchestration & automated response

---

## Why Splunk Exists

Every app, server, and device generates logs:
```
2026-06-07 14:23:01 INFO  Server started on port 8080
2026-06-07 14:23:15 WARN  Database connection slow (1.2s)
2026-06-07 14:23:42 ERROR Failed to process payment for user 12345
2026-06-07 14:24:01 INFO  User 67890 logged in from Jakarta
```

When you have **millions** of these per day across hundreds of servers, you need:
1. **Centralized storage** — all logs in one place
2. **Fast search** — find specific events in seconds
3. **Pattern detection** — spot anomalies, errors, security threats
4. **Alerting** — notify when something bad happens
5. **Dashboards** — visualize trends and metrics
6. **AI integration** — ask questions in natural language

Splunk does all of this. It's the de facto standard for log analysis in enterprise.

---

## Real-World Use Cases

### 1. Security Operations (SecOps)
- Detect unusual login attempts (brute force attacks)
- Find malware activity across endpoints
- Investigate security incidents (who did what, when)
- Compliance reporting (SOC2, ISO27001, PCI-DSS)
- Threat hunting

**Example:** "Show me all failed login attempts from outside Indonesia in the last 24 hours"

### 2. IT Operations (ITOps)
- Monitor server health (CPU, memory, disk)
- Detect service outages
- Track application errors
- Capacity planning
- Incident response

**Example:** "Alert me when server CPU exceeds 80% for more than 5 minutes"

### 3. Observability (DevOps)
- Application Performance Monitoring (APM)
- Distributed tracing across microservices
- Real User Monitoring (RUM)
- Synthetic monitoring
- SLO tracking

**Example:** "Show me the p99 latency for the checkout API in the last hour"

### 4. Network Operations (NetOps)
- Monitor network traffic
- Detect DDoS attacks
- Bandwidth analysis
- Firewall log analysis
- DNS query monitoring

**Example:** "Show me the top 10 IPs by bandwidth usage today"

### 5. Business Analytics
- User behavior analysis
- Revenue tracking
- Funnel analysis
- A/B test results
- Customer journey mapping

**Example:** "Show me conversion rate by traffic source this week"

### 6. AI/LLM Observability (NEW in 2026)
- Monitor LLM token usage and costs
- Track AI agent performance
- Detect prompt injection attacks
- Monitor model latency
- Track AI infrastructure (GPU usage, inference time)

**Example:** "Show me total Gemini API cost per user this month"

---

## Splunk AI Capabilities (2026)

This is what's NEW and what the hackathon wants you to use:

### 1. Splunk MCP Server (GA Feb 2026)
**What:** Standardized bridge between AI agents and Splunk data
**Why it matters:** AI agents can now query Splunk in natural language
**Tools exposed:**
- `splunk_*` — Core Splunk platform tools (search, indexes, data)
- `saia_*` — Splunk AI Assistant tools (generate SPL, explain SPL, ask questions)

**Example:**
```
You: "Show me the last 5 error logs from the payment service"
Agent (via MCP): Runs SPL search → Returns results
```

### 2. Splunk Hosted AI Models (GA Feb 2026)
**What:** Pre-trained models you can use via API
**Models available:**
- **Foundation-Sec-1.1-8B-Instruct** — Security-focused LLM
- **gpt-oss-120b** — Open-source large model
- **gpt-oss-20b** — Open-source medium model
- **Cisco Deep Time Series Model** — Anomaly detection on time series

**Why use them:** Free with your Splunk instance, no need for separate OpenAI/Anthropic API keys

### 3. Splunk AI Assistant for SPL (SAIA)
**What:** AI that writes and explains SPL queries
**Tools:**
- `saia_generate_spl` — Generate SPL from natural language
- `saia_explain_spl` — Explain what an SPL query does
- `saia_ask_splunk_question` — Ask questions about your data

**Example:**
```
You: "Generate a query to find all 500 errors in the last hour"
SAIA: index=web status=500 earliest=-1h
```

### 4. Splunk AI Toolkit
**What:** Build custom AI models on your data
**Use cases:**
- Custom anomaly detection
- Predictive analytics
- Classification models
- Clustering

### 5. Python SDK AI
**What:** Python library to build AI-powered Splunk apps
**Examples:**
- AI Custom Alert App
- AI Custom Search App
- AI Modular Input App

---

## SPL (Splunk Processing Language) Basics

SPL is Splunk's query language. It's similar to SQL but for log data.

**Basic syntax:**
```
search <filters> | <transformations> | <visualizations>
```

**Examples:**

**Find errors:**
```
index=web status=500
```

**Count errors per hour:**
```
index=web status=500 | timechart count by host
```

**Top 10 IPs by requests:**
```
index=web | top 10 clientip
```

**Average response time per endpoint:**
```
index=web | stats avg(response_time) by uri_path
```

**Failed logins by user:**
```
index=auth action=failure | stats count by user | sort -count
```

**Transaction analysis:**
```
index=web transaction_id=* | transaction clientip | stats avg(duration) by clientip
```

**Fields you'll see in SPL:**
- `index` — Where data is stored (like a database name)
- `sourcetype` — Type of data (access logs, error logs, etc.)
- `host` — Where the data came from
- `source` — Specific file or stream
- `_time` — Timestamp

**Common commands:**
- `stats` — Aggregate (count, sum, avg, etc.)
- `timechart` — Time-series visualization
- `top` — Most frequent values
- `rare` — Least frequent values
- `eval` — Create new fields
- `where` — Filter results
- `sort` — Sort results
- `head` / `tail` — First/last N results
- `dedup` — Remove duplicates
- `transaction` — Group related events

---

## What You Need to Create for the Hackathon

### Submission Requirements

1. **Public GitHub repo** with open-source license (MIT recommended)
2. **Demo video** under 3 minutes (YouTube/Vimeo/Youku)
3. **Architecture diagram** at root of repo (`architecture_diagram.md` or `.pdf` or `.png`)

### What to Build

**An AI-powered solution that:**
- Enhances system monitoring (Observability track)
- OR detects security threats (Security track)
- OR improves developer experience (Platform & DevEx track)

**Must use:**
- One or more Splunk AI capabilities (MCP, hosted models, AI Assistant, AI Toolkit)
- Solve a real-world challenge
- Be original work created during the hackathon period

### Track Breakdown

**Observability Track ($3K):**
- Solutions for engineering teams
- Monitor systems, detect anomalies, automate responses
- Examples: AI log analyzer, anomaly detector, automated incident responder

**Security Track ($3K):**
- Solutions for security teams
- Detect threats, investigate incidents, automate workflows
- Examples: AI threat hunter, security incident analyzer, automated SOC assistant

**Platform & Developer Experience Track ($3K):**
- Solutions for developers
- Enhance dev workflows, automate tasks, simplify Splunk interaction
- Examples: Natural language Splunk query builder, AI-powered debugging tool, dev productivity bot

**Grand Prize ($7K):** Best overall across all tracks

**Specialty Prizes ($1K each):**
- Best Use of Splunk MCP Server
- Best Use of Splunk Hosted Models
- Best Use of Splunk Developer Tools

---

## How to Get Started

### Step 1: Create Free Account
- Go to: https://www.splunk.com/en_us/form/sign-up.html
- Free Splunk Cloud trial available

### Step 2: Install Splunk Enterprise (Optional, Self-Hosted)
- Download: https://www.splunk.com/en_us/download/splunk-enterprise.html
- 60-day free trial
- Apply for 6-month Developer License: https://dev.splunk.com/

### Step 3: Install Splunk MCP Server
- From Splunkbase: Search for "Splunk MCP Server"
- Or: https://help.splunk.com/en/splunk-enterprise/mcp-server-for-splunk-platform

### Step 4: Get Sample Data
- Splunk has built-in sample datasets
- Or upload your own logs

### Step 5: Build Your Solution
- Use the AI capabilities (MCP, hosted models, AI Assistant)
- Build your agent, app, or workflow
- Test with real data

### Step 6: Record Demo Video
- Under 3 minutes
- Show your solution working
- Explain the problem and value

### Step 7: Submit on Devpost
- GitHub repo URL
- Demo video URL
- Architecture diagram
- Written description

---

## Architecture Diagram Requirements

Your `architecture_diagram.md/pdf/png` must show:
1. How your app interacts with Splunk
2. How AI models/agents are integrated
3. Data flow between services, APIs, and components

**Example structure:**
```
[Your Agent] → [MCP Client] → [Splunk MCP Server] → [Splunk Platform]
                                    ↓
                              [Hosted Models]
                                    ↓
                              [AI Toolkit]
```

---

## Architecture Diagram Examples

**For an AI log analyzer:**
```
[User Query] → [Your AI Agent] → [Splunk MCP Server] → [Splunk Search] → [Results]
                                      ↓
                                [SAIA: Explain SPL]
                                      ↓
                                [Hosted Model: Summarize]
                                      ↓
                                [User-Friendly Response]
```

**For a security threat detector:**
```
[Security Logs] → [Splunk Index] → [AI Agent via MCP] → [Foundation-Sec Model] → [Threat Score] → [Alert Dashboard]
```

**For a developer productivity tool:**
```
[Developer] → [Natural Language Query] → [SAIA: Generate SPL] → [Splunk MCP] → [Splunk Search] → [Formatted Results]
```

---

## AI Coding Tools Allowed

The hackathon FAQ typically allows:
- Claude / Claude Code
- Cursor
- GitHub Copilot
- Codex
- Any other AI coding assistant

**Use them to:**
- Write your agent code
- Generate SPL queries (then verify with SAIA)
- Create the architecture diagram
- Write the README and docs
- Build the web UI

---

## Common Splunk Terms Glossary

| Term | Meaning |
|------|---------|
| Index | Storage location for data (like a database) |
| Sourcetype | Type/format of data (access_combined, json, csv, etc.) |
| Event | A single log entry/record |
| Field | Key-value pair in an event (status=500, user=alice) |
| SPL | Splunk Processing Language (query language) |
| Dashboard | Visual display of data/charts |
| Alert | Notification triggered by a search condition |
| Forwarder | Agent that sends data to Splunk |
| Indexer | Server that stores and indexes data |
| Search Head | Server that runs searches |
| MCP | Model Context Protocol (AI ↔ tools standard) |
| SAIA | Splunk AI Assistant |
| AITK | AI Toolkit |
| RAG | Retrieval-Augmented Generation |
| SIEM | Security Information and Event Management |
| SOAR | Security Orchestration, Automation, and Response |
| APM | Application Performance Monitoring |

---

## Next Steps

1. **Create free Splunk account** (5 minutes)
2. **Install Splunk MCP Server** (15 minutes)
3. **Explore sample data** (30 minutes)
4. **Test AI capabilities** (1 hour)
5. **Brainstorm project idea** (1 hour)
6. **Build MVP** (3-4 days)
7. **Polish + demo video** (1 day)
8. **Submit** (1 day)

**Total: ~7-8 days** (tight but doable)

---

## Resources

- Splunk Documentation: https://help.splunk.com/
- Splunk MCP Server: https://help.splunk.com/en/splunk-enterprise/mcp-server-for-splunk-platform
- Splunk AI: https://www.splunk.com/en_us/products/platform.html
- SPL Reference: https://help.splunk.com/en/splunk-enterprise/search-manual/9.4/use-the-search-app/use-the-search-app-1
- Sample Data: https://help.splunk.com/en/splunk-enterprise/get-started/get-started-in-your-environment/get-data-in-1
- Developer License: https://dev.splunk.com/
- Hackathon Discord: Join via https://www.splunk.com/en_us/community.html (look for #splunk-ai-hackathon)
