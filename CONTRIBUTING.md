# Contributing

This toolkit is deliberately small. A new template earns its place by being a reusable framework for a part of prompting people actually get stuck on, not another list of one-off prompts.

## Report a framework that broke down

The most useful contribution is a case where one of these templates gave the wrong answer. Open an issue with the **Framework broke down** template and include:

1. Which template, and which step or check.
2. What you were trying to do, with the model and setup you used.
3. What the template told you to do, and what actually happened.

Every report is reviewed. If it holds up, the template is corrected and the change is noted in the pull request that fixes it.

## Suggest a new template

Open an issue with the **Suggest a template** template before writing one. A good suggestion:

- Solves a decision or design problem that comes up again and again, not a single task
- Is generic and reusable, not tied to one product or company
- Rests on evidence (a study, documentation, or repeatable testing), not on a single impressive output

## Pull requests

Typo fixes and small wording corrections can go straight to a pull request. For anything larger, open an issue first so the change can be agreed before you spend time on it.

Match the structure of the existing files in `templates/`: a one-line summary, a "Why this exists" section, the framework itself, and a quick-reference table where it helps. This repository is plain Markdown, so please don't add scripts, dependencies or build steps.

By contributing, you agree that your contribution is released under the [MIT License](LICENSE), the same as the rest of the repository. Please also follow the [Code of Conduct](CODE_OF_CONDUCT.md).
