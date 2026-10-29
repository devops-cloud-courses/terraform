## Hands-on Practice

----

### 1. Installation

- Install Terraform via package manager (`brew`, `apt`, `dnf`) or version manager (`tfenv` / `mise`):

```bash
# macOS / Linux (Homebrew)
$ brew tap hashicorp/tap
$ brew install hashicorp/tap/terraform

# Or direct Linux binary (v1.17.0)
$ curl -fsSLO https://releases.hashicorp.com/terraform/1.17.0/terraform_1.17.0_linux_amd64.zip
$ sudo unzip terraform_1.17.0_linux_amd64.zip -d /usr/local/bin
```

```bash
$ terraform --version
Terraform v1.17.0
on darwin_arm64
```

----

### 2. Initialization (`init`)

```bash
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/aws versions matching "~> 6.0"...
- Installing hashicorp/aws v6.70.0...
- Installed hashicorp/aws v6.70.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository.

Terraform has been successfully initialized!
```

----

### 3. Formatting, Validation & Testing (`fmt`, `validate`, `test`)

- Format `.tf` files, validate schema/types, and run `.tftest.hcl` unit tests before planning:

```bash
$ terraform fmt -recursive
$ terraform validate
Success! The configuration is valid.

$ terraform test
Success! 3 passed, 0 failed.
```

----

### 4. Execution Plan (`plan`)

```bash
$ terraform plan -out=plan.out

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:
  # aws_s3_bucket.lgu_bucket will be created
  + resource "aws_s3_bucket" "lgu_bucket" {
      + bucket = "lgu-bucket-prod"
      + id     = (known after apply)
    }
...
Plan: 8 to add, 0 to change, 0 to destroy.
Saved the plan to: plan.out
```

----

### 5. Inspecting the Saved Plan (`show`)

- Export a machine-readable JSON representation of `plan.out` (used by policy & cost scanners before `apply`):

```bash
$ terraform show -json plan.out > plan.json
```

----

### 6. Applying Changes (`apply`)

```bash
$ terraform apply plan.out

aws_s3_bucket.lgu_bucket: Creating...
aws_s3_bucket.lgu_bucket: Creation complete after 2s [id=lgu-bucket-prod]
...
Apply complete! Resources: 8 added, 0 changed, 0 destroyed.
```

----

### 7. Tearing Down (`destroy`)

```bash
$ terraform destroy

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:
...
Plan: 0 to add, 0 to change, 8 to destroy.
...
Destroy complete! Resources: 8 destroyed.
```

----

### Production GitOps, DevSecOps & FinOps Pipeline

- In production, **nobody runs `terraform apply` from a laptop** — changes flow through Pull Requests with automated security, policy, and cost gates:

![Terraform GitOps Pipeline](img/terraform-gitops.svg)

----

### Shift-Left Security (`Trivy` / `OPA`) & FinOps (`Infracost`)

- Scan `.tf` files and `plan.json` in CI/CD before allowing any merge to `main`:

```bash
# 1. Linting & provider-aware rules
$ tflint --recursive

# 2. Security & misconfiguration scan (e.g., public S3 buckets, open 0.0.0.0/0 SSH)
$ trivy config .

# 3. Policy as Code evaluation (Open Policy Agent / Rego against plan.json)
$ opa exec --decision terraform/analysis/authz --bundle policy/ plan.json

# 4. FinOps cloud cost diff posted directly on the Pull Request
$ infracost diff --path=plan.json
# Monthly cost change: +$42.10 (+12%)
```

----

### MSc Capstone Lab: Multi-AZ Cloud Architecture

- Build, test, and validate a modular AWS topology (using your **AWS Sandbox Account** or locally for free via **LocalStack** + `tflocal`):

![MSc Terraform Lab Architecture](img/terraform-lab.svg)

----

### MSc Lab Requirements & Acceptance Criteria

1. **State & Providers**: Configure `terraform >= 1.17.0`, `hashicorp/aws ~> 6.0`, and an S3 backend with `use_lockfile = true`
2. **Dynamic Networking (`for_each`)**: Create a VPC (`10.0.0.0/16`) and provision 2 public subnets across `eu-central-1a` and `eu-central-1b` using `for_each` (no `count`!)
3. **Compute & Data Sources**: Lookup the latest **Amazon Linux 2023 ARM64 AMI** dynamically via `data "aws_ssm_parameter"` and deploy `t4g.micro` instances with `create_before_destroy = true`
4. **Secure Storage**: Create an `aws_s3_bucket` with `aws_s3_bucket_versioning` enabled and a variable `validation` rule enforcing lowercase naming
5. **Verification**: Add a `check` block or `tests/main.tftest.hcl` unit test suite and pass `terraform fmt -check`, `terraform validate`, and `terraform test`
