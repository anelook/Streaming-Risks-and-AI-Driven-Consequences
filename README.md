# Streaming Systems, Hidden Risks, and AI-Driven Consequences: resources

## 1. The story: Operation Mincemeat (1943)

A corpse, a fictional officer ("Major William Martin") and a briefcase of plausible documents convinced the Axis that the Allies would not land in Sicily. Nobody broke into headquarters. A believable lie was placed in a channel the enemy trusted.

- [Operation Mincemeat (Wikipedia)](https://en.wikipedia.org/wiki/Operation_Mincemeat)
- [The War on Paper: Operation Mincemeat (Imperial War Museums)](https://www.iwm.org.uk/history/the-war-on-paper-operation-mincemeat)
- [Secret Agents, Secret Armies: Operation Mincemeat (National WWII Museum)](https://www.nationalww2museum.org/war/articles/secret-agents-secret-armies-operation-mincemeat)

## 2. Information poisoning and AI

Where models get their data (training, prompts, context, tools), and how each stage can be poisoned.

- **"As few as 250 malicious documents can produce a backdoor"** (models from 600M to 13B parameters): Anthropic, the UK AI Security Institute and the Alan Turing Institute. [A small number of samples can poison LLMs of any size](https://www.anthropic.com/research/small-samples-poison)
- **Context manipulation**: attacks on what an agent treats as reality (memory, history, context). [Real AI Agents with Fake Memories: Fatal Context Manipulation Attacks on Web3 Agents (arXiv 2503.16248)](https://arxiv.org/abs/2503.16248) · [Princeton AI blog summary](https://blog.ai.princeton.edu/2025/09/09/when-your-ai-assistant-gets-gaslithow-real-ai-agents-with-fake-memories-shows-that-smart-assistants-can-go-dangerously-off-script/)
- **A catalogue of the risk**: [OWASP Top 10 for LLM Applications, LLM04:2025 Data and Model Poisoning](https://genai.owasp.org/llmrisk/llm04/)
- **MCP (Model Context Protocol)**: how agents get tools and context. [Specification](https://modelcontextprotocol.io/specification/latest)

## 3. The demo: earthquakes, a duty officer and four layers

A rule-based "duty officer" reads earthquake events from a Kafka topic and recommends actions. We attack the stream, then switch on one defence at a time.

- Data source: [USGS Earthquake feeds](https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php) · [USGS event query API](https://earthquake.usgs.gov/fdsnws/event/1/)
- Node.js client used in the demo: [confluent-kafka-javascript](https://github.com/confluentinc/confluent-kafka-javascript)

## 4. Layer 1: who can speak

Network boundary → identity → authentication → authorization.

- Can you get there? [Networking on Confluent Cloud (private networking)](https://docs.confluent.io/cloud/current/networking/overview.html)
- Who are you? [Manage service accounts](https://docs.confluent.io/cloud/current/security/authenticate/workload-identities/service-accounts/manage-service-accounts.html)
- Can you prove it? [Use API keys to control access](https://docs.confluent.io/cloud/current/access-management/authenticate/api-keys/api-keys.html) · [Manage API keys](https://docs.confluent.io/cloud/current/access-management/authenticate/api-keys/manage-api-keys.html)
- What can you do? [Role-based access control (RBAC) on Confluent Cloud](https://docs.confluent.io/cloud/current/access-management/access-control/rbac/overview.html) · [Predefined RBAC roles](https://docs.confluent.io/cloud/current/access-management/access-control/rbac/predefined-rbac-roles.html)

## 5. Layer 2: shape and quality

Define → add rules → enforce on write → encrypt sensitive parts.

- Schemas and the registry: [Schema Registry 101 course](https://developer.confluent.io/courses/schema-registry) · [Getting started with schemas and Schema Registry (blog)](https://www.confluent.io/blog/schema-registry-for-beginners/)
- Extra rules (CEL, domain rules, migration rules): [Data contracts for Schema Registry on Confluent Cloud](https://docs.confluent.io/cloud/current/sr/fundamentals/data-contracts.html) · [Using data contracts with Confluent Schema Registry (blog)](https://www.confluent.io/blog/data-contracts-confluent-schema-registry/)
- Hiding sensitive fields: [Client-side field level encryption (CSFLE) overview](https://docs.confluent.io/cloud/current/security/encrypt/csfle/overview.html) · [Use CSFLE on Confluent Cloud](https://docs.confluent.io/cloud/current/security/encrypt/csfle/client-side.html) · [CSFLE FAQ](https://docs.confluent.io/cloud/current/security/encrypt/csfle/faq.html)

Worth knowing: data contract rules need the Stream Governance **Advanced** package, and Flink SQL does not execute them.

## 6. Layer 3: provenance, the "eyelash"

Provenance is proof of origin: who really produced this message, and has it changed since? In the demo the poller signs every event with an Ed25519 private key, and the consumer verifies with the public key. This is application-level code, not a Confluent feature.

- [RFC 8032: Edwards-Curve Digital Signature Algorithm (Ed25519)](https://www.rfc-editor.org/rfc/rfc8032)
- OWASP lists tracking data origins as a control against poisoning: [LLM04:2025 Data and Model Poisoning](https://genai.owasp.org/llmrisk/llm04/)

## 7. Stream governance

Discover → classify → trace → govern.

- [Stream Governance on Confluent Cloud](https://docs.confluent.io/cloud/current/stream-governance/)
- Classify, organize and find: [Stream Catalog](https://docs.confluent.io/cloud/current/stream-governance/stream-catalog-overview.html)
- Navigate the terrain of data streams: [Stream Lineage](https://docs.confluent.io/cloud/current/stream-governance/stream-lineage.html)
- [Stream Governance product page](https://www.confluent.io/product/stream-governance/) · [Stream Governance: how it works (blog)](https://www.confluent.io/blog/stream-governance-how-it-works/)
- Course: [Governing Data Streams](https://developer.confluent.io/courses/governing-data-streams)

## 8. Layer 4: detect and respond in real time

Observe → audit → alert → respond.

- Observe what the system is doing: [Confluent Cloud Metrics API](https://docs.confluent.io/cloud/current/monitoring/metrics-api.html)
- Record who did what: [Audit log concepts](https://docs.confluent.io/cloud/current/monitoring/audit-logging/cloud-audit-log-concepts.html) · [Access and consume audit logs](https://docs.confluent.io/cloud/current/monitoring/audit-logging/configure.html) · [Audit log event categories](https://docs.confluent.io/cloud/current/monitoring/audit-logging/cloud-audit-log-categories.html)
- Detect abnormal behaviour early with stream processing: [Detect anomalies with Confluent Cloud for Apache Flink (`ML_DETECT_ANOMALIES`)](https://docs.confluent.io/cloud/current/ai/builtin-functions/detect-anomalies.html) · [Anomaly detection tutorial (Flink SQL)](https://developer.confluent.io/confluent-tutorials/anomaly-detection/flinksql/) · [Flink ML anomaly detection for agentic investigation and remediation (blog)](https://www.confluent.io/blog/flink-ml-anomaly-detection-for-agentic-investigation-remediation/)
- All built-in AI/ML functions: [Built-in AI/ML functions overview](https://docs.confluent.io/cloud/current/ai/builtin-functions/overview.html)

## 9. Agents and Confluent

- [Build with the open-source MCP server](https://docs.confluent.io/cloud/current/ai/ai-tools/open-source-mcp-server.html) · [mcp-confluent on GitHub](https://github.com/confluentinc/mcp-confluent)

## Takeaway

Data is the attack surface. Secure, govern, validate, observe and trace the streams that your AI depends on.
