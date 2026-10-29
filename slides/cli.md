## CLI (Command Line Interface)

----

### `terraform init`

- Initializes a working directory containing `.tf` configuration files
- Must be run first when bootstrapping a new configuration or after cloning an existing repository from Git

----

### What `terraform init` does

- Downloads and installs required **providers** (and records checksums in `.terraform.lock.hcl`)
- Initializes the configured **state backend** (`local`, `s3`, `gcs`, `azurerm`, `cloud`)
- Downloads and installs referenced **modules**

```bash
$ terraform init
```

----

### `terraform plan`

- Generates a deterministic **execution plan**
- Compares desired `.tf` configuration against the current state and target cloud APIs to determine which actions (`+ create`, `~ update`, `- destroy`, `-/+ replace`) are needed

----

### Saving an Execution Plan

- `terraform plan` is a **dry run** — it never modifies real infrastructure
- Save the plan to a binary file so `terraform apply` executes those exact approved changes:

```bash
$ terraform plan -out=plan.out
```

----

### `terraform apply`

- Executes the actions proposed in a Terraform plan to create, update, or replace real infrastructure resources

```bash
$ terraform apply plan.out
```

----

### `terraform providers`

- Displays the tree of providers required by the current configuration and its child modules:

```bash
$ terraform providers

Providers required by configuration:
.
├── provider[registry.terraform.io/hashicorp/aws] ~> 5.0
└── module.my_module
    ├── provider[registry.terraform.io/hashicorp/aws]
    └── provider[registry.terraform.io/hashicorp/external]
```

----

### `terraform destroy`

- Destroys **all** remote objects managed by the current Terraform configuration and state

```bash
$ terraform destroy
```

- You can also preview or save a destruction plan beforehand:

```bash
$ terraform plan -destroy
```

----

### `terraform console`

- Opens an interactive shell to evaluate expressions, test built-in functions, and inspect resource attributes before planning or applying:

```bash
$ echo "aws_iam_user.lgu.arn" | terraform console
"arn:aws:iam::123456789012:user/lgrangeau"
```

----

### `terraform get`

- Downloads and updates child modules mentioned in the root module (`terraform get -update`):

```bash
$ terraform get -update
```

----

### `terraform graph`

- Outputs a visual dependency graph (in Graphviz `DOT` format) showing how Terraform orders resource creation:

```bash
$ terraform graph | dot -Tpng > graph.png
```

![Terraform Dependency Graph](img/terraform-graph.svg)
