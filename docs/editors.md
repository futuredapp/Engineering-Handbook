---
hide:
  - navigation
---

# For Editors

This guide helps you contribute to the Engineering Handbook using Material for MkDocs.

## Installation

1. Clone the repository:
    ```bash
    git clone https://github.com/futuredapp/Engineering-Handbook.git
    cd Engineering-Handbook
    ```

2. Create a Virtual Environment:
    ```bash
    python3 -m venv myenv
    ```

3. Activate the Virtual Environment:
    ```bash
    source myenv/bin/activate
    ```

4. Install the required packages:
    ```bash
    pip install mkdocs-material
    pip install mkdocs-glightbox
    ```
   
    !!! note
        The `mkdocs-material` and plugin dependencies must be installed in the same Python environment.  
        Installing `mkdocs-material` from Homebrew and other plugins (`mkdocs-glightbox`) using `pip` might not work for you if you use `pyenv` or similar environment management tools.

5. When you're done, you can deactivate the environment with:
    ```bash
    deactivate
    ```

## Local Development

1. Activate the Virtual Environment:
    ```bash
    source myenv/bin/activate
    ```

2. Run the following command from the repository root to start a local development server with live-reloading:
    ```bash
    mkdocs serve
    ```

    The documentation will be available at [http://127.0.0.1:8000/Engineering-Handbook/](http://127.0.0.1:8000/Engineering-Handbook/).

3. When you're done, you can deactivate the environment with:
    ```bash
    deactivate
    ```

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
- Respect light and dark themes - export 2 images for light and dark backgrounds. Use the `#only-light` and `#only-dark` suffixes to specify which theme the image is for. See [Light and dark mode](https://squidfunk.github.io/mkdocs-material/reference/images/#light-and-dark-mode) for more details.
- Use the [`data-gallery`](https://blueswen.github.io/mkdocs-glightbox/gallery/gallery/) attribute to group images into a light gallery. For example, `data-gallery="light"` for light mode images and `data-gallery="dark"` for dark mode images. This ensures that lightbox will not display dark mode images in light mode and vice versa.  
  Related issue: [blueswen/mkdocs-glightbox/issues/26](https://github.com/blueswen/mkdocs-glightbox/issues/26)

Full example of diagram with transparent background in light and dark variant and lightbox support:

```markdown
![](../Resources/architecture/arch_kmp_light.png#only-light){data-gallery="light"}
![](../Resources/architecture/arch_kmp_dark.png#only-dark){data-gallery="dark"}
```

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
