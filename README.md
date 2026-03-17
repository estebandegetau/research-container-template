# Research Container Template

A reproducible research environment using Docker, R, Python, and modern data science tools.

## What This Template Provides

This template creates a fully reproducible research environment with:

| Component | Version | Purpose |
|-----------|---------|---------|
| **R** | 4.5 | Statistical computing and graphics |
| **Python** | 3.12 | General-purpose programming |
| **Quarto** | Latest | Scientific publishing |
| **TinyTeX** | Latest | PDF document generation |
| **Claude Code** | Latest | AI-assisted development |

### Pre-installed R Packages

- **tidyverse** - Data manipulation and visualization
- **targets** - Pipeline toolkit for reproducible workflows
- **tarchetypes** - Targets archetypes (Quarto integration, etc.)
- **here** - Project-relative file paths
- **renv** - Dependency management
- **quarto** - Quarto integration for R

### Pre-installed Python Packages

- **numpy** - Numerical computing
- **pandas** - Data manipulation
- **jupyter** - Interactive notebooks

## Getting Started

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [VS Code](https://code.visualstudio.com/) with the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

### Quick Start

1. **Use this template** to create a new repository, or clone it directly:
   ```bash
   git clone https://github.com/YOUR_USERNAME/research-container-template.git my-project
   cd my-project
   ```

2. **Open in VS Code**:
   ```bash
   code .
   ```

3. **Reopen in Container**: When prompted, click "Reopen in Container" or run the command `Dev Containers: Reopen in Container` from the command palette (Ctrl+Shift+P / Cmd+Shift+P).

4. **Wait for the container to build**. The first build may take several minutes as it installs all dependencies.

5. **Start working!** The R environment is ready with all packages installed.

## Project Structure

```
├── .claude/
│   └── skills/              # Claude Code skill definitions (6 skills)
├── .devcontainer/           # VS Code Dev Container configuration
│   └── devcontainer.json
├── .github/
│   └── workflows/           # GitHub Actions workflows
│       └── build-test.yml
├── R/                       # R function scripts (sourced by targets)
│   └── functions.R
├── data/                    # Raw data files (gitignored by default)
├── docs/                    # Quarto documents and research docs
│   ├── report.qmd           # Starter Quarto report
│   ├── onboarding.qmd       # Human-facing guide to the companion system
│   ├── strategy.md          # Research methodology (Tier 2, human-owned)
│   └── deltas.md            # Implementation discovery log
├── output/                  # Generated outputs (gitignored)
├── prompts/
│   └── iterations/          # Iteration logs per instrument (YAML)
├── reports/                 # Dated progress reports (.qmd)
├── _targets/                # targets cache (gitignored)
├── _targets.R               # Pipeline definition
├── CLAUDE.md                # Project-specific Claude Code instructions (Tier 1)
├── COMPANION.md             # Permanent principles and architecture (Tier 2)
├── Dockerfile               # Container definition
├── renv/                    # renv configuration (generated at build)
├── .Rprofile                # R startup configuration
└── README.md                # This file
```

## Customization Checklist

After creating your project from this template, update the following:

- [ ] **README.md**: Replace this content with your project description
- [ ] **Dockerfile**: Update the maintainer label with your information
- [ ] **_targets.R**: Define your analysis pipeline
- [ ] **R/functions.R**: Add your R functions
- [ ] **docs/report.qmd**: Write your research report
- [ ] **renv**: Run `renv::init()` then `renv::snapshot()` to track dependencies
- [ ] **docs/onboarding.qmd**: Read the principles and architecture guide
- [ ] **CLAUDE.md**: Run `/setup-project` to generate project-specific instructions
- [ ] **docs/strategy.md**: Draft your research strategy with Claude's help, then review and correct it
- [ ] **docs/strategy.md**: Review and correct the draft — this is the most valuable intellectual work at project start

## Using the targets Pipeline

The [targets](https://docs.ropensci.org/targets/) package manages your analysis pipeline:

```r
# View the pipeline
targets::tar_visnetwork()

# Run the pipeline
targets::tar_make()

# Load a target result
data <- targets::tar_read(target_name)

# Check pipeline status
targets::tar_outdated()
```

### Adding New Targets

1. Define functions in `R/` directory
2. Add targets to `_targets.R`
3. Run `targets::tar_make()`

## Managing R Packages with renv

Core packages (tidyverse, targets, etc.) are pre-installed in the container. To track your project's dependencies with renv:

```r
# Initialize renv for your project (first time only)
renv::init()

# Install a new package
install.packages("packagename")

# Update the lockfile after installing packages
renv::snapshot()

# Restore packages from lockfile (e.g., after pulling changes)
renv::restore()

# Check package status
renv::status()
```

**Workflow:**
1. Core packages are pre-installed at container build time
2. Run `renv::init()` when you want to start tracking dependencies
3. After installing new packages, run `renv::snapshot()` to update `renv.lock`
4. Commit `renv.lock` to version control for reproducibility

## Rendering Quarto Documents

```bash
# Render to HTML
quarto render docs/report.qmd --to html

# Render to PDF
quarto render docs/report.qmd --to pdf

# Render all documents
quarto render docs/
```

Or in R:
```r
quarto::quarto_render("docs/report.qmd")
```

## Using Claude Code

Claude Code is pre-installed in the container. To use it:

1. Set your API key as an environment variable (add to `.env` file, which is gitignored):
   ```
   ANTHROPIC_API_KEY=your-api-key-here
   ```

2. The devcontainer is configured to pass this key to the container.

3. Run Claude Code from the terminal:
   ```bash
   claude
   ```

## Claude Code Research Companion

This template includes a Claude Code companion system with principles, architecture, and workflow conventions for rigorous LLM-assisted research. It is ready to populate for your specific project.

### What this is

**`COMPANION.md`** is the permanent soul document. It establishes four principles that govern every interaction with Claude in this project: human confers meaning, delegate instrumental and own the core, credibility tracks involvement, and decisions belong to humans. These principles are not guidelines — they are the load-bearing structure of an honest research process. They exist because LLMs can produce plausible-sounding output at every step while the human quietly disengages from the work that actually matters. COMPANION.md makes that disengagement visible and costly.

The companion system operationalizes these principles through a three-tier documentation architecture, a set of workflow skills, and an auditable iteration log. Every pipeline run is recorded with its metrics, the human's interpretation, and the human's decision. Every implementation discovery that contradicts the research plan is logged for reconciliation. The result is a project history that is defensible at peer review — not because Claude generated it, but because the human was forced to engage with it at every critical point.

### The three-tier documentation system

| Tier | Files | What Claude does |
|------|-------|-----------------|
| **Tier 1 (auto-update)** | `CLAUDE.md` and subdirectory variants | Updates directly when implementation changes make them stale |
| **Tier 2 (never auto-edit)** | `docs/strategy.md` and designated documents | Logs discoveries to `docs/deltas.md`; human reconciles via `/strategy-sync` |
| **Tier 3 (ignore)** | Stable references, generated artifacts | Leaves alone; logs a delta if a factual error is found |

The key constraint: Claude never edits Tier 2 documents. Research design decisions — what to measure, how to validate it, what counts as success — are human-owned. Implementation discoveries flow upward through the delta log, where the human reconciles them under adversarial questioning from Claude. This is not bureaucracy; it is the mechanism that keeps the strategy document honest.

### The skill system

Six slash commands enforce a repeatable workflow:

| Skill | When to use | What it enforces |
|-------|-------------|-----------------|
| `/setup-project` | Once, from template | Onboarding interview → draft `CLAUDE.md` |
| `/doc-sync` | After significant work | Tier 1 updates + Tier 2 delta logging |
| `/strategy-sync` | Stage gates or 3+ deltas | Adversarial reconciliation of deltas with strategy |
| `/log-iteration` | After each pipeline stage | Auto-gathered metadata + human interpretation/decision |
| `/progress-report` | External communication | Auto-gathered state + terminology translation for readers |
| `/quarto-style` | Writing `.qmd` files | Formatting authority for all Quarto documents |

### The iteration log as lab notebook

If you have worked in Stata or R, you know the problem: you changed a parameter three weeks ago, results improved, and you cannot reconstruct what you changed or why. The iteration log (`prompts/iterations/<instrument>.yml`) is that lab notebook. Every pipeline run records the instrument version, model, metrics, your interpretation, your decision, and a git hash linking to the exact file state. Reconstruct any past result with `git show <hash>:<instrument_file>`.

### Claude Infrastructure Checklist

Use this checklist when setting up a new project from this template:

- [ ] Set `ANTHROPIC_API_KEY` in your environment (`.env` or shell profile)
- [ ] Read `docs/onboarding.qmd` — the principles and architecture before anything else
- [ ] Run `/setup-project` to generate your project-specific `CLAUDE.md` (takes ~15-30 min of interview)
- [ ] Describe your project to Claude and ask for a draft `docs/strategy.md`
- [ ] Review and correct `docs/strategy.md` carefully — this is the most valuable intellectual work at project start
- [ ] Configure agents in `.claude/agents/` following the taxonomy in `COMPANION.md`
- [ ] Run `/doc-sync` after your first significant implementation session

### Companion directory structure

```
COMPANION.md                          <- Soul document (Tier 2, never auto-edited)
CLAUDE.md                             <- Project instructions (Tier 1, maintained by /doc-sync)
docs/
  onboarding.qmd                      <- Human-facing narrative guide
  strategy.md                         <- Authoritative methodology (Tier 2)
  deltas.md                           <- Implementation discovery log
.claude/
  skills/
    setup-project/SKILL.md            <- One-time onboarding interview
    doc-sync/SKILL.md                 <- Documentation sync (three-tier)
    strategy-sync/SKILL.md            <- Adversarial delta reconciliation
    log-iteration/SKILL.md            <- Iteration log entry
    progress-report/SKILL.md          <- External progress reports
    quarto-style/SKILL.md             <- Quarto formatting authority
```

## GitHub Actions

This template includes a GitHub Actions workflow that:

- Builds the Docker container on every push to `main` or when the Dockerfile changes
- Verifies the container builds successfully
- Tests that R and key packages load correctly

See `.github/workflows/build-test.yml` for details.

## Best Practices for Reproducibility

1. **Never edit data files manually** - All data transformations should be in code
2. **Use `here::here()` for file paths** - Ensures paths work across environments
3. **Commit `renv.lock`** - Captures exact package versions
4. **Document your pipeline** - Use comments in `_targets.R` and function documentation
5. **Use targets for long-running computations** - Avoids re-running unchanged steps
6. **Keep raw data immutable** - Store in `data/`, never modify

## Troubleshooting

### Container won't build

- Ensure Docker is running
- Try rebuilding without cache: `docker build --no-cache .`

### R packages won't install

- Core packages are pre-installed; just use `library()` to load them
- For new packages: `install.packages("pkg")` then `renv::snapshot()`
- Check system dependencies in Dockerfile if compilation fails
- Run `renv::status()` to diagnose renv issues

### Quarto PDF output fails

- Ensure TinyTeX is installed: `quarto install tinytex`
- Check for missing LaTeX packages in error output

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Citation

If you use this template, please cite:

```
[Add citation information for your project]
```
