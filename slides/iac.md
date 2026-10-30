## Infrastructure as Code (IaC)

----

### The Evolution of Infrastructure Management

| Era | Approach | Failure Mode |
|---|---|---|
| **1. ClickOps** | Manual clicks in cloud consoles | Unreproducible *"Snowflake"* servers, human error |
| **2. Imperative Scripts** | `Bash`, `AWS CLI`, `Boto3` scripts | **Not idempotent** — running twice fails or duplicates |
| **3. Config Management** | `Ansible`, `Chef`, `Puppet` | Mutates long-lived servers (*Configuration Drift*) |
| **4. Declarative IaC** | `Terraform`, `OpenTofu`, `CloudFormation` | **Idempotent state convergence** — engine computes diff |

----

### Core Engineering Principles (`Key Concepts`)

- **Idempotency & Convergence**: Applying configuration `N` times yields the exact same state (`apply(apply(S)) == apply(S)`) with zero side effects
- **Immutable Infrastructure (*Pets vs. Cattle*)**: Replace instances from golden images (**Packer** / containers) via `create_before_destroy` instead of patching in place
- **Separation of Concerns**:
  - **Terraform**: Provisions cloud topology (`VPC`, `EKS`, `RDS`, `IAM`, `S3`, `DNS`)
  - **Packer / Docker**: Builds immutable VM and container artifacts
  - **Kubernetes / Helm / ArgoCD**: Orchestrates application workloads on top
