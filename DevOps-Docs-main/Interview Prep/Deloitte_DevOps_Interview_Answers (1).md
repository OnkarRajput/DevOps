# Deloitte DevOps Engineer Interview — Detailed Answers with Examples

This document works through both rounds question-by-question, with a clear answer and a concrete example for each so you can adapt the wording to your own project experience.

---

## ROUND 1: Technical Screening

### 1. Explain your end-to-end CI/CD workflow. Which type of Jenkins pipeline do you use and why?

**Answer:**
A typical end-to-end flow looks like:
1. Developer pushes code to a feature branch → opens a PR.
2. Webhook triggers a Jenkins job → checkout, build, unit tests, static code analysis (SonarQube), dependency/security scan.
3. On merge to `main`/`develop`, Jenkins builds a Docker image, tags it (commit SHA + semantic version), pushes to a registry (ECR/Nexus/Harbor).
4. Image is scanned for vulnerabilities (Trivy/Aqua).
5. Deployment stage: Helm chart is updated with the new image tag and applied to a Dev/QA cluster automatically; Staging/Prod usually needs manual approval (`input` step).
6. Post-deploy smoke tests and health checks run; on failure, automatic rollback (`helm rollback` or `kubectl rollout undo`).
7. Notifications sent to Slack/Teams with build + deployment status.

**Pipeline type:** I use a **Declarative pipeline** as the default because it's structured, easier to read/maintain, has built-in syntax validation, and supports shared libraries cleanly. I fall back to **Scripted** only for edge cases needing complex Groovy logic (dynamic stage generation, custom loops/conditionals that declarative syntax can't express easily).

**Example (simplified Jenkinsfile):**
```groovy
pipeline {
  agent { label 'docker' }
  environment { IMAGE = "myrepo/app:${env.GIT_COMMIT}" }
  stages {
    stage('Build & Test') {
      steps { sh 'mvn clean package && mvn test' }
    }
    stage('Docker Build & Push') {
      steps { sh "docker build -t ${IMAGE} . && docker push ${IMAGE}" }
    }
    stage('Deploy to Dev') {
      steps { sh "helm upgrade --install app ./chart --set image.tag=${env.GIT_COMMIT}" }
    }
  }
}
```

---

### 2. Difference between Declarative and Scripted Jenkins pipelines

| Aspect | Declarative | Scripted |
|---|---|---|
| Syntax | Structured, fixed sections (`pipeline`, `stages`, `steps`) | Free-form Groovy code |
| Ease of use | Easier for beginners, more readable | Requires strong Groovy knowledge |
| Flexibility | Limited to defined DSL, some restrictions | Full programmatic control (loops, conditionals, custom logic) |
| Error handling | Built-in `post` blocks (`success`, `failure`, `always`) | Manual `try/catch/finally` |
| Validation | Syntax validated before execution | No upfront validation, fails at runtime |
| Restart | Supports `restartFromStage` | Does not support easily |

**Example:** Declarative uses `pipeline { stages { stage('Build') { steps {...} } } }`, while Scripted wraps everything in a `node { stage('Build') { ... } }` block with plain Groovy.

---

### 3. How do you define and trigger Jenkins pipelines?

**Answer:**
- **Define:** Either inline in the Jenkins job config, or (preferred) as a `Jenkinsfile` checked into the repo, using "Pipeline from SCM."
- **Trigger mechanisms:**
  - **Webhook** from GitHub/GitLab/Bitbucket on push/PR events (fastest, event-driven).
  - **Poll SCM** (`pollSCM('H/5 * * * *')`) — Jenkins periodically checks for changes.
  - **Scheduled builds** (`cron`) for nightly/regression builds.
  - **Manual trigger** via "Build Now" or parameterized builds.
  - **Upstream/downstream** job triggers (`build job: 'deploy-job'`).

**Example:** A GitHub webhook posts to `http://jenkins-url/github-webhook/` on every push; Jenkins' GitHub plugin picks it up and starts the pipeline within seconds, rather than waiting for a poll cycle.

---

### 4. What are Jenkins Shared Libraries? How are they structured and used in Jenkinsfiles?

**Answer:**
Shared Libraries let you centralize reusable pipeline code (common stages, functions, utilities) so multiple Jenkinsfiles across teams don't duplicate logic.

**Structure:**
```
(shared-library-repo)
├── vars/
│   ├── buildApp.groovy       # global functions callable as buildApp()
│   └── deployToK8s.groovy
├── src/
│   └── org/company/Utils.groovy   # Groovy classes
└── resources/
    └── org/company/config.yaml    # non-Groovy resource files
```

**Usage in Jenkinsfile:**
```groovy
@Library('shared-library@main') _
pipeline {
  agent any
  stages {
    stage('Build') { steps { buildApp(appName: 'orders-service') } }
    stage('Deploy') { steps { deployToK8s(env: 'staging') } }
  }
}
```
This keeps Jenkinsfiles thin and standardizes how every team builds/deploys/tests.

---

### 5. What types of applications have you deployed using Jenkins pipelines?

**Answer (frame around your real experience):** Typically covers a mix of:
- Java/Spring Boot microservices deployed as Docker containers to EKS/AKS.
- Node.js/React front-end apps deployed to S3+CloudFront or containerized to K8s.
- Batch/cron jobs deployed as Kubernetes CronJobs.
- Serverless functions (AWS Lambda) packaged as ZIP/container images.
- Infrastructure changes via Terraform pipelines (plan/apply gated by approval).

**Example:** "I built a pipeline for a Spring Boot payments microservice: Maven build → unit + integration tests → SonarQube gate → Docker build → Trivy scan → push to ECR → Helm deploy to EKS with canary rollout via Argo Rollouts."

---

### 6. Which deployment tools have you used (Docker, Kubernetes, Helm, Terraform)?

**Answer:** Frame as: Docker for containerization, Kubernetes for orchestration, Helm for templated/versioned K8s releases, Terraform for provisioning the underlying infra (VPC, EKS cluster, RDS, IAM roles) as code, with remote state in S3 + DynamoDB locking.

**Example:** "Terraform provisions the EKS cluster and node groups; Helm charts define the app's K8s manifests (Deployment, Service, Ingress, HPA); Jenkins glues it together by running `terraform apply` for infra changes and `helm upgrade` for app releases."

---

### 7. If a Jenkins pipeline runs but the build does not trigger, what could be the reasons?

**Answer — common causes:**
- Webhook not configured correctly or blocked by firewall/security group (Jenkins URL not reachable from GitHub/GitLab).
- Branch filters/regex in the job (e.g., "Branches to build") excluding the pushed branch.
- SCM polling interval too long, or polling disabled.
- Jenkinsfile path misconfigured ("Script Path" points to wrong location).
- Multibranch pipeline hasn't re-scanned the repo (needs "Scan Repository Now").
- Credentials for repo checkout expired/invalid — job silently fails to even start.
- Trigger conditions (`when` block, `changeset` filters) not matched.
- Jenkins agent/executor unavailable (no free executors, label mismatch).

**Example:** "We once had webhooks silently failing because GitHub's outbound IP range wasn't whitelisted in our corporate firewall — Jenkins showed no incoming trigger at all. Checking GitHub's webhook delivery logs (Settings → Webhooks → Recent Deliveries) showed a timeout, which pointed us to the network issue."

---

### 8. What is a webhook, and how is it used in a CI/CD pipeline?

**Answer:** A webhook is an HTTP callback — the source system (e.g., GitHub) sends an HTTP POST request to a configured URL whenever an event occurs (push, PR opened, tag created). In CI/CD, this lets Jenkins react instantly to code changes instead of polling.

**Example:** On `git push`, GitHub sends a JSON payload to `https://jenkins.company.com/github-webhook/`. Jenkins' GitHub plugin parses the payload, matches it to the corresponding job/branch, and starts the build immediately — reducing feedback time from minutes (with polling) to seconds.

---

### 9. How do you create and manage Kubernetes clusters using Terraform?

**Answer:** Use the cloud provider's Terraform provider (e.g., `aws_eks_cluster`, `google_container_cluster`) plus supporting resources (VPC, subnets, IAM roles, node groups). Manage state remotely (S3 + DynamoDB lock, or Terraform Cloud) so multiple engineers can collaborate safely.

**Example:**
```hcl
resource "aws_eks_cluster" "main" {
  name     = "prod-cluster"
  role_arn = aws_iam_role.eks.arn
  vpc_config { subnet_ids = var.subnet_ids }
}

resource "aws_eks_node_group" "workers" {
  cluster_name    = aws_eks_cluster.main.name
  node_group_name = "default"
  scaling_config { desired_size = 3, min_size = 2, max_size = 6 }
}
```
Cluster upgrades, node group scaling, and add-ons (CoreDNS, VPC-CNI) are all managed as code, reviewed via PR, and applied through a Terraform pipeline with `plan` → manual approval → `apply`.

---

### 10. What is the role of the master (control plane) and worker nodes?

**Answer:**
- **Control plane (master):** Manages cluster state and scheduling decisions. Key components: `kube-apiserver` (front door for all API calls), `etcd` (cluster's key-value store of truth), `kube-scheduler` (assigns pods to nodes), `kube-controller-manager` (runs controllers like ReplicaSet, Node controller), `cloud-controller-manager` (cloud-specific integration).
- **Worker nodes:** Actually run the application workloads. Key components: `kubelet` (talks to API server, manages pod lifecycle on the node), `kube-proxy` (handles networking/service routing), and the container runtime (containerd/CRI-O).

**Example:** "When you `kubectl apply` a Deployment, the API server stores the desired state in etcd; the scheduler picks a node; the kubelet on that node pulls the image and starts the container, then reports status back to the API server."

---

### 11. What common Kubernetes errors have you faced (CrashLoopBackOff, ImagePullBackOff), and how did you resolve them?

**Answer:**
- **CrashLoopBackOff:** Container starts, crashes, restarts repeatedly. Debug with `kubectl logs <pod> --previous` and `kubectl describe pod`. Common causes: app misconfiguration, missing env vars/secrets, failing liveness probe, unhandled exception on startup, wrong entrypoint command.
- **ImagePullBackOff / ErrImagePull:** Kubernetes can't pull the image. Common causes: wrong image tag/typo, image doesn't exist in the registry, missing/incorrect `imagePullSecrets` for private registries, network/DNS issue reaching the registry.
- **OOMKilled:** Container exceeds its memory limit — fix by tuning `resources.limits.memory` or optimizing the app.
- **Pending pods:** Insufficient cluster resources or node selector/affinity rules that can't be satisfied — check with `kubectl describe pod` for scheduling events.

**Example:** "A pod kept CrashLoopBackOff-ing; `kubectl logs --previous` showed a missing `DATABASE_URL` env var because the ConfigMap name in the Deployment didn't match what was created — fixed by correcting the `configMapRef` name."

---

### 12. How do you access a running pod, and how do you define Kubernetes objects?

**Answer:**
- **Access a pod:** `kubectl exec -it <pod-name> -- /bin/sh` (or `bash`) to get a shell; `kubectl logs <pod-name> -f` to tail logs; `kubectl port-forward <pod-name> 8080:8080` to access it locally.
- **Define objects:** Declaratively, as YAML manifests (Deployment, Service, ConfigMap, Secret, Ingress, etc.) applied with `kubectl apply -f` or via Helm templates. Each manifest has `apiVersion`, `kind`, `metadata`, and `spec`.

**Example:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: orders-service
spec:
  replicas: 3
  selector: { matchLabels: { app: orders } }
  template:
    metadata: { labels: { app: orders } }
    spec:
      containers:
      - name: orders
        image: myrepo/orders:1.2.0
        ports: [{ containerPort: 8080 }]
```

---

### 13. Explain the folder structure of a Helm chart

**Answer:**
```
mychart/
├── Chart.yaml          # chart metadata (name, version, appVersion)
├── values.yaml         # default configuration values
├── charts/             # dependent/sub-charts
├── templates/          # Kubernetes manifest templates (Go templating)
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── _helpers.tpl    # reusable template snippets/functions
│   └── NOTES.txt       # post-install usage notes shown to user
└── .helmignore          # files to exclude when packaging
```

**Example:** `values.yaml` defines `image.tag: 1.0.0`; `templates/deployment.yaml` references it as `{{ .Values.image.tag }}`. Running `helm upgrade --install app ./mychart -f values-prod.yaml --set image.tag=1.2.0` overrides values per environment without duplicating manifests.

---

### 14. What are the stages involved in building a Docker image?

**Answer:**
1. Write a `Dockerfile` specifying a base image (`FROM`), working directory (`WORKDIR`), copying source (`COPY`), installing dependencies (`RUN`), setting env vars (`ENV`), exposing ports (`EXPOSE`), and defining the startup command (`CMD`/`ENTRYPOINT`).
2. `docker build -t myapp:1.0 .` — Docker reads each instruction, creating a cached layer per step.
3. Layers are cached; unchanged layers are reused on rebuild for speed.
4. Multi-stage builds are used to keep the final image small — a "builder" stage compiles/builds the artifact, and a slim final stage (e.g., `alpine`) only copies the compiled output.
5. Tag and push the image to a registry.

**Example (multi-stage build):**
```dockerfile
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre-alpine
COPY --from=builder /app/target/app.jar /app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

---

### 15. What is the difference between ENTRYPOINT and CMD in Docker?

**Answer:**
- **ENTRYPOINT:** Defines the fixed, main command that always runs when the container starts — hard to override (must use `--entrypoint` flag).
- **CMD:** Provides default arguments — easily overridden by passing arguments at `docker run` time.
- When both are used together, `CMD` supplies default arguments to `ENTRYPOINT`.

**Example:**
```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
CMD ["--spring.profiles.active=dev"]
```
Running `docker run myimage` uses the dev profile; running `docker run myimage --spring.profiles.active=prod` overrides just the CMD portion while keeping the ENTRYPOINT fixed.

---

### 16. How do you connect and manage services such as Databases, EC2, EKS, and ECS?

**Answer:**
- **Databases:** Connect via connection strings/secrets stored in AWS Secrets Manager or K8s Secrets, injected as env vars; use IAM database authentication where possible instead of static passwords; manage schema/infra via Terraform (RDS module) and migrations via Flyway/Liquibase.
- **EC2:** Managed via Terraform/CloudFormation for provisioning; SSM Session Manager (preferred over SSH) for secure shell access without opening port 22.
- **EKS:** Cluster provisioned via Terraform/eksctl; `aws eks update-kubeconfig` to get `kubectl` access; IAM Roles for Service Accounts (IRSA) for pod-level AWS permissions.
- **ECS:** Task definitions and services managed via Terraform or `aws ecs` CLI/CDK; Fargate for serverless containers, or EC2 launch type for self-managed instances.

**Example:** "Our microservice on EKS uses IRSA to assume an IAM role granting read access to a specific S3 bucket and Secrets Manager secret containing DB credentials — no hardcoded credentials anywhere."

---

### 17. What is the command used to connect to a running ECS container?

**Answer:** Using ECS Exec (built on SSM):
```bash
aws ecs execute-command \
  --cluster my-cluster \
  --task <task-id> \
  --container my-container \
  --interactive \
  --command "/bin/sh"
```
This requires `enableExecuteCommand: true` on the service/task and the appropriate SSM/IAM permissions — no SSH keys or open ports needed.

---

### 18. Which container registry do you use to store Docker images?

**Answer:** Most commonly **Amazon ECR** (tightly integrated with IAM, ECS/EKS, and vulnerability scanning), sometimes **Docker Hub** (public/simple projects), or **Harbor/Nexus** (self-hosted, on-prem, with fine-grained RBAC and built-in scanning via Trivy/Clair).

**Example:** "We push images to ECR with immutable tags (commit SHA) and use lifecycle policies to expire untagged images older than 30 days to control storage costs."

---

## ROUND 2: In-Depth Technical Screening

### 1. Which branching strategy do you follow (GitFlow / Trunk-based), and how do you avoid breaking the release branch?

**Answer:** Trunk-based development (short-lived feature branches merged frequently into `main`) is common for teams practicing continuous delivery; GitFlow (with `develop`, `release/*`, `hotfix/*` branches) suits products with scheduled/versioned releases.

**Avoiding breaking the release branch:**
- Protect the branch: require PR reviews + passing CI checks before merge.
- Feature flags to merge incomplete work safely without activating it.
- Short-lived feature branches (rebase/merge often to avoid drift).
- Automated gates: unit tests, integration tests, SonarQube quality gate must pass.
- Release branches get only cherry-picked, tested hotfixes — no new feature merges.

**Example:** "We use trunk-based development with feature flags via LaunchDarkly — a half-finished feature merges into `main` behind a flag, so `main` is always deployable, and we cut a `release/x.y` branch only for final hardening/regression testing."

---

### 2. If a critical bug is found in production, what is your approach to fixing it?

**Answer (STAR-style approach):**
1. **Assess impact** — check monitoring/alerts, error rate, affected users; declare an incident if severity warrants it.
2. **Mitigate first** — roll back to the last known good deployment or use feature flag to disable the broken feature, buying time (fastest path to recovery beats a perfect fix).
3. **Root cause** — use logs/traces/metrics to isolate the cause.
4. **Hotfix** — branch off `main`/tag, apply minimal fix, run through expedited but still gated CI (tests + security scan), deploy via the same pipeline (no manual production changes).
5. **Verify** — smoke test, monitor error rates/SLIs post-deploy.
6. **Postmortem** — blameless writeup: timeline, root cause, action items to prevent recurrence.

**Example:** "A payment service started throwing 500s after a config change; we rolled back via `helm rollback` within 4 minutes to restore service, then root-caused a bad feature-flag default, fixed it, and added a pre-deploy config validation step to our pipeline as an action item."

---

### 3. Explain your complete deployment workflow from code commit to production

**Answer:** Commit → PR + code review → CI (build, unit tests, static analysis, security scan) → merge to main → build & push versioned Docker image → deploy to Dev (auto) → automated integration/E2E tests → deploy to Staging (auto or gated) → manual QA/UAT sign-off → deploy to Production (approval-gated, often via canary or blue-green) → post-deploy smoke tests & monitoring → rollback path ready if SLO breach.

**Example:** "We use Argo Rollouts for canary deployments to prod: 10% traffic to the new version, automated analysis checks error rate/latency against baseline for 10 minutes, then progressively increases to 100% if healthy, or auto-aborts and rolls back if metrics degrade."

---

### 4. What stages do you define in your Jenkins pipeline to ensure code quality?

**Answer:** Typical quality stages:
1. **Lint/static analysis** (ESLint, Checkstyle, Pylint).
2. **Unit tests** with coverage threshold enforcement (JaCoCo, Istanbul).
3. **SonarQube quality gate** (code smells, duplication, coverage, vulnerabilities) — pipeline fails if gate fails.
4. **Dependency vulnerability scan** (OWASP Dependency-Check, Snyk).
5. **Integration tests** against a test environment or ephemeral containers (Testcontainers).
6. **Docker image vulnerability scan** (Trivy) before pushing to registry.

**Example:** `waitForQualityGate abortPipeline: true` after the SonarQube analysis step ensures a failed quality gate stops the pipeline before deployment.

---

### 5. How do you design and reuse Jenkins Shared Libraries?

**Answer:** Design principles:
- Keep `vars/*.groovy` functions small and single-purpose (e.g., `dockerBuildPush()`, `notifySlack()`, `deployHelm()`).
- Parameterize with a Map argument for flexibility: `buildApp(name: 'orders', jdkVersion: '17')`.
- Version the library with tags/branches (`@Library('shared-lib@v2.1')`) so teams can pin to a stable version and upgrade deliberately.
- Store common config (registry URLs, cluster names) in `resources/` rather than hardcoding.
- Write tests for library code using the Jenkins Pipeline Unit testing framework.

**Example:** A single `vars/deployToK8s.groovy` function is reused across 20+ microservice Jenkinsfiles — each just calls `deployToK8s(env: 'staging', chart: 'orders-chart')`, so a fix or improvement (e.g., adding a rollback-on-failure check) benefits every pipeline at once.

---

### 6. How do you perform Docker image vulnerability scanning during build time and at the registry level?

**Answer:**
- **Build-time:** Run a scanner (Trivy/Snyk) as a pipeline stage right after `docker build`, before push — fail the build on Critical/High CVEs above a defined threshold.
- **Registry-level:** Enable native scanning (ECR image scanning, Harbor's integrated Trivy/Clair, or Docker Hub scanning) so every pushed image is automatically re-scanned, and scheduled re-scans catch newly disclosed CVEs in images already deployed.

**Example:**
```groovy
stage('Scan Image') {
  steps {
    sh 'trivy image --exit-code 1 --severity CRITICAL,HIGH myrepo/app:${GIT_COMMIT}'
  }
}
```
This fails the pipeline before the image ever reaches production if a critical vulnerability is found.

---

### 7. Which security tools or plugins have you used for image scanning?

**Answer:** Common ones: **Trivy** (open-source, fast, widely used for images + IaC + filesystem scanning), **Snyk Container**, **Aqua Security**, **Clair**, **Anchore Engine**, plus native cloud scanners like **AWS ECR image scanning** (Amazon Inspector-backed). Jenkins integration is typically a shell step calling the CLI, or a dedicated plugin (e.g., Aqua Security Jenkins plugin).

---

### 8. How do you pass environment variables during Docker build and runtime?

**Answer:**
- **Build-time:** `ARG` in the Dockerfile, passed via `docker build --build-arg KEY=value`. Only available during build unless also set as `ENV`.
- **Runtime:** `ENV` in the Dockerfile for defaults, or `docker run -e KEY=value`, or an env file (`--env-file .env`), or (in Kubernetes) via `env`/`envFrom` referencing a ConfigMap/Secret.

**Example:**
```dockerfile
ARG APP_VERSION
ENV APP_VERSION=${APP_VERSION}
```
```bash
docker build --build-arg APP_VERSION=1.2.0 -t myapp:1.2.0 .
docker run -e DATABASE_URL=postgres://... myapp:1.2.0
```
In Kubernetes, sensitive values use `secretKeyRef` rather than plain `env` values to avoid leaking secrets in manifests.

---

### 9. Which services or registries do you use to store Docker images?

**Answer:** Same as Round 1 Q18 — primarily **ECR**, sometimes **Docker Hub**, **GitHub Container Registry (GHCR)**, or self-hosted **Harbor/Nexus**, chosen based on cloud provider integration, RBAC needs, and whether on-prem hosting is required.

---

### 10. How do you establish secure connections with databases?

**Answer:**
- Enforce TLS/SSL for all DB connections (`sslmode=require` for Postgres, etc.).
- Store credentials in **AWS Secrets Manager**/**HashiCorp Vault**, never in code or plain ConfigMaps.
- Prefer **IAM database authentication** (RDS IAM auth) so no long-lived password is needed at all.
- Restrict network access via security groups/VPC — DB should only be reachable from app subnets, not the public internet.
- Rotate credentials automatically (Secrets Manager rotation Lambda).

**Example:** "Our EKS pods use IRSA to get temporary IAM credentials, which are exchanged for a short-lived RDS auth token via the AWS SDK — the password never exists as a static string anywhere in our system."

---

### 11. How do you authenticate to EKS clusters and securely manage secrets?

**Answer:**
- **Authentication:** `aws eks update-kubeconfig --name cluster-name` uses IAM identity mapped to Kubernetes RBAC via the `aws-auth` ConfigMap (or EKS access entries in newer setups) — so cluster access is tied to IAM users/roles, not separate credentials.
- **Secrets management:** Use **IRSA** so pods assume specific IAM roles instead of using node-level credentials; store app secrets in **AWS Secrets Manager** or **Vault**, synced into K8s via the **External Secrets Operator** or **Secrets Store CSI Driver**, rather than storing raw secrets as base64 (which is not encryption) in manifests.

---

### 12. How do you create and deploy AWS Lambda functions?

**Answer:**
- Write function code (Python/Node/Java/Go).
- Package as a ZIP (for interpreted runtimes) or a container image (for larger dependencies, up to 10GB).
- Define infra as code (Terraform's `aws_lambda_function`, or AWS SAM/CDK).
- Set up trigger (API Gateway, S3 event, EventBridge rule, SQS).
- Deploy via CI/CD pipeline: build → package → `aws lambda update-function-code` or Terraform apply → run smoke test invocation.
- Use **versions + aliases** for safe rollout (e.g., alias `prod` pointed at a specific version, shifted gradually with weighted routing).

**Example (Terraform):**
```hcl
resource "aws_lambda_function" "processor" {
  function_name = "order-processor"
  runtime       = "python3.12"
  handler       = "app.handler"
  filename      = "function.zip"
  role          = aws_iam_role.lambda_exec.arn
}
```

---

### 13. What are the different ways to push artifacts to AWS Lambda?

**Answer:**
1. **ZIP upload** directly via `aws lambda update-function-code --zip-file`.
2. **S3-based deployment** — upload ZIP to S3, then reference `s3_bucket`/`s3_key` in the Lambda config (better for larger packages, avoids CLI payload limits).
3. **Container image** — build a Lambda-compatible Docker image, push to ECR, and point the function at that image URI.
4. **Infrastructure-as-Code tools** — Terraform, AWS SAM (`sam deploy`), Serverless Framework, or CDK, which wrap the above under the hood as part of a repeatable deployment.

**Example:** "For a function with heavy ML dependencies exceeding the 250MB unzipped limit, we switched to container image deployment — building the image in our Jenkins pipeline, pushing to ECR, then updating the Lambda function's image URI via Terraform."

---

### 14. What is email signing and Helm chart signing? Which tools are used?

**Answer:**
- **Email signing** (in a DevOps security context, usually referring to **commit/artifact signing**, or literally S/MIME/PGP email signing for verifying sender identity): typically implemented via **GPG** to sign emails or, more relevantly for DevOps, to sign **Git commits/tags** (`git commit -S`) so commit authorship is cryptographically verifiable — GitHub shows a "Verified" badge.
- **Helm chart signing:** Helm supports provenance files (`.prov`) using GPG — `helm package --sign --key 'name' --keyring ~/.gnupg/secring.gpg mychart/` produces a chart tarball plus a `.prov` file. `helm verify` (or `helm install --verify`) checks the signature before installing, ensuring the chart hasn't been tampered with and comes from a trusted source.

**Example:** "We sign every Helm chart release with GPG in the release pipeline, and our internal Helm repo enforces `--verify` on install, so an unsigned or tampered chart is rejected before it ever reaches a cluster."

---

## General Interview Tips

- Use the **STAR method** (Situation, Task, Action, Result) for every behavioral/scenario answer, and quantify outcomes where possible ("reduced deploy time from 40 min to 8 min").
- For scenario questions, narrate your **thought process out loud** — interviewers at the Senior level are grading judgment and trade-off reasoning, not just the final answer.
- Be ready to whiteboard or verbally sketch a pipeline/architecture — draw boxes for stages/components as you talk.
- Research Deloitte's typical client tech stacks beforehand (commonly AWS/Azure, Jenkins/GitHub Actions, Terraform, Kubernetes) so your examples align with tools they're likely to ask follow-ups about.
- Have 2–3 solid real incident stories ready (one clean rollback, one root-cause deep-dive, one where you improved a process afterward) — these get reused across almost every behavioral question.
