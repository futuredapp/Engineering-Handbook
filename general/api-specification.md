# API specification

Our preferred way of specifying server APIs is using [OpenAPI (version 3.0)](https://swagger.io/specification/).

Ideal way is to kick this off is collaboratively.
It does not need to be a backend developer who starts the documentation.
Whoever need this most and/or has the most free time during the initial phase of the project is perfect person to do it.

To edit specifications we recommend using [Stoplight Studio](https://stoplight.io/studio/) to prevent any conflicts in the team.

## Templates

We have compiled these templates to help with kick-starting specifications:

- [OpenAPI project template](https://github.com/futuredapp/OpenAPI-Project-Template)
- [Apiary project template](https://github.com/futuredapp/apiary-project-template) – deprecated, used on older project or where required by our partner.

## New project setup

Start with the template.
The OpenAPI template contains has basic setup for linting, generating documentation and starting mock servers.

Keep in mind that to make it work properly you need to do few things:

- Required steps:
  1. Create mock dyno on Heroku.
  2. Add `HEROKU_API_KEY` to repository secrets.
  3. Run `yarn` to create up-to-date `yarn.lock` file.
  4. Replace `project-name` in `Makefile` with your new hyphenated project name.
  5. Enable GitHub Pages for `main` branch where possible. (Be aware, it is publicly accessible, which might be a security risk for some projects.)
- Optional (but recommended) steps:
  1. Replace the project name in title tag in the `index.html` file to make your documentation usable better in a browser.
  2. Update deployed server addresses in `api.yml`.
  3. Update you API name in `api.yml`.

Now you are ready to start documenting your API!
