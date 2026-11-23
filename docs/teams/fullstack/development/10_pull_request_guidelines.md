We use GitHub for code collaboration and follow best practices to ensure our pull requests are easy to review, maintain high quality, and integrate smoothly into our workflow.

- **Keep PRs Small and Focused**  
  Submit pull requests that address a single purpose or issue. Smaller, focused PRs are easier and faster to review and reduce the risk of introducing bugs.

- **Informative Titles and Descriptions**  
  Use clear, descriptive titles. In the description, provide context on what the PR does, reference the related Jira ticket, and include any relevant details or links. This helps reviewers quickly understand the change and its purpose.

- **Include Jira Ticket Numbers**  
  Reference the relevant Jira ticket number in the PR title and description to link code changes to tracked work. When it is properly linked, the PR will automatically update the ticket status in Jira.

- **Self-Review on GitHub**  
  Open your PR and review the diff on GitHub before marking it ready. Reading changes in the GitHub UI often catches things you miss in your local editor. Mark the PR as draft while you're still reviewing or if CI is running.

- **English, Czech and Slovak Only**  
  All PR titles, descriptions, and comments must be written in these languages to ensure clarity for all collaborators.

- **Show Evidence**  
  Fill the `## Evidence` section of the PR template: screenshots for UI, a test or request/response sample for an endpoint, the failing test for a fix. What counts for each kind of change is on the [Code Review](20_code_review_process.md) page.

- **AI Review**  
  On projects with the review bot, the bot reviews every ready PR and decides with the `review-gate` check whether a human must approve it. See [Code Review](20_code_review_process.md).

- **Slack Integration**  
  PR activity on some projects is integrated with Slack to keep the team informed of new pull requests, reviews, and merges in real time. There are dedicated channels for each project to facilitate communication and collaboration.

- **Feature and Bugfix Branches**  
  Open pull requests from feature (`feature/…`) and bugfix (`bugfix/…`) branches with the base branch set to `develop`.
- **Hotfix Branches**  
  Open pull requests from hotfix (`hotfix/…`) branches with the base branch set to `main`.

- **Who to Assign**  
  On projects with the review bot, nobody: the bot requests the project reviewer when a PR needs a human. Add the `needs-human` label when you want one anyway. On other projects, assign at least one teammate familiar with the code; if unsure, ask your team lead.

- **When to Mark Ready**  
  Mark the PR ready (and, without the bot, assign reviewers) once CI is green. Use draft PRs for work in progress: the bot does not review drafts either.

- **Sync Regularly**  
  Regularly update your branch with the latest changes from the base branch (`develop` or `main`) to minimize merge conflicts and ensure your code is tested against the most recent version.

By following these guidelines, we ensure our pull requests are clear, actionable, and easy to review, leading to faster merges and higher code quality.
