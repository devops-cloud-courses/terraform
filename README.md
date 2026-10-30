# Terraform — In a Nutshell

[![Live Presentation](https://img.shields.io/badge/Slides-Live%20Presentation-2563eb?style=for-the-badge)](https://devops-cloud-courses.github.io/terraform/)
[![Reveal.js](https://img.shields.io/badge/Reveal.js-6.0.2-4f46e5?style=for-the-badge)](https://revealjs.com/)
[![Terraform](https://img.shields.io/badge/Terraform-v1.17+-7c3aed?style=for-the-badge)](https://www.terraform.io/)

Interactive **Infrastructure as Code (IaC)** lecture and hands-on course material covering **Terraform**, modern HCL patterns, cloud architecture, and production GitOps/DevSecOps workflows.

🔗 **Live Presentation:** [https://devops-cloud-courses.github.io/terraform/](https://devops-cloud-courses.github.io/terraform/)

---

## 👥 Authors

- **Laurent Grangeau** — Cloud Solution Architect @ Google ([@laurentgrangeau](https://twitter.com/laurentgrangeau) | [github.com/laurentgrangeau](https://github.com/laurentgrangeau))
- **Arthur Mauvezin** — DevOps Engineer @ Ouidou Consulting ([@arthurmauvezin](https://twitter.com/arthurmauvezin) | [github.com/arthurmauvezin](https://github.com/arthurmauvezin))

---

## 📚 Course Curriculum & Slide Deck Structure

The presentation is organized modularly in [`slides/`](slides/) and loaded dynamically via [index.html](index.html):

| Section | File | Topics Covered |
|---|---|---|
| **1. Title & Speakers** | [`slides/title.md`](slides/title.md) | Course cover and speaker introductions |
| **2. Introduction** | [`slides/introduction.md`](slides/introduction.md) | Deck navigation, evolution of infrastructure (`ClickOps` → `Imperative` → `Config Mgmt` → `Declarative IaC`), and core engineering principles (*Idempotency*, *Immutable Infrastructure*, *Separation of Concerns*) |
| **3. Terraform Overview** | [`slides/terraform.md`](slides/terraform.md) | What Terraform is, core capabilities, `Write → Plan → Apply` workflow, and AWS Provider `v6.x` `main.tf` example |
| **4. Core Concepts** | [`slides/concepts.md`](slides/concepts.md) | `HCL`, Providers & `gRPC` architecture, OIDC/IAM authentication, typed `variables` & `validation`, `locals`, `data` sources (dynamic SSM AL2023 ARM64 AMI lookup), `outputs`, `for_each` vs. `count`, `modules`, `terraform.tfstate`, native S3 state locking (`use_lockfile = true`), `ephemeral` resources & write-only (`_wo`) arguments, `workspaces` vs. layered state architecture, `lifecycle` guardrails, config-driven `import`/`moved`, `check` assertions, and `provisioners` |
| **5. Terraform CLI** | [`slides/cli.md`](slides/cli.md) | `init`, `providers`, `test` (`.tftest.hcl`), `plan` (`-out`, `-refresh-only`, `-minimal-refresh`), `graph` (`DAG`), `apply`, `console`, and `destroy` |
| **6. Comparisons, Pros & Cons** | [`slides/proscons.md`](slides/proscons.md) | Side-by-side comparison of **Terraform**, **OpenTofu**, **AWS CloudFormation**, **AWS CDK**, and **Pulumi**, plus key strengths and trade-offs |
| **7. Hands-on Practice & Lab** | [`slides/practicals.md`](slides/practicals.md) | End-to-end CLI workflow (`init` → `fmt`/`validate`/`test` → `plan` → `show -json` → `apply` → `destroy`), production GitOps/DevSecOps/FinOps pipeline (`TFLint`, `Trivy`, `OPA`, `Infracost`), and the **Hands-on Multi-AZ Lab** |
| **8. Conclusion** | [`slides/conclusion.md`](slides/conclusion.md) | Key takeaways on declarative IaC and GitOps automation |

---

## 🎨 Technical Stack & Assets

- **Presentation Engine**: [Reveal.js `6.0.2`](https://revealjs.com/) (loaded via jsDelivr CDN in [`index.html`](index.html)) with custom `hcl` (`terraform`, `tf`) syntax highlighting registered in `RevealHighlight`.
- **Custom Theme**: [`css/custom.css`](css/custom.css) — Modern light theme with `Inter` & `JetBrains Mono` typography, card-based lists, high-contrast dark slate (`#0f172a`) code blocks, and responsive tables.
- **Custom Vector Diagrams ([`img/`](img/))**:
  - [`img/terraform-overview.svg`](img/terraform-overview.svg) — Declarative HCL to Multi-Cloud APIs overview
  - [`img/terraform-workflow.svg`](img/terraform-workflow.svg) — `Write → Plan → Apply` lifecycle
  - [`img/terraform-providers.svg`](img/terraform-providers.svg) — Terraform Core (`gRPC`) & Provider plugins architecture
  - [`img/terraform-state.svg`](img/terraform-state.svg) — Remote S3 state backend & native `.tflock` locking
  - [`img/terraform-layers.svg`](img/terraform-layers.svg) — Production layered state architecture & blast-radius isolation
  - [`img/terraform-graph.svg`](img/terraform-graph.svg) — Directed Acyclic Graph (`DAG`) dependency resolution
  - [`img/terraform-gitops.svg`](img/terraform-gitops.svg) — Production GitOps, DevSecOps (`Trivy`/`OPA`), and FinOps (`Infracost`) pipeline
  - [`img/terraform-lab.svg`](img/terraform-lab.svg) — Hands-on Multi-AZ AWS Lab topology
- **Speaker Avatars**:
  - [`img/avatar-laurent.jpg`](img/avatar-laurent.jpg)
  - [`img/avatar-arthur.jpg`](img/avatar-arthur.jpg)

---

## 🚀 Running Locally

Because Reveal.js fetches the external Markdown files in [`slides/`](slides/) over HTTP, serve the repository root with any static HTTP server:

```bash
# Using Python 3
python3 -m http.server 8000

# Or using Node.js (npx)
npx serve -l 8000
```

Then open [http://localhost:8000](http://localhost:8000) in your browser.

### Keyboard Shortcuts

- <kbd>←</kbd> / <kbd>→</kbd> : Navigate between main sections
- <kbd>↑</kbd> / <kbd>↓</kbd> or <kbd>Space</kbd> : Navigate vertically within a section
- <kbd>Esc</kbd> or <kbd>O</kbd> : Toggle slide overview mode
- <kbd>S</kbd> : Open speaker notes window
- <kbd>F</kbd> : Toggle fullscreen
- <kbd>?</kbd> : Show keyboard shortcuts help
