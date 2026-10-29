## Terraform

----

### What is Terraform?

- Created in **2014** by **HashiCorp** and written in **Go**
- Declarative **Infrastructure as Code (`IaC`)** tool
- Enables teams to safely **build**, **change**, and **version** cloud and on-prem infrastructure

----

### What does it do?

- Provisions and manages **Infrastructure as Code (`IaC`)** across any cloud or service API
- Ensures **consistency** and **reproducibility** across environments (`dev`, `staging`, `prod`)
- Computes an **execution graph** to apply incremental changes in the right dependency order
- Previews exact modifications (`+ create`, `~ update`, `- destroy`) before applying them
- Tears down or replaces resources cleanly when needed

----

### Three Core Workflow Stages

- **Write**: Author declarative infrastructure definitions in `.tf` files using **HCL**
- **Plan**: Generate an execution plan comparing desired configuration against current state
- **Apply**: Provision and converge target infrastructure reproducibly across environments

![Terraform Workflow](img/terraform-workflow.svg)

----

### Example (`main.tf`)

```hcl
resource "aws_s3_bucket" "lgu_bucket" {
  bucket = "lgu-bucket"

  tags = {
    Owner       = "Laurent Grangeau"
    Environment = "production"
    ManagedBy   = "Terraform"
  }
}
```
