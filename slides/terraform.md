## Terraform

----

### What is Terraform?

- Created in **2014** by **HashiCorp (IBM)** in **Go** — the de facto standard for **Infrastructure as Code (`IaC`)**
- Safely **build**, **change**, **test**, and **version** infrastructure across any cloud or SaaS API

![Terraform Overview](img/terraform-overview.svg)

----

### What does it do?

- Provisions and manages **Infrastructure as Code (`IaC`)** across any cloud or SaaS API
- Ensures **consistency** and **reproducibility** across environments (`dev`, `staging`, `prod`)
- Builds a **Directed Acyclic Graph (`DAG`)** to apply incremental changes in parallel
- Previews exact modifications (`+ create`, `~ update`, `- destroy`, `-/+ replace`) before applying
- Detects **configuration drift** and enforces **Policy as Code**

----

### Three Core Workflow Stages

- **Write**: Author declarative infrastructure definitions in `.tf` files using **HCL**
- **Plan**: Preview exact execution changes comparing desired code against real state
- **Apply**: Provision and converge target infrastructure reproducibly across environments

![Terraform Workflow](img/terraform-workflow.svg)

----

### Example (`main.tf`)

- Modern AWS Provider (`v6.x`) uses dedicated sub-resources for bucket settings:

```hcl
resource "aws_s3_bucket" "lgu_bucket" {
  bucket = "lgu-bucket-prod"
  tags   = { Owner = "Laurent Grangeau", Environment = "production" }
}

resource "aws_s3_bucket_versioning" "lgu_bucket" {
  bucket = aws_s3_bucket.lgu_bucket.id
  versioning_configuration {
    status = "Enabled"
  }
}
```
