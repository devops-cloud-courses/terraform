# Terraform in a Nutshell

An interactive presentation on **Terraform, Infrastructure as Code (IaC), and Cloud DevSecOps**, powered by [Reveal.js 6.0.2](https://revealjs.com/).

* **Live Presentation:** [https://devops-cloud-courses.github.io/terraform](https://devops-cloud-courses.github.io/terraform)
* **Download PDF (Latest Release):** [terraform-in-a-nutshell.pdf](https://github.com/devops-cloud-courses/terraform/releases/latest/download/terraform-in-a-nutshell.pdf)
* **Browser Print View:** [https://devops-cloud-courses.github.io/terraform/?print-pdf&pdfSeparateFragments=false](https://devops-cloud-courses.github.io/terraform/?print-pdf&pdfSeparateFragments=false)

---

## Course Curriculum

1. **Introduction & Engineering Principles** (`slides/introduction.md`)
   * The evolution of infrastructure management (`ClickOps` → `Imperative Scripts` → `Config Management` → `Declarative IaC`)
   * **Idempotency & Convergence** (`apply(apply(S)) == apply(S)`) and **Immutable Infrastructure** (*Pets vs. Cattle*)
   * **Separation of Concerns** across `Terraform`, `Packer` / `Docker`, and `Kubernetes` / `Helm` / `ArgoCD`
2. **Overview of Terraform** (`slides/terraform.md`)
   * History, multi-cloud & SaaS ecosystem, and Directed Acyclic Graph (`DAG`) parallel execution
   * The three core workflow stages: **Write → Plan → Apply**
   * Declarative HCL configuration example (`main.tf`) with AWS Provider `v6.x`
3. **Core Concepts** (`slides/concepts.md`)
   * **HCL (HashiCorp Configuration Language)**: rich types, built-in functions, and declarative target state
   * **Providers**: `gRPC` plugin architecture, version pinning (`versions.tf`, `.terraform.lock.hcl`), and OIDC / short-lived authentication
   * **Variables, Locals & Data Sources**: typed inputs with `validation` rules, reusable `locals`, and dynamic `data` lookups (`aws_ssm_parameter` AL2023 ARM64 AMI)
   * **Outputs, Scaling & Modules**: exposing module outputs, `for_each` vs. `count` index-shift pitfalls, and reusable modules
   * **State & Secret Management**: remote state (`terraform.tfstate`), native S3 state locking (`use_lockfile = true`), and **Ephemeral Values & Write-Only (`_wo`) arguments** (`Terraform 1.10+`)
   * **Production Architecture & Guardrails**: CLI Workspaces vs. layered state architecture (`blast radius` isolation), `lifecycle` meta-arguments (`create_before_destroy`, `prevent_destroy`, `ignore_changes`, `postcondition`), config-driven `import` & `moved` blocks, continuous `check` assertions, and `provisioners`
4. **Terraform CLI** (`slides/cli.md`)
   * Bootstrapping and inspecting providers (`terraform init`, `-upgrade`, `-reconfigure`, `terraform providers`)
   * Automated native testing (`terraform test` with `.tftest.hcl` and mock providers)
   * Execution plans & optimization (`terraform plan -out`, `-refresh-only`, `-minimal-refresh`) and dependency visualization (`terraform graph`)
   * Applying changes, interactive debugging (`terraform console`), and tearing down infrastructure (`terraform destroy`)
5. **Comparisons, Pros & Cons** (`slides/proscons.md`)
   * Side-by-side comparison of **Terraform**, **OpenTofu**, **AWS CloudFormation**, **AWS CDK**, and **Pulumi**
   * Key strengths (multi-cloud ecosystem, declarative HCL, modularity, `plan` / `test` safety) and trade-offs (state management, no automatic rollback, `BUSL 1.1` vs. `MPL 2.0`)
6. **Hands-on Practice & DevSecOps Pipeline** (`slides/practicals.md`)
   * End-to-end CLI workflow (`init` → `fmt` / `validate` / `test` → `plan` → `show -json` → `apply` → `destroy`)
   * **Production GitOps, DevSecOps & FinOps Pipeline**: automated PR gates with `TFLint`, `Trivy`, `Open Policy Agent (OPA)`, and `Infracost`
   * **Hands-on Multi-AZ Cloud Architecture Lab**: building and validating a modular AWS topology (AWS Sandbox or LocalStack + `tflocal`)

---

## Repository Structure

```text
.
├── .github/workflows/
│   └── export-pdf.yml  # GitHub Actions workflow exporting slides to PDF & GitHub Releases
├── index.html          # Reveal.js 6.0.2 entrypoint (jsDelivr CDN, zero-build setup)
├── css/
│   └── custom.css      # Custom light theme, typography (Inter & JetBrains Mono), and responsive layout
├── img/                # Custom vector SVG architecture diagrams & author avatars
└── slides/             # Modular Markdown slide decks loaded dynamically by Reveal.js
```

---

## Running Locally

Because Reveal.js loads external Markdown files (`slides/*.md`) via `fetch`, serve the repository over a local HTTP server rather than opening `index.html` via `file://`:

### Option 1: Python (built-in)
```bash
python3 -m http.server 8000
```
Then open [http://localhost:8000](http://localhost:8000).

### Option 2: Node.js (`npx`)
```bash
npx serve .
```

### Option 3: Docker (`nginx`)
```bash
docker run --rm -p 8080:80 -v "$PWD":/usr/share/nginx/html:ro nginx:alpine
```
Then open [http://localhost:8080](http://localhost:8000).

---

## Exporting to PDF

### Automated GitHub Release (Recommended)
Every push to slide assets on `master` triggers the [Export Slides to PDF & Release](.github/workflows/export-pdf.yml) GitHub Actions workflow using Playwright Chromium (`?print-pdf&pdfSeparateFragments=false`), publishing **`terraform-in-a-nutshell.pdf`** both as a workflow artifact and as a versioned GitHub Release asset (`vYYYY.MM.DD-<run>`):
* **Direct Download:** [terraform-in-a-nutshell.pdf](https://github.com/devops-cloud-courses/terraform/releases/latest/download/terraform-in-a-nutshell.pdf)

### Manual Export via Chrome / Chromium

1. Open [https://devops-cloud-courses.github.io/terraform/?print-pdf&pdfSeparateFragments=false](https://devops-cloud-courses.github.io/terraform/?print-pdf&pdfSeparateFragments=false)
2. Press `Cmd + P` (macOS) or `Ctrl + P` (Windows/Linux)
3. Configure the print dialog settings:

| Setting | Value |
|---|---|
| **Destination** | Save as PDF |
| **Pages** | All |
| **Margins** | None / Default |
| **Options** | Enable **Background graphics** |

---

## Authors

* **Laurent Grangeau** — Cloud Solution Architect @ Google ([@laurentgrangeau](https://twitter.com/laurentgrangeau) | [github.com/laurentgrangeau](https://github.com/laurentgrangeau))
* **Arthur Mauvezin** — DevOps Engineer @ Ouidou Consulting ([@arthurmauvezin](https://twitter.com/arthurmauvezin) | [github.com/arthurmauvezin](https://github.com/arthurmauvezin))
