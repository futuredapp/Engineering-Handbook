# For editors

Material for MKDocs [documentation](https://squidfunk.github.io/mkdocs-material/getting-started/)

## Installation guide

Call `brew install mkdocs-material` for tapping all necessary packages.

## Commands

Call `mkdocs serve` from repo root to start local live-reloading docs server. Local page will be available [here](http://127.0.0.1:8000/Engineering-Handbook/).

## Edit

Open a pull request in [Engineering Handbook](https://github.com/futuredapp/Engineering-Handbook) repo. You need at least one approve for merging it. After the merge GitHub action [Pages](https://github.com/futuredapp/Engineering-Handbook/blob/main/.github/workflows/pages.yml) will automatically publish the documentation.

## Project layout

    mkdocs.yml                  # The configuration file
    docs/
        index.md                # The documentation homepage
        editors.md              # Editors manual
        Teams/                  # Teams space
            Team name/
                ...             # Pages
                Resources/      # Images, Diagrams, ...
