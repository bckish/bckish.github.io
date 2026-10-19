# Portfolio Interview Playbook

This is a truthful discussion guide for the 26 public portfolio repositories. Use it to explain what you built, why the design was chosen, how you validated it, and what you would improve. Do **not** describe a learning or demonstration repository as a production system you operated for a customer.

## A reliable interview structure

For every project, answer in this order:

1. **Problem:** what engineering problem the design addresses.
2. **Design:** the main components and the flow between them.
3. **Operational proof:** how you validate, observe, secure, or recover it.
4. **Trade-off:** what is deliberately simplified and what you would add in production.

## 1. AWS EKS Three-Tier Platform

- **Problem:** provide a repeatable foundation for a web, application, and data-tier workload on Kubernetes.
- **Design:** EKS hosts separate application components; ingress/load balancing exposes the web tier while workload configuration is declared in infrastructure and Kubernetes manifests.
- **Interview focus:** explain network boundaries, workload identity, autoscaling, health probes, and why stateful data needs a separate durability and backup strategy.
- **Validation and next step:** run Terraform validation and inspect Kubernetes readiness. In production, add private endpoints, IRSA, network policies, multi-AZ data services, alerts, and disaster-recovery exercises.

## 2. Azure AKS GitOps Platform

- **Problem:** reduce configuration drift in AKS deployments.
- **Design:** Git is the source of truth; the GitOps controller reconciles desired manifests into the cluster. Application and infrastructure changes are reviewed before merge.
- **Interview focus:** distinguish pull-based GitOps from push-based deployment, and explain reconciliation, drift correction, environment promotion, and secrets handling.
- **Trade-off:** a demo can use simple manifests; production needs protected branches, workload identity, a secret operator, policy enforcement, and alerting for failed reconciliation.

## 3. Terraform EKS Platform

- **Problem:** create a consistent EKS baseline without console-driven configuration.
- **Design:** Terraform defines the VPC, EKS control plane, worker capacity, and supporting IAM resources as reusable infrastructure.
- **Interview focus:** discuss remote state, locking, provider version pinning, least-privilege IAM, plan review, and module boundaries.
- **Strong answer:** “Terraform makes the desired infrastructure reviewable. The critical production control is not `apply`; it is protected state, reviewed plans, and a tested change process.”

## 4. Terraform AWS Three-Tier

- **Problem:** provision a conventional three-tier AWS application landscape consistently.
- **Design:** Terraform models network, compute/load-balancing, and database-facing layers with explicit dependencies and outputs.
- **Interview focus:** explain public versus private subnets, security groups as stateful controls, ALB health checks, and why databases should not be internet reachable.
- **Improve:** add encrypted remote state, secrets injection, backups, WAF, alarms, and cost budgets.

## 5. Go, EKS, GitHub Actions & Helm

- **Problem:** safely deliver a containerized Go service to Kubernetes.
- **Design:** GitHub Actions builds and tests the image; Helm packages Kubernetes configuration; EKS runs the release.
- **Interview focus:** image tagging, immutable release artifacts, chart values per environment, rollback with Helm, and how to prevent the pipeline from deploying unreviewed code.
- **Validation:** build the image, run unit checks, lint the chart, and verify the deployed Service and Pods.

## 6. DevSecOps EKS, Jenkins & Argo CD

- **Problem:** add security checks before a Kubernetes release while separating build and deployment responsibilities.
- **Design:** Jenkins runs CI activities; image and code checks gate promotion; Argo CD reconciles the reviewed deployment configuration into EKS.
- **Interview focus:** explain why Jenkins should not directly mutate a production cluster when GitOps can provide an auditable pull-based deployment trail.
- **Improve:** use short-lived cloud credentials, signed images, SBOMs, admission control, and severity-based vulnerability exceptions with expiry.

## 7. Java CI/CD Kubernetes

- **Problem:** automate the path from Java source code to a running Kubernetes workload.
- **Design:** the pipeline compiles/tests the application, builds a container image, then deploys versioned manifests.
- **Interview focus:** Maven dependency caching, test stages, container-layer optimization, readiness/liveness probes, and release rollback.
- **Pitfall to avoid:** never say a successful image build proves the application is ready; health checks and deployment verification are separate controls.

## 8. AWS CodePipeline EC2 Delivery

- **Problem:** implement AWS-native continuous delivery to an EC2-hosted workload.
- **Design:** CodePipeline orchestrates source, build, and deployment stages; the instance receives a versioned artifact.
- **Interview focus:** contrast this VM model with a Kubernetes rollout, including host patching, scaling, deployment-agent lifecycle, and blue/green alternatives.
- **Improve:** use Auto Scaling groups, immutable images, traffic shifting, CloudWatch alarms, and an automated rollback condition.

## 9. EKS GitOps Observability

- **Problem:** make Kubernetes operations observable while preserving declarative configuration.
- **Design:** GitOps deploys the observability stack and application instrumentation; metrics and dashboards support operational feedback.
- **Interview focus:** golden signals (latency, traffic, errors, saturation), alert fatigue, cardinality, dashboard ownership, and SLO-based alerting.
- **Demonstrate:** show a workload, its metrics endpoint, a dashboard, and one actionable alert path.

## 10. EKS RBAC Least Privilege

- **Problem:** prevent over-privileged Kubernetes access.
- **Design:** Roles and RoleBindings grant narrowly scoped verbs to defined identities or service accounts.
- **Interview focus:** namespace-scoped Role versus ClusterRole, service-account identity, permission review with `kubectl auth can-i`, and regular access recertification.
- **Improve:** combine RBAC with workload identity, Pod Security Admission, NetworkPolicies, audit logging, and policy-as-code.

## 11. Helm Chart Publishing

- **Problem:** package Kubernetes applications as reusable, versioned deployment artifacts.
- **Design:** a Helm chart contains templates, values, and version metadata; a chart repository makes releases discoverable and repeatable.
- **Interview focus:** chart version versus application version, values layering, `helm lint`, template rendering, semantic versioning, and backward compatibility.
- **Trade-off:** charts are powerful but can become unreadable; use clear defaults, schema validation, and keep templates deliberately small.

## 12. Kafka Cluster Infrastructure

- **Problem:** establish the infrastructure concerns around a distributed streaming platform.
- **Design:** the project focuses on cluster configuration, broker availability, storage, and client connectivity patterns.
- **Interview focus:** partitions, replication factor, ISR, consumer groups, retention, ordering guarantees, and why Kafka capacity planning is storage and network intensive.
- **Improve:** add TLS, ACLs, Schema Registry, exporter metrics, replication monitoring, and a disaster-recovery replication design.

## 13. AWS Serverless Terraform

- **Problem:** create event-driven AWS services consistently with Infrastructure as Code.
- **Design:** Terraform declares Lambda and the surrounding AWS resources, allowing plan-driven changes instead of console setup.
- **Interview focus:** least-privilege execution roles, Lambda retries and DLQs, cold starts, API Gateway throttling, observability, and state management.
- **Strong answer:** “Serverless removes server management, not operational responsibility. I still need monitoring, concurrency controls, error handling, and cost visibility.”

## 14. S3 Lambda Image Resizer

- **Problem:** resize uploaded images automatically without running a persistent worker.
- **Design:** an S3 object event triggers Lambda, which transforms the image and writes a derivative to a target location.
- **Interview focus:** event loops, object-key filters, idempotency, retries, permissions, memory sizing, and malformed upload handling.
- **Improve:** isolate original and derived buckets, use queues for buffering, add antivirus/content checks, lifecycle policies, and structured logs.

## 15. Rekognition Serverless Image Analysis

- **Problem:** enrich uploaded media with managed image-analysis results.
- **Design:** a serverless workflow accepts an object event, calls Rekognition, and records or returns the analysis outcome.
- **Interview focus:** data privacy, confidence thresholds, human review for sensitive decisions, error handling, API quotas, and cost management.
- **Important framing:** describe it as an engineering integration demonstration, not as a model-quality or policy guarantee.

## 16. AWS FinOps Automation

- **Problem:** turn cloud-cost visibility into an actionable operating practice.
- **Design:** cost data and resource signals are collected to identify spend anomalies or optimization candidates.
- **Interview focus:** tagging quality, allocation, budgets, anomaly detection, rightsizing, commitment planning, and the difference between saving money and reducing business value.
- **Improve:** notify owners with context, require approval for destructive automation, and measure savings against a baseline.

## 17. Ansible Infrastructure Automation

- **Problem:** make host configuration repeatable and reduce manual drift.
- **Design:** idempotent playbooks describe packages, services, configuration files, and deployment tasks.
- **Interview focus:** inventories, variables, roles, idempotency, check mode, secrets with Ansible Vault, and how Ansible differs from Terraform.
- **Strong answer:** “Terraform declares infrastructure resources; Ansible is well suited to configuring the operating system and software on those resources.”

## 18. MERN Docker Compose

- **Problem:** provide a reproducible local environment for a multi-service web application.
- **Design:** Docker Compose coordinates frontend, backend, and database services with networks, volumes, and environment configuration.
- **Interview focus:** container networking, persistent volumes, environment separation, health checks, build contexts, and why Compose is primarily a local-development tool.
- **Improve:** add non-root containers, image scanning, secrets management, database backups, and a Kubernetes deployment path.

## 19. K8sGPT & Ollama Cluster Triage

- **Problem:** explore AI-assisted interpretation of Kubernetes cluster symptoms.
- **Design:** cluster information is collected and passed to a local-model workflow to suggest likely causes and next investigation steps.
- **Interview focus:** protecting sensitive cluster data, human review, prompt/context quality, rate limits, hallucination risk, and the difference between suggestion and remediation.
- **Safe framing:** “AI assists triage; it does not receive authority to change production infrastructure.”

## 20. Azure DevOps App Service CI/CD

- **Problem:** continuously deliver an application to Azure App Service.
- **Design:** Azure DevOps pipelines build, test, package, and deploy an application artifact to an App Service target.
- **Interview focus:** service connections, deployment slots, configuration separation, Key Vault references, rollback by slot swap, Application Insights, and release approvals.
- **Improve:** use workload identity instead of long-lived secrets and promote the same immutable artifact across environments.

## 21. AWS Disaster Recovery & Backup Automation

- **Problem:** apply consistent backup coverage and retention to cloud resources selected by tag.
- **Design:** Terraform creates a KMS-encrypted AWS Backup vault, daily plan, scoped service role, and tag-based selection for `backup = daily` resources.
- **Interview focus:** RPO versus RTO, encryption, retention cost, cross-account/cross-region recovery, recovery testing, and why a backup is not proof of recoverability.
- **Validation:** `terraform init -backend=false`, `fmt -check`, and `validate` pass locally. A real deployment needs AWS credentials and a reviewed plan because it creates billable resources.

## 22. ELK Log Aggregation Pipeline

- **Problem:** centralize structured application logs for search and incident investigation.
- **Design:** a JSON log generator writes to a shared volume; Filebeat ships the records to Elasticsearch; Kibana provides exploration.
- **Interview focus:** structured versus unstructured logs, index lifecycle management, mapping/cardinality, PII redaction, retention, and access control.
- **Validation:** Docker Compose configuration is validated locally. Start Docker Desktop to run the full stack and create the `portfolio-logs-*` data view in Kibana.

## 23. Container Security Scanning Pipeline

- **Problem:** stop images with serious known vulnerabilities from reaching a release stage.
- **Design:** GitHub Actions builds a small non-root Python image, scans it with Trivy, and produces a CycloneDX SBOM artifact.
- **Interview focus:** CVE severity versus exploitability, base-image updates, SBOM purpose, false-positive exception process, image signing, and registry admission control.
- **Validation:** source and workflow are checked locally. The image build requires Docker Desktop to be running; CI is designed to apply the vulnerability gate on pull requests and main-branch pushes.

## 24. Kubernetes Blue-Green Delivery

- **Problem:** release a new version with a quick, predictable traffic switch and simple rollback.
- **Design:** blue and green Deployments run independently. A stable Service selector routes traffic to the active color; the promotion script changes only that selector after readiness succeeds.
- **Interview focus:** readiness gates, rollback time, database compatibility, cost of duplicate capacity, and why blue-green is different from canary delivery.
- **Validation:** manifests build with Kustomize. Use kind or another local cluster for the full promotion test, then confirm Service endpoints switch to the selected release.

## 25. Database Migration CI/CD

- **Problem:** deliver schema changes safely alongside application releases.
- **Design:** PostgreSQL runs locally with Flyway applying immutable, versioned SQL files. GitHub Actions starts the stack and verifies the migrated table.
- **Interview focus:** backward-compatible migrations, expand/contract changes, migration ordering, locks, rollback strategy, backup validation, and why destructive changes need a separate approval path.
- **Validation:** Docker Compose configuration is validated locally. With Docker Desktop running, `docker compose up --wait` runs the migration and permits an SQL verification query.

## 26. Terraform Policy as Code

- **Problem:** catch infrastructure violations before cloud changes are applied.
- **Design:** Terraform produces a plan; OPA/Conftest policy denies missing ownership/environment tags and public-IP assignment in AWS subnets.
- **Interview focus:** policy-as-code versus IAM, shift-left controls, policy testing, justified exceptions, enforcement locations, and preventing policy bypass.
- **Validation:** Terraform formatting and validation pass locally. Install Conftest to evaluate the Rego guardrails against a generated plan JSON.

## Cross-project interview questions

### How do you decide between Terraform, Ansible, Helm, and GitOps?

Terraform manages cloud infrastructure and declarative provider resources. Ansible configures hosts and software. Helm packages Kubernetes applications. GitOps is the operating model in which a controller reconciles reviewed Git state into a cluster. They solve different layers and can be used together.

### How do you secure a CI/CD pipeline?

Use protected branches, code review, short-lived credentials, least-privilege service identities, dependency and image scanning, SBOMs, signed artifacts, secret scanning, approval gates for risky environments, and auditable deployment records. Security controls must also have an exception process and expiry.

### How do you make a platform reliable?

Define service objectives, instrument meaningful signals, test failure paths, automate safe recovery, keep runbooks current, restrict blast radius, and rehearse restore or rollback. Reliability is demonstrated through recovery and operational feedback, not architecture diagrams alone.

### What should you say when asked whether these are production systems?

“These are public engineering demonstrations built to practice and communicate real platform patterns. I can explain the design, local validation, and the additional controls I would require before production use. I do not present them as customer production systems.”
