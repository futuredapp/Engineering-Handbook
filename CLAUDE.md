# Engineering Handbook

Futured Engineering Handbook — internal documentation site built with [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

## Project structure

```
mkdocs.yml          # Site config and navigation
docs/
  index.md          # Homepage
  editors.md        # Contributing guide
  teams/
    android/        # Android & KMP team docs
    delivery/       # Delivery team docs
    design/         # Design team (placeholder)
    fullstack/      # Full-stack team docs
    ios/            # iOS team docs
    qa/             # QA team (placeholder)
```

## Key conventions

- **Navigation** is defined in `mkdocs.yml` under `nav:`. Keep entries alphabetically ordered within each team section.
- **Team index pages** (`index.md`) list links to all section pages in alphabetical order.
- **iOS file naming**: `ios_<topic>.md` (e.g. `ios_builds.md`, `ios_release.md`). Resources go in `docs/teams/ios/Resources/`.
- **Android file naming**: uses numbered prefix directories (e.g. `architecture/00_native.md`, `project_setup/10_new_repo.md`).
- **Full-stack file naming**: numbered prefixes within subdirectories (e.g. `development/00_git_flow.md`).

## Markdown features (Material for MkDocs)

- Admonitions: `!!! note "Title"` (open), `??? info "Title"` (collapsible)
- Tabs: `=== "Tab name"`
- Code blocks with language tags and `content.code.copy` enabled
- Images with width attributes: `![alt](Resources/file.png){ width="900" }`
- Light/dark mode images: `#only-light` / `#only-dark` suffixes
- Lightbox galleries: `{data-gallery="light"}` / `{data-gallery="dark"}`

## Local development

```bash
source myenv/bin/activate
mkdocs serve
# Site at http://127.0.0.1:8000/Engineering-Handbook/
```

## Workflow

- Branch from `main`, open a PR, get approval, merge.
- GitHub Pages deploys automatically on merge via `.github/workflows/pages.yml`.
