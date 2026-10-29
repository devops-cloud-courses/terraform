## CLI (Command Line Interface)

----

### `terraform init`

- Initializes a working directory containing `.tf` configuration files
- Must be run first when bootstrapping a new configuration or after cloning an existing repository from Git

----

### What `terraform init` does

- Downloads and installs required **providers** (and verifies SHA-256 hashes in `.terraform.lock.hcl`)
- Initializes the configured **state backend** (`local`, `s3`, `gcs`, `azurerm`, `cloud`)
- Downloads and installs referenced **modules**

```bash
$ terraform init
$ terraform init -upgrade      # Upgrade providers/modules within version constraints
$ terraform init -reconfigure  # Reconfigure backend settings
```

----

### `terraform providers`

- Displays the tree of providers required by the current configuration and its child modules:

```bash
$ terraform providers

Providers required by configuration:
.
├── provider[registry.terraform.io/hashicorp/aws] ~> 6.0
└── module.my_module
    ├── provider[registry.terraform.io/hashicorp/aws]
    └── provider[registry.terraform.io/hashicorp/tls]
```

----

### `terraform test`

- Executes native automated unit and integration tests (`tests/*.tftest.hcl`) against your Terraform modules using real resources or mock providers (`mock_provider`):

```bash
$ terraform test
tests/s3_bucket.tftest.hcl... in progress
  run "valid_bucket_naming"... pass
tests/s3_bucket.tftest.hcl... tearing down
tests/s3_bucket.tftest.hcl... pass

Success! 1 passed, 0 failed.
```

----

### `terraform plan`

- Generates a deterministic **execution plan**
- Compares desired `.tf` configuration against the current state and target cloud APIs to determine which actions (`+ create`, `~ update`, `- destroy`, `-/+ replace`) are needed

----

### Saving & Optimizing Plans

- `terraform plan` is a **dry run** — it never modifies real infrastructure
- Save the plan to a binary file, detect drift, or speed up large states with `-minimal-refresh`:

```bash
$ terraform plan -out=plan.out          # Save exact plan for apply
$ terraform plan -refresh-only          # Detect drift without changing resources
$ terraform plan -minimal-refresh       # Only refresh resources with proposed changes (v1.17+)
```

----

### `terraform graph`

- Outputs a visual dependency graph (in Graphviz `DOT` format) showing how Terraform orders parallel resource operations in its **DAG**:

```bash
$ terraform graph | dot -Tsvg > graph.svg
```

![Terraform Dependency Graph](img/terraform-graph.svg)

----

### `terraform apply`

- Executes the actions proposed in a Terraform plan to create, update, or replace real infrastructure resources

```bash
$ terraform apply plan.out
```

----

### `terraform console`

- Opens an interactive shell to evaluate HCL expressions, test built-in functions, and inspect live state attributes:

```bash
$ echo "aws_iam_user.lgu.arn" | terraform console
"arn:aws:iam::123456789012:user/lgrangeau"
```

----

### `terraform destroy`

- Destroys **all** remote objects managed by the current Terraform configuration and state

```bash
$ terraform destroy
```

- Preview or save a destruction plan beforehand:

```bash
$ terraform plan -destroy
```
