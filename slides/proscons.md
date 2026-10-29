## Comparisons, Pros & Cons

----

### IaC Tools Comparison

| Tool | Cloud Support | Language & License | State Management |
|---|---|---|---|
| `Terraform` | Multi-cloud (`4,000+` providers) | Declarative `HCL` / `JSON` (`BUSL 1.1`) | Remote Backend (`S3` `.tflock`, `GCS`, `HCP`) |
| `OpenTofu` | Multi-cloud (Terraform Registry / OpenTofu) | Declarative `HCL` / `JSON` (`MPL 2.0` Linux Foundation) | Remote Backend + Native Client-Side State Encryption |
| `CloudFormation` | AWS only | Declarative `YAML` / `JSON` (AWS Managed) | Managed natively by AWS CloudFormation Stacks |
| `AWS CDK` | AWS (and `cdk8s`) | Imperative (`TS`, `Python`, `Go`, `C#`, `Java`) | Synthesizes to CloudFormation Stacks |
| `Pulumi` | Multi-cloud | Imperative (`TS`, `Python`, `Go`, `C#`, `YAML`) | Pulumi Cloud or Self-managed (`S3` / `GCS` / `AzBlob`) |

----

### Comparison: Terraform & OpenTofu

- Supports almost all **AWS**, **GCP**, **Azure**, **Kubernetes**, and SaaS services via a unified workflow
- **Terraform** (by HashiCorp / IBM, `BUSL 1.1` license) and **OpenTofu** (Linux Foundation `MPL 2.0` open-source fork) share the HCL language and provider ecosystem
- Native testing (`terraform test`), config-driven refactoring (`import`, `moved`, `removed`), and native S3 locking (`use_lockfile = true`)

----

### Comparison: AWS CloudFormation

- Deep native support for **AWS services** (AWS-only)
- **Fully managed** and free service provided by AWS (no external state bucket to set up)
- Native **automatic stack rollback** on deployment failure and drift detection
- Written in **YAML** or **JSON** (can become verbose on large multi-account architectures)

----

### Comparison: AWS CDK

- Targets **AWS** by synthesizing high-level code into CloudFormation templates
- **Open source** framework with rich higher-level abstractions (**L2 / L3 Constructs**)
- Uses general-purpose programming languages (`TypeScript`, `Python`, `Go`, `C#`, `Java`)
- Supports importing existing resources via `cdk import` and CloudFormation resource import

----

### Comparison: Pulumi

- **Multi-cloud** support (bridges Terraform providers plus native cloud APIs like `aws-native`)
- Uses general-purpose programming languages (`TypeScript`, `Python`, `Go`, `C#`, `Java`, `YAML`)
- Built-in **State management** and secret encryption (Pulumi Cloud / ESC or self-hosted backends)
- Full programming constructs (loops, unit test frameworks, classes), requiring strict team conventions

----

### Terraform: Pros

- De facto standard for **multi-cloud and hybrid Infrastructure as Code (`IaC`)**
- **Concise, readable declarative syntax (`HCL`)** easy for both Platform Engineers and Developers to review
- Strong **modularity and reuse** via typed variables, validations, outputs, and versioned modules
- Safe **`terraform plan`**, native **`terraform test`**, and **Policy as Code** before any production change
- Modern secret protection with **`ephemeral` resources** and **write-only (`_wo`) arguments** (`v1.10+`)

----

### Terraform: Cons

- **No automatic rollback** on partial `apply` failure (requires fixing forward and re-running `apply`)
- **State management responsibility**: Teams must secure, back up, and avoid manual out-of-band drift against `terraform.tfstate`
- **Legacy secret exposure**: Standard (non-`_wo`) sensitive attributes are still persisted in `tfstate`, requiring encrypted remote backends and strict IAM access controls
- **License fragmentation**: HashiCorp's switch to `BUSL 1.1` split the ecosystem between **Terraform** and **OpenTofu**
