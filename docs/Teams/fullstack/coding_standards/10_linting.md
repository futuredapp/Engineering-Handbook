### Our Approach

We enforce consistent code quality and style across all our projects by using a shared set of linting rules, maintained in our public repository:  
[https://github.com/futuredapp/eslint-config-futured](https://github.com/futuredapp/eslint-config-futured)

Each project imports the appropriate linting configuration from this repository via npm, ensuring that all developers follow the same standards—whether working on frontend (Vue.js) or backend (NestJS) code.

In the future, we will also include Prettier rules in this shared configuration for automated code formatting.

---

### Why Linting Matters

Linting is the process of automatically analyzing code to detect errors, enforce coding standards, and identify problematic patterns. It is a key practice in modern software development for several reasons:

- **Error Detection and Prevention**  
  Linters catch common mistakes early—such as syntax errors, unused variables, or incorrect imports—before code is even run or reviewed. This reduces debugging time and prevents bugs from reaching production.
- **Consistency Across the Codebase**  
  By enforcing a unified style, linting ensures that code written by different team members looks and behaves similarly. This makes the codebase easier to read, maintain, and collaborate on, especially in large or distributed teams.
- **Improved Code Quality and Maintainability**  
  Linting encourages best practices and discourages anti-patterns, leading to cleaner, more robust, and more secure code.
- **Efficient Code Reviews**  
  Automated linting frees up code reviewers to focus on logic, architecture, and design, rather than style or formatting nits.
- **Knowledge Sharing and Onboarding**  
  Linting rules serve as a living documentation of coding standards, helping new team members quickly adopt best practices.
- **Integration with CI/CD**  
  Linting is integrated into our continuous integration pipeline, blocking code that does not meet our standards from being merged.

---

### Tools and Configuration

- **ESLint**  
  Our primary linting tool for both JavaScript and TypeScript, with custom rule sets for Vue.js (frontend) and NestJS (backend).
- **Prettier**  
  Soon to be included in our shared configuration for automated code formatting, ensuring consistent style across all projects.
- **Editor Integration**  
  We encourage developers to set up linting and formatting to run automatically in their code editors for real-time feedback and corrections. We have a guide for automating this setup in out internal documentation.

---

By standardizing linting and formatting across the organization, we maintain a high-quality, professional codebase that is easy to work with and reflects our commitment to excellence.
