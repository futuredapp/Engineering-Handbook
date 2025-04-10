---
hide:
  - navigation
---

# For Editors

This guide helps you contribute to the Engineering Handbook using Material for MkDocs.

## Installation

1. Install the required packages:
   ```bash
   brew install mkdocs-material
   ```

2. Clone the repository:
   ```bash
   git clone https://github.com/futuredapp/Engineering-Handbook.git
   cd Engineering-Handbook
   ```

## Local Development

Run the following command from the repository root to start a local development server with live-reloading:

```bash
mkdocs serve
```

The documentation will be available at [http://127.0.0.1:8000/Engineering-Handbook/](http://127.0.0.1:8000/Engineering-Handbook/).

## Editing Guidelines

### Making Changes

1. Create a new branch from `main`
2. Make your changes
3. Open a pull request to the [Engineering Handbook repository](https://github.com/futuredapp/Engineering-Handbook)
4. Get at least one approval before merging
5. After merging, the GitHub Action [Pages workflow](https://github.com/futuredapp/Engineering-Handbook/blob/main/.github/workflows/pages.yml) will automatically publish the updated documentation

### Diagrams

Some diagrams are created using [Excalidraw](https://excalidraw.com/). When exporting images, ensure following:

- Embed scene into exported image, or commit source `.excalidraw` file to the repository so it can be edited later by someone else.
- Respect light and dark themes. Use the `#only-light` and `#only-dark` suffixes to specify which theme the image is for. See [Light and dark mode](https://squidfunk.github.io/mkdocs-material/reference/images/#light-and-dark-mode) for more details.

### Markdown Syntax

Material for MkDocs uses standard Markdown with some additional features

- [**Admonitions**](https://squidfunk.github.io/mkdocs-material/reference/admonitions/):
  ```markdown
  !!! note "Title"
      Content of the note
  ```

### Editing Navigation

Edit the `mkdocs.yml` file to adjust the navigation structure.

### Adding New Pages

1. Create a new Markdown file in the appropriate directory
2. Add the page to navigation in `mkdocs.yml` if needed

## Project Structure

```
mkdocs.yml              # Configuration file
docs/
    index.md            # Documentation homepage
    editors.md          # Editors manual
    Teams/              # Teams space
        Team name/
            ...         # Pages
            Resources/  # Images, Diagrams, etc.
```


## Troubleshooting

For more detailed information, refer to the [Material for MkDocs documentation](https://squidfunk.github.io/mkdocs-material/getting-started/).
