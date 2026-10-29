## Introduction

----

### Who knows Terraform?

----

### The Evolution of Infrastructure Management

| Era | Approach | Failure Mode |
|---|---|---|
| **1. ClickOps** | Manual clicks in AWS/GCP/Azure consoles | Unreproducible, undocumented *"Snowflake"* servers, human error |
| **2. Imperative Scripts** | `Bash`, `AWS CLI`, `Boto3` scripts | **Not idempotent** — running a script twice fails or duplicates resources |
| **3. Config Management** | `Ansible`, `Chef`, `Puppet` | Great for OS packages, but mutates long-lived servers (*Configuration Drift*) |
| **4. Declarative IaC** | `Terraform`, `OpenTofu`, `CloudFormation` | **Idempotent state convergence** — declare *what* you want, engine computes the diff |

----

### Core Engineering Principles (`MSc Key Concepts`)

- **Idempotency & Convergence**: Applying the same configuration `N` times yields the exact same infrastructure state (`apply(apply(S)) == apply(S)`) with zero unintended side effects
- **Immutable Infrastructure (*Pets vs. Cattle*)**: Instead of SSH-ing into servers to patch them in place, replace instances from golden images (**Packer** / containers) via `create_before_destroy`
- **Separation of Concerns**:
  - **Terraform**: Provisions cloud topology (`VPC`, `EKS`, `RDS`, `IAM`, `S3`, `DNS`)
  - **Packer / Docker**: Builds immutable VM/container artifacts
  - **Kubernetes / Helm / ArgoCD**: Orchestrates application workloads on top

----

## How to explore this doc

* This presentation uses [Reveal.js framework](https://revealjs.com/#/)
* Press <kbd>Esc</kbd> to zoom out to all slides or zoom in
* Press <kbd>?</kbd> on your keyboard to get navigation help

<div style="margin-top: 1.2em;">
  <a href="#/" class="btn-primary">Go back to first slide</a>
</div>
