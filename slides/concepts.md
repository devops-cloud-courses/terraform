## Concepts

----

### HCL (HashiCorp Configuration Language)

- **Human-readable** declarative configuration language (also compatible with JSON)
- Expresses **desired target state** rather than imperative step-by-step scripts
- Supports rich type constraints (`object`, `map`, `set`), built-in functions (`cidrsubnet`, `jsonencode`, `try`), meta-arguments (`for_each`, `count`, `lifecycle`), and `check` assertions

----

### Providers

- Responsible for understanding API interactions and managing a resource's **lifecycle**
- Built on **CRUD** operations (*Create / Read / Update / Delete*) over `gRPC`
- Over **4,000+ providers** available on the **Terraform Registry**:
  - **Public Clouds**: `AWS`, `GCP`, `Azure`, `OCI`, `OVH`, `Scaleway`
  - **Platform & Containers**: `Kubernetes`, `Helm`, `Docker`, `Talos`
  - **Security & Identity**: `Vault`, `Okta`, `Cloudflare`, `TLS`
  - **DevOps & Data**: `GitHub`, `GitLab`, `Datadog`, `PostgreSQL`, `Snowflake`

----

### Providers Architecture

![Terraform Providers Architecture](img/terraform-providers.svg)

----

### Configuring Providers

- Pin Terraform and provider versions in `versions.tf` (checksums locked in `.terraform.lock.hcl`):

```hcl
terraform {
  required_version = ">= 1.17.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = "eu-central-1"
}
```

----

### Provider Authentication

- **Never commit static credentials** (`Access Keys`, `Secrets`, `Tokens`) in `.tf` files
- Prefer **short-lived identity federation** (`OIDC` in GitHub Actions / GitLab CI, `AWS SSO` / `IAM Roles`) or environment variables:

```bash
export AWS_ACCESS_KEY_ID="YOUR_ACCESS_KEY"
export AWS_SECRET_ACCESS_KEY="YOUR_SECRET_KEY"
export AWS_REGION="eu-central-1"
```

----

### Variables & Validation

- Define typed inputs in `variables.tf` with custom `validation` rules:

```hcl
variable "s3_bucket_name" {
  type    = string
  default = "lgu-bucket-prod"

  validation {
    condition     = can(regex("^[a-z0-9-]+$", var.s3_bucket_name))
    error_message = "Bucket name must be lowercase alphanumeric and hyphens."
  }
}

variable "tags_contact" {
  type    = string
  default = "Laurent Grangeau"
}
```

----

### Referencing Variables & Locals

- Combine input variables (`var.<name>`) and reusable `locals` inside `.tf` files:

```hcl
locals {
  common_tags = {
    Tool    = "Terraform"
    Contact = var.tags_contact
  }
}

resource "aws_s3_bucket" "lgu_bucket" {
  bucket = var.s3_bucket_name
  tags   = local.common_tags
}
```

----

### Data Sources

- Query **existing cloud resources**, dynamic AMI IDs, or external metadata
- **Best practice**: Never hardcode `subnet_id`, `vpc_id`, `ami_id`, or `account_id`

```hcl
# Dynamically fetch the latest Amazon Linux 2023 ARM64 AMI via SSM
data "aws_ssm_parameter" "al2023_ami" {
  name = "/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-arm64"
}

# Query an existing subnet by tag
data "aws_subnet" "selected" {
  filter {
    name   = "tag:Name"
    values = ["lgu-subnet-private"]
  }
}
```

----

### Using Data Sources

- Reference data source attributes via `data.<TYPE>.<NAME>.<ATTRIBUTE>`:

```hcl
resource "aws_instance" "frontend" {
  ami           = data.aws_ssm_parameter.al2023_ami.value
  instance_type = "t4g.micro"
  subnet_id     = data.aws_subnet.selected.id

  tags = {
    Name = "Frontend-Server"
  }
}
```

----

### Outputs

- Expose values from a module or root configuration for other modules or CI/CD pipelines:

```hcl
# 1. Expose value in ./modules/lambda/outputs.tf
output "invoke_arn" {
  description = "Invoke ARN of the Lambda function"
  value       = aws_lambda_function.processor.invoke_arn
}

# 2. Consume module output in root main.tf
resource "aws_api_gateway_authorizer" "custom_authorizer" {
  name           = "CustomAuthorizer"
  rest_api_id    = aws_api_gateway_rest_api.main.id
  authorizer_uri = module.lambda.invoke_arn
}
```

----

### Scaling Resources: `for_each` vs. `count`

- **Avoid `count` for non-identical resources**: Removing `[0]` shifts indices (`[1] -> [0]`), recreating resources!
- **Prefer `for_each`** keyed by stable map keys or a `set(string)`:

```hcl
variable "availability_zones" {
  default = { a = "10.0.1.0/24", b = "10.0.2.0/24" }
}

resource "aws_subnet" "public" {
  for_each          = var.availability_zones
  vpc_id            = aws_vpc.main.id
  availability_zone = "eu-central-1${each.key}"
  cidr_block        = each.value
  tags              = { Name = "public-${each.key}" }
}
# State addresses: aws_subnet.public["a"], aws_subnet.public["b"]
```

----

### Modules

- Package and reuse related resources as a **versioned, self-contained component**
- Accept **input variables** and expose **outputs** for standardization across teams:

```hcl
module "lambda" {
  source        = "./modules/lambda"
  function_name = "order-processor"
  s3_bucket     = var.artifacts_bucket_name
  s3_key        = var.lambda_package_key
  runtime       = "nodejs22.x"
  architectures = ["arm64"]
}
```

----

### State (`terraform.tfstate`)

- JSON snapshot mapping declarative `.tf` resources to real-world cloud IDs
- Stored locally by default (`terraform.tfstate`), or in a **Remote Backend** for teams

![Terraform State Architecture](img/terraform-state.svg)

----

### Remote State Backend & Native S3 Locking

- Store state remotely with **encryption at rest** and **native S3 state locking (`use_lockfile = true`)**
- *Note: `use_lockfile = true` uses S3 conditional writes (`.tflock`) and replaces the deprecated `dynamodb_table` lock table!*

```hcl
terraform {
  backend "s3" {
    region       = "eu-central-1"
    bucket       = "lgu-terraform-state"
    key          = "prod/network/terraform.tfstate"
    encrypt      = true
    use_lockfile = true
  }
}
```

----

### Ephemeral Values & Secrets (`Terraform 1.10+`)

- Standard resource attributes are saved in `terraform.tfstate` — use **`ephemeral` resources** and **write-only arguments (`_wo`)** so secrets are **never stored** in state or plan files:

```hcl
ephemeral "aws_secretsmanager_secret_version" "db_password" {
  secret_id = "prod/rds/master-password"
}

resource "aws_db_instance" "postgres" {
  identifier          = "prod-postgres"
  engine              = "postgres"
  instance_class      = "db.t4g.micro"
  username            = "dbadmin"
  password_wo         = ephemeral.aws_secretsmanager_secret_version.db_password.secret_string
  password_wo_version = 1
}
```

----

### Workspaces

- Manage multiple isolated state files (`dev`, `preprod`, `prod`) from the **same configuration directory**:

```bash
$ terraform workspace new dev         # Create and switch to a new workspace
$ terraform workspace select preprod  # Switch to an existing workspace
$ terraform workspace list            # List workspaces (* indicates active)
  default
* dev
  preprod
  prod
```

```hcl
bucket = terraform.workspace == "preprod" ? var.bucket_demo_preprod : var.bucket_demo
```

----

### Production State Architecture & Blast Radius

- **Avoid CLI Workspaces alone in Prod**: Sharing one backend across `dev` and `prod` increases **blast radius**
- **Best Practice**: Isolate environments by **directory & account** (`envs/prod/`) and split state into **layers**:

![Terraform Layered State Architecture](img/terraform-layers.svg)

----

### Resource Lifecycle & Guardrails (`lifecycle`)

- Customize how Terraform creates, updates, and protects critical resources:

```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ssm_parameter.al2023_ami.value
  instance_type = "t4g.micro"
  lifecycle {
    create_before_destroy = true                  # Zero-downtime replacement
    prevent_destroy       = true                  # Block accidental deletion
    ignore_changes        = [tags["LastScanned"]] # Ignore external mutators
    postcondition {
      condition     = self.public_dns != ""
      error_message = "Instance must be assigned a public DNS hostname."
    }
  }
}
```

----

### Config-Driven Import & Refactoring

- Bring existing cloud resources under Terraform management and generate HCL automatically (`terraform plan -generate-config-out=generated.tf`)
- Use declarative `import`, `moved`, and `removed` blocks to refactor state addresses without destroying infrastructure:

```hcl
import {
  to = aws_instance.frontend
  id = "i-0abcd1234efgh5678"
}

moved {
  from = aws_instance.old_name
  to   = aws_instance.frontend
}
```

----

### Continuous Infrastructure Assertions (`check`)

- Unlike `validation` or `postcondition` (which halt execution), `check` blocks verify **live end-to-end health** after `apply` and report warnings on drift:

```hcl
check "web_health_check" {
  data "http" "index" {
    url = "https://${aws_instance.web.public_dns}/healthz"
  }

  assert {
    condition     = data.http.index.status_code == 200
    error_message = "Web server returned HTTP ${data.http.index.status_code}"
  }
}
```

----

### Provisioners (Legacy / Last Resort)

- Execute scripts (`local-exec`, `remote-exec`) or copy files (`file`) during creation/destruction
- **Discouraged by HashiCorp** — Terraform cannot track imperative changes made by provisioners
- Prefer immutable images (**Packer**), `user_data` / `cloud-init`, or **SSM / Ansible**:

```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ssm_parameter.al2023_ami.value
  instance_type = "t4g.micro"
  user_data     = file("${path.module}/cloud-init.yaml")
}
```
