## Comparisons, Pros & Cons

----

### IaC Tools Comparison

| Tool | Cloud Support | Language | State Management |
|---|---|---|---|
| `Terraform` | Multi-cloud (`4,000+` providers) | Declarative (`HCL` / `JSON`) | Local or Remote Backend (`tfstate`) |
| `CloudFormation` | AWS only | Declarative (`YAML` / `JSON`) | Managed natively by AWS Stacks |
| `AWS CDK` | AWS (and `CDKTF` / `cdk8s`) | Imperative (`TS`, `Python`, `Go`, `C#`, `Java`) | Synthesizes to CloudFormation |
| `Pulumi` | Multi-cloud | Imperative (`TS`, `Python`, `Go`, `C#`, `YAML`) | Pulumi Cloud or Self-managed |

----

### Comparison: Terraform

- Supports almost all **AWS**, **GCP**, **Azure**, and SaaS services via a unified workflow
- Vast ecosystem of community modules and providers (**Terraform Registry** & **OpenTofu** compatible)
- Declarative **HCL** / **JSON** syntax purpose-built for infrastructure
- Explicit **State management** with remote backends and locking

----

### Comparison: AWS CloudFormation

- Deep native support for **AWS services** (AWS-only)
- **Fully managed** and free service provided by AWS (no external state bucket to set up)
- Native **automatic stack rollback** on deployment failure and EC2 Auto Scaling rolling updates
- Written in **YAML** or **JSON** (can become verbose on large stacks)

----

### Comparison: AWS CDK

- Targets **AWS** by synthesizing high-level code into CloudFormation templates
- **Open source** framework with rich higher-level abstractions (**L2 / L3 Constructs**)
- Uses general-purpose programming languages (`TypeScript`, `JavaScript`, `Python`, `Go`, `C#`, `Java`)
- Supports importing existing resources via `cdk import` / CloudFormation resource import

----

### Comparison: Pulumi

- **Multi-cloud** support (can bridge any Terraform provider plus native cloud APIs)
- Uses general-purpose programming languages (`TypeScript`, `Python`, `Go`, `C#`, `Java`, `YAML`)
- Built-in **State management** (Pulumi Cloud or self-hosted S3/GCS/Blob backend) with native secret encryption
- Full programming constructs (loops, unit test frameworks, classes), which require strict team conventions

----

### Terraform: Pros

- Standardizes **Infrastructure as Code (`IaC`)** across multi-cloud and hybrid environments
- **Concise, readable declarative syntax** (`HCL`) easy for both Ops and Devs to review
- Strong **modularity and reuse** via variables, locals, outputs, and versioned modules
- Safe **`terraform plan`** dry-run workflow before any production change
- Massive community, provider ecosystem, and security/linting tooling (`tflint`, `trivy`, `checkov`)

----

### Terraform: Cons

- **No automatic rollback** on partial `apply` failure (requires fixing forward or re-running `apply`)
- **State file management** overhead (must secure, lock, and avoid drift in `terraform.tfstate`)
- **Plaintext secrets in state**: Sensitive values referenced in resources are stored in `tfstate` (mitigated with ephemeral values in Terraform `1.10+` and external secret managers)
- Can become repetitive without well-structured modules
