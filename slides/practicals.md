## Hands-on Practice

----

### Installation

- Install Terraform via package manager (`brew`, `apt`, `yum`) or official binary release:

```bash
# macOS / Linux (Homebrew)
$ brew tap hashicorp/tap
$ brew install hashicorp/tap/terraform

# Or direct Linux binary
$ curl -fsSLO https://releases.hashicorp.com/terraform/1.9.8/terraform_1.9.8_linux_amd64.zip
$ sudo unzip terraform_1.9.8_linux_amd64.zip -d /usr/local/bin
```

```bash
$ terraform --version
Terraform v1.9.8
```

----

### Initialization (`init`)

```bash
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/aws versions matching "~> 5.0"...
- Installing hashicorp/aws v5.70.0...
- Installed hashicorp/aws v5.70.0 (signed by HashiCorp)

Terraform has been successfully initialized!
```

----

### Formatting & Validation (`fmt` & `validate`)

- Automatically format `.tf` files to canonical style and validate syntax before planning:

```bash
$ terraform fmt -recursive
$ terraform validate
Success! The configuration is valid.
```

----

### Execution Plan (`plan`)

```bash
$ terraform plan

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:
  # aws_s3_bucket.lgu_bucket will be created
  + resource "aws_s3_bucket" "lgu_bucket" {
      + bucket = "lgu-bucket"
      + id     = (known after apply)
    }
...
Plan: 8 to add, 0 to change, 0 to destroy.
```

----

### Applying Changes (`apply`)

```bash
$ terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:
...
Plan: 8 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Enter a value: yes
...
Apply complete! Resources: 8 added, 0 changed, 0 destroyed.
```

----

### Tearing Down (`destroy`)

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

### Inspecting State (`show`)

- Export a machine-readable JSON representation of the current state or saved plan file (ideal for policy checks like `OPA` / `Checkov`):

```bash
$ terraform show -json
```
