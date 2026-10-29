## Concepts

----

### HCL (HashiCorp Configuration Language)

- **Human-readable** declarative configuration language (also compatible with JSON)
- Expresses **desired target state** rather than imperative step-by-step scripts
- Supports expressions, type constraints, built-in functions (`cidrsubnet`, `jsonencode`, `try`), and dynamic blocks (`for_each`, `count`)

----

### Providers

- Responsible for understanding API interactions and managing a resource's **lifecycle**
- Built on **CRUD** operations (*Create / Read / Update / Delete*)
- Over **4,000+ providers** available on the **Terraform Registry**:
  - **Public Clouds**: `AWS`, `GCP`, `Azure`, `OVH`, `DigitalOcean`, `Scaleway`
  - **Platform & Containers**: `Kubernetes`, `Helm`, `Docker`, `Nomad`
  - **HashiCorp Stack**: `Vault`, `Consul`, `Boundary`
  - **DevOps & SaaS**: `GitHub`, `GitLab`, `Datadog`, `Cloudflare`, `PostgreSQL`

----

### Providers Architecture

![Terraform Providers Architecture](img/terraform-providers.svg)

----

### Configuring Providers

- Declare required provider versions and regional settings in `providers.tf` or `versions.tf`:

```hcl
terraform {
  required_version = ">= 1.9.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "eu-central-1"
}
```

----

### Provider Authentication

- **Never commit sensitive credentials** (`Access Keys`, `Secrets`, `Tokens`) in `.tf` files
- Use short-lived credentials (`OIDC`, `IAM Roles`, `Workload Identity`) or environment variables:

```bash
export AWS_ACCESS_KEY_ID="YOUR_ACCESS_KEY"
export AWS_SECRET_ACCESS_KEY="YOUR_SECRET_KEY"
export AWS_DEFAULT_REGION="eu-central-1"
```

----

### Provisioners

- Execute scripts or copy files onto a local or remote resource during creation or destruction
- **Use only as a last resort** — Terraform cannot track changes made by provisioners in its state
- Prefer immutable images built with **Packer**, `cloud-init` / `user_data`, or **Ansible**

----

### Provisioner Types

- Built-in provisioners:
  - `file` — Copies files or directories from the machine running Terraform to the newly created resource
  - `local-exec` — Invokes a local executable after a resource is created
  - `remote-exec` — Invokes a script on a remote resource over `SSH` or `WinRM`

----

### Provisioner Example

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"

  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update && sudo apt-get -y full-upgrade"
    ]

    connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ubuntu"
      private_key = file("~/.ssh/id_ed25519")
    }
  }
}
```

----

### Variables

- Parameterize configurations in `variables.tf` with explicit `type`, `description`, `default`, and `validation` rules:

```hcl
variable "s3_bucket_name" {
  description = "Name of the S3 bucket"
  type        = string
  default     = "lgu-bucket"
}

variable "tags_tool" {
  type    = string
  default = "Terraform"
}

variable "tags_contact" {
  type    = string
  default = "Laurent Grangeau"
}
```

----

### Referencing Variables

- Reference input variables anywhere in `.tf` files using `var.<name>`:

```hcl
resource "aws_s3_bucket" "lgu_bucket" {
  bucket = var.s3_bucket_name

  tags = {
    Tool    = var.tags_tool
    Contact = var.tags_contact
  }
}
```

----

### Modules

- Package and reuse related resources as a **self-contained black box**
- Improve **organization**, **encapsulation**, and **standardization** across teams
- Accept **input variables** and expose **outputs**:

```hcl
module "lambda" {
  source = "./modules/lambda"

  function_name = "order-processor"
  s3_bucket     = var.artifacts_bucket_name
  s3_key        = var.lambda_package_key
  runtime       = "nodejs20.x"
}
```

----

### Outputs

- Expose values from a module or root configuration so other resources, modules, or CI/CD pipelines can consume them:

```hcl
# Inside ./modules/lambda/outputs.tf
output "invoke_arn" {
  description = "Invoke ARN of the Lambda function"
  value       = aws_lambda_function.processor.invoke_arn
}
```

```hcl
# Consuming the module output in root main.tf
resource "aws_api_gateway_authorizer" "custom_authorizer" {
  name                             = "CustomAuthorizer"
  rest_api_id                      = aws_api_gateway_rest_api.main.id
  authorizer_uri                   = module.lambda.invoke_arn
  identity_validation_expression   = "^Bearer [-0-9a-zA-Z\\._]*$"
  authorizer_result_ttl_in_seconds = 30
}
```

----

### Data Sources

- Query and read **existing resources** or external metadata managed outside the current Terraform stack
- **Best practice**: Use `data` sources instead of hardcoding `subnet_id`, `vpc_id`, `ami_id`, or `account_id`

```hcl
# Query an existing subnet by tag
data "aws_subnet" "selected" {
  filter {
    name   = "tag:Name"
    values = ["lgu-subnet"]
  }
}
```

----

### Using Data Sources

- Reference data source attributes via `data.<TYPE>.<NAME>.<ATTRIBUTE>`:

```hcl
module "app_cluster" {
  source    = "./modules/app-cluster"
  subnet_id = data.aws_subnet.selected.id
}
```

----

### Importing Existing Resources

- Bring pre-existing cloud resources under Terraform management for **progressive infrastructure migration**
- Supports both the CLI command and the declarative `import` block (Terraform `1.5+`):

```bash
$ terraform import aws_instance.frontend i-0abcd1234efgh5678
```

```hcl
# Declarative import block (Terraform 1.5+)
import {
  to = aws_instance.frontend
  id = "i-0abcd1234efgh5678"
}

resource "aws_instance" "frontend" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"

  tags = {
    Name = "Frontend-HelloWorld"
  }
}
```

----

### State (`terraform.tfstate`)

- JSON snapshot mapping your declarative `.tf` resources to real-world provider IDs since the last `terraform apply`
- Stored locally by default (`terraform.tfstate`), but should be stored in a **Remote Backend** for teams

![Terraform State Architecture](img/terraform-state.svg)

----

### Remote State Backend

- Store state remotely with **encryption at rest** and **state locking** to prevent concurrent writes:

```hcl
# Backend configuration is loaded early, so variables cannot be interpolated here
terraform {
  backend "s3" {
    region         = "eu-central-1"
    bucket         = "lgu-terraform-state"
    key            = "prod/network/terraform.tfstate"
    encrypt        = true
    dynamodb_table = "terraform-state-locks"
  }
}
```

----

### Workspaces

- Manage multiple isolated state files (`dev`, `preprod`, `prod`) from the **same configuration directory**

```bash
$ terraform workspace list            # List all existing workspaces
$ terraform workspace new <name>      # Create and switch to a new workspace
$ terraform workspace select <name>   # Switch to an existing workspace
$ terraform workspace delete <name>   # Delete an empty workspace
```

```bash
$ terraform workspace list
  default
* dev
  preprod
  prod
```

```hcl
bucket = terraform.workspace == "preprod" ? var.bucket_demo_preprod : var.bucket_demo
```
