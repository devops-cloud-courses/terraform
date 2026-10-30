## Introduction

----

## How to explore this doc

* This presentation uses [Reveal.js framework](https://revealjs.com/#/)
* Press <kbd>Esc</kbd> to zoom out to all slides or zoom in
* Press <kbd>?</kbd> on your keyboard to get navigation help

<div style="margin-top: 1.2em;">
  <a href="#/" class="btn-primary">Go back to first slide</a>
</div>

----

### Who knows Terraform?

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
