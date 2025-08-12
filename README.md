
# Serverless & Event-Driven Services — Azure vs AWS vs GCP (CST8917)

This report compares the **11 Azure services** used in whole course with their closest **AWS** and **GCP** equivalents.  
For each service we include:
- **Overview**
- **Core Features** (triggers, bindings, messaging/eventing capabilities)
- **Integration Options** (with other cloud services and CI/CD)
- **Monitoring & Observability**
- **Pricing Model** (high-level)
- **Strengths & Weaknesses** (serverless perspective)
---

## 🔎 Quick Mapping Table

| # | Azure | AWS | GCP |
|---|---|---|---|
| 1 | **Azure Functions** | **AWS Lambda** | **Cloud Functions** |
| 2 | **Azure Durable Functions** | **AWS Step Functions** (with Lambda) | **Cloud Workflows** |
| 3 | **Azure Logic Apps** | **Step Functions** + **EventBridge** (+ Lambda) | **Cloud Workflows** + **Eventarc** |
| 4 | **Azure Storage (Blob)** | **Amazon S3** | **Cloud Storage** |
| 5 | **Azure Blob Storage** (explicit) | **Amazon S3** | **Cloud Storage** |
| 6 | **Azure Cosmos DB** | **Amazon DynamoDB** | **Firestore** / **Cloud Spanner** (use‑case dependent) |
| 7 | **Azure Service Bus** (Queues/Topics) | **SQS** + **SNS** | **Pub/Sub** |
| 8 | **Azure Event Hubs** | **Kinesis Data Streams** / **Amazon MSK** | **Pub/Sub** |
| 9 | **Azure Event Grid** | **Amazon EventBridge** | **Eventarc** |
| 10 | **Azure Relay** | Closest: **API Gateway (private integrations via VPC Link)** / **AWS PrivateLink** | No direct equivalent; closest patterns via **API Gateway + VPC** / **VPC Service Controls** |
| 11 | **Azure IoT Hub** | **AWS IoT Core** | **Cloud IoT Core** (*deprecated*) → partner solutions + Pub/Sub |

---

## 1) Azure Functions ↔ AWS Lambda ↔ Google Cloud Functions

**Overview**  
Event-driven serverless compute for running code without managing servers.

**Core Features**  
- **Triggers/Bindings (Azure):** HTTP, Timer, Queue/Service Bus, Blob, Event Hub, Event Grid, Cosmos DB, SignalR, etc. Output bindings simplify I/O.  
- **Lambda:** Triggers via API Gateway/ALB, SQS/SNS, EventBridge, DynamoDB Streams, S3, Kinesis, CloudWatch, IoT, etc.  
- **Cloud Functions:** HTTP, Pub/Sub, Cloud Storage, Firestore events, Cloud Scheduler (via Pub/Sub), Eventarc sources.

**Integration Options**  
- **Azure:** GitHub Actions/Azure DevOps; Bicep/Terraform; Managed Identity to access Azure services.  
- **AWS:** CodePipeline/CodeBuild; SAM/CDK/Terraform; IAM roles; VPC access.  
- **GCP:** Cloud Build/Deploy; Terraform; Workload Identity Federation; VPC access.

**Monitoring & Observability**  
- **Azure:** Application Insights (traces/metrics), Azure Monitor logs, Live Metrics, distributed tracing.  
- **AWS:** CloudWatch metrics/logs, X-Ray tracing.  
- **GCP:** Cloud Monitoring/Logging, Cloud Trace/Profiler/Error Reporting.

**Pricing Model**  
Requests + GB‑seconds (execution time × memory), plus egress/invocations beyond free tier. Optional provisioned concurrency billed.

**Strengths & Weaknesses**  
- **Strengths:** Fast iteration, auto-scale, rich event sources, bindings (Azure) reduce boilerplate.  
- **Weaknesses:** Cold starts, execution time limits; stateful/long-running workflows require orchestration.

---

## 2) Azure Durable Functions ↔ AWS Step Functions ↔ Cloud Workflows

**Overview**  
Stateful serverless orchestration for function chaining, retries, human interaction, and long-running processes.

**Core Features**  
- **Azure Durable:** Orchestrator/Activity/Client functions, durable timers, external events, fan‑out/in, sub-orchestrations; state checkpointing in Storage.  
- **Step Functions:** Express/Standard workflows, service integrations, error handling, retries, map state, callbacks (task tokens), synchronous/async.  
- **Cloud Workflows:** YAML-based workflows, connectors, long‑running execution, error handling, retries, callbacks.

**Integration Options**  
- Integrates with each cloud’s serverless, data, and messaging services; IaC via ARM/Bicep/SAM/CDK/Terraform; CI/CD pipelines to deploy state machines.

**Monitoring & Observability**  
- **Azure:** Durable history, Application Insights traces; instance status APIs.  
- **AWS:** Execution history, CloudWatch metrics/logs, X-Ray.  
- **GCP:** Execution logs, Monitoring/Logging, Error Reporting.

**Pricing Model**  
Per-step/execution pricing (varies by Standard/Express or region); plus underlying function invocations and service calls.

**Strengths & Weaknesses**  
- **Strengths:** Code‑first (Durable), rich error handling, compensations, timers.  
- **Weaknesses:** Vendor lock-in on workflow DSL/runtimes; visibility/limits differ per platform.

---

## 3) Azure Logic Apps ↔ Step Functions + EventBridge ↔ Cloud Workflows + Eventarc

**Overview**  
Low-code/no-code workflow automation with hundreds of connectors.

**Core Features**  
- **Logic Apps:** Triggers (HTTP, schedule, event), actions, control flow, connectors (M365, SAP, SQL, Service Bus, etc.), B2B/EDI, Integration Service Environment.  
- **AWS stack:** Step Functions for orchestration; EventBridge for event routing; Lambda for custom steps; rich SaaS integrations.  
- **GCP:** Cloud Workflows + Eventarc + connectors (via GCF/Cloud Run).

**Integration Options**  
- Connects to SaaS/on‑prem via Hybrid connections/VNET ISE; CI/CD with ARM/Bicep/Terraform; GitHub/Azure DevOps. AWS/GCP use IaC and event buses for similar patterns.

**Monitoring & Observability**  
Run history, metrics, retries; logs to Monitor/Log Analytics (Azure), CloudWatch (AWS), Cloud Logging (GCP).

**Pricing Model**  
Per-action/connector/execution; standard vs consumption tiers; network/egress billed separately.

**Strengths & Weaknesses**  
- **Strengths:** Rapid integration, minimal code, enterprise connectors.  
- **Weaknesses:** Per‑action costs can add up; complex logic may become hard to version/control vs code‑based orchestrators.

---

## 4–5) Azure Storage / Azure Blob Storage ↔ Amazon S3 ↔ Google Cloud Storage

**Overview**  
Highly durable, scalable object storage for unstructured data and event triggers.

**Core Features**  
Object buckets/containers, versioning, lifecycle rules, event notifications (to Functions/Lambda/PubSub), immutability/WORM, multipart uploads, static website hosting.

**Integration Options**  
Native triggers for serverless; data lake analytics (ADLS/S3‑LakeFormation/GCS + BigQuery); CI artifacts storage; CDN integration.

**Monitoring & Observability**  
Storage metrics, access logs, data access/audit logs; integrate with SIEM/monitoring suites.

**Pricing Model**  
Per‑GB storage by tier (hot/cool/archive), requests (PUT/GET), data retrieval (for cold tiers), egress. Lifecycle policies reduce cost.

**Strengths & Weaknesses**  
- **Strengths:** Durability, scale, event hooks, low cost.  
- **Weaknesses:** Egress costs; eventual consistency behaviors (provider‑specific); access policies can be complex.

---

## 6) Azure Cosmos DB ↔ Amazon DynamoDB ↔ Firestore / Cloud Spanner

**Overview**  
Managed NoSQL/planet‑scale databases (DynamoDB/Cosmos) and globally consistent relational/NoSQL (Spanner/Firestore).

**Core Features**  
- **Cosmos DB:** Multi‑model (Core API, Mongo, Cassandra, Gremlin, Table), global distribution, multi‑master, configurable consistency, change feed triggers.  
- **DynamoDB:** Key‑value/Document, global tables, DAX cache, Streams for triggers, on‑demand or provisioned capacity.  
- **Firestore/Spanner:** Firestore (document, realtime), Spanner (relational with global consistency/SQL).

**Integration Options**  
Serverless triggers (Cosmos change feed, DynamoDB Streams, Firestore triggers), data pipelines, IaC and CI/CD for schema/policies.

**Monitoring & Observability**  
Throughput/consumption metrics, latency, errors; query insights; logs to monitoring stacks.

**Pricing Model**  
RU/s (Cosmos), read/write capacity or on‑demand (DynamoDB), document/operation pricing (Firestore), node/storage/compute (Spanner).

**Strengths & Weaknesses**  
- **Strengths:** Scale, low‑latency, serverless triggers, multi‑region.  
- **Weaknesses:** Capacity/RU planning; hot partitions; cross‑region costs/latency; Spanner requires larger spend/fit.

---

## 7) Azure Service Bus (Queues/Topics) ↔ SQS + SNS ↔ Pub/Sub

**Overview**  
Reliable messaging for decoupling services; queues and pub/sub.

**Core Features**  
- **Service Bus:** Queues, Topics + Subscriptions, sessions (FIFO‑like), dead‑lettering, transactions, scheduled/deferred messages, filters/actions.  
- **AWS:** SQS (standard/FIFO), SNS topics; message attributes; DLQs; FIFO with deduplication; filtering (SNS).  
- **GCP Pub/Sub:** Topics/subscriptions, ordering keys, filtering, push/pull, DLQs, exactly‑once (with conditions).

**Integration Options**  
Triggers for Functions/Lambda/Cloud Functions; event routing with Event Grid/EventBridge/Eventarc; IaC and pipeline deploys.

**Monitoring & Observability**  
Queues depth/lag, throughput, age; delivery failures; logs to Monitor/CloudWatch/Cloud Logging; tracing via APM.

**Pricing Model**  
Per‑request/message, data transfer, long‑polling, throughput units (where applicable).

**Strengths & Weaknesses**  
- **Strengths:** Decoupling, reliability, DLQs, filtering, scheduling.  
- **Weaknesses:** Ordering tradeoffs (standard vs FIFO); visibility tuning; poison message handling complexity.

---

## 8) Azure Event Hubs ↔ Kinesis Data Streams / Amazon MSK ↔ Pub/Sub

**Overview**  
High‑throughput event streaming ingestion (telemetry, logs, clickstreams).

**Core Features**  
Partitions/shards, consumer groups, checkpointing (EventProcessor/KCL), capture to storage, Kafka protocol support (Event Hubs for Kafka, MSK).

**Integration Options**  
Stream processing (Functions/Lambda, Stream Analytics/Kinesis Analytics/Dataflow), data lake sinks, SIEM ingestion.

**Monitoring & Observability**  
Lag metrics, throughput, partition health, consumer errors; logs to platform monitors.

**Pricing Model**  
Throughput/processing units (Event Hubs), shard hours (Kinesis), MSK cluster resources, per‑message egress/storage.

**Strengths & Weaknesses**  
- **Strengths:** Massive ingestion, replay (streaming), ecosystem (Kafka).  
- **Weaknesses:** Consumer complexity; partition key design; stateful stream processing adds ops complexity.

---

## 9) Azure Event Grid ↔ Amazon EventBridge ↔ Eventarc

**Overview**  
Serverless event routing (pub/sub) with schema, filtering, and fan‑out to targets.

**Core Features**  
Event sources (Azure services, custom topics), advanced filtering, dead‑lettering, retry policies, CloudEvents support (Event Grid/Eventarc), SaaS integrations.

**Integration Options**  
Targets: Functions, Logic Apps, WebHooks, Service Bus, Event Hubs; AWS: Lambda/Step Functions/SQS; GCP: Cloud Run/Functions/Workflows.

**Monitoring & Observability**  
Delivery metrics, failure logs, dead‑letter destinations, replay depends on backend (Eventarc/EventBridge features vary).

**Pricing Model**  
Per‑million events published/delivered; egress to downstream services billed separately.

**Strengths & Weaknesses**  
- **Strengths:** Loose coupling, scalable fan‑out, CloudEvents support.  
- **Weaknesses:** No long‑term storage; use with streaming (Event Hubs/Kinesis) when replay is required.

---

## 10) Azure Relay ↔ API Gateway (private integrations via VPC Link) / AWS PrivateLink ↔ —

**Overview**  
Securely expose on‑premises services to the cloud without opening inbound firewall ports; clients connect through a relay endpoint.

**Core Features**  
Hybrid connections (HTTP, TCP), outbound-only connectivity from on‑prem to Azure, relayed requests, authentication/authorization controls.

**Integration Options**  
Used alongside Logic Apps/Functions/API Mgmt to call on‑prem services. In AWS, similar patterns achieved with **API Gateway private integrations + VPC Link**, **PrivateLink**, **Direct Connect/VPN**. GCP: combine **API Gateway/Cloud Run** with **VPC** and **Hybrid Connectivity**; there is no direct drop‑in managed relay.

**Monitoring & Observability**  
Connection status, throughput, failures; platform logs to monitoring stacks.

**Pricing Model**  
Charges for relay listeners/relayed data (bandwidth/time).

**Strengths & Weaknesses**  
- **Strengths:** Simple hybrid access without inbound ports; dev-friendly.  
- **Weaknesses:** Not a full API gateway; latency overhead; limited protocol options vs bespoke network links.

---

## 11) Azure IoT Hub ↔ AWS IoT Core ↔ (*GCP IoT Core deprecated*)

**Overview**  
Managed IoT device ingestion and management (devices ↔ cloud).

**Core Features**  
Device identity/registry, bi‑directional messaging (device-to-cloud and cloud-to-device), MQTT/AMQP/HTTP, twin/state sync, per‑device auth, rules routing.

**Integration Options**  
Ingest to Event Hubs/Stream Analytics/Functions; AWS IoT rules to Kinesis/Lambda; GCP typically uses partner IoT platforms + Pub/Sub after IoT Core deprecation.

**Monitoring & Observability**  
Device metrics, connection stats, message counts; route logs to monitoring platforms; per‑device diagnostics.

**Pricing Model**  
Per‑message tiers, device counts, features (DPS/provisioning optional), egress. AWS similar per‑message pricing; GCP via partner pricing + Pub/Sub charges.

**Strengths & Weaknesses**  
- **Strengths:** Scalable device management, secure protocols, rules engines and routing.  
- **Weaknesses:** Edge cases (offline/patchy connectivity), security lifecycle management effort, cross‑region routing costs.

---
