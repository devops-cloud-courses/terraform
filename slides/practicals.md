## Hands-on Practice

----

### Installation

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

### Initialization (`init`)

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

### Formatting, Validation & Testing (`fmt`, `validate`, `test`)

- Format `.tf` files, validate schema/types, and run `.tftest.hcl` unit tests before planning:

```bash
$ terraform fmt -recursive
$ terraform validate
Success! The configuration is valid.

$ terraform test
Success! 3 passed, 0 failed.
```

----

### Execution Plan (`plan`)

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

### Applying Changes (`apply`)

```bash
$ terraform apply plan.out

aws_s3_bucket.lgu_bucket: Creating...
aws_s3_bucket.lgu_bucket: Creation complete after 2s [id=lgu-bucket-prod]
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

- Export a machine-readable JSON representation of the current state or saved plan file:

```bash
$ terraform show -json plan.out > plan.json
```
