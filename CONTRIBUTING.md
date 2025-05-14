# Contributing to PDPE (PHP Docker Portable Environment)

Thank you for your interest in contributing to this project. Contributions are encouraged and appreciate as long as they fit within the defined structure, style and intention of this environment.

## Contribution types

You may contribute in the following ways:

- New optional stacks: redis stack, add postgreSQL support, etc.
- Improved Docker configuration or DevContainer configuration.
- Example apps or templates.
- Improved documentation (README, examples, etc.).
- Bug fixing existing scripts (`create-project.sh`, for example).
- Automation or workflow improvements.

## Getting started

Fork the repository and work in your changes:

```bash
git clone https://github.com/jmeiracorbal/pdpe.git
```

```bash
cd pdpe
```

Create a new branch:

```bash
git checkout -b feature/your-feature-name
```

### Make your changes and test locally

Use the development stack where you want to validate your changes:

```bash
docker compose -f etc/docker/stacks/development/docker-compose.yml up -d
```

If you are working on a override, you can copy the existant for mysql or mongo to create your own override.

### Commit and push

```bash
git add .
```

You must to use the pull request template:

```bash
git commit -m "Add: [describe your change]"
```

```bash
git push origin feature/your-feature-title
```

### Open a pull request to the main branch.

**Coding and structure guidelines**

- Only modify the main stack logic if completely necessary as long as it's backward compatible.
- Add new override stacks in: etc/docker/stacks/development.
- When adding new environment variables, use .env.example in case your stack needs them.
- Keep a separate virtual host template in: templates/apache2/sites-available.

## Use DevContainer

If you are using VSCode you'll be able to open the project in a preconfigured DevContainer with an option that appears as a prompt on right side of the screen. This stack includes PHP, Xdebug, Composer and Docker CLI tools.

**Requirements**
- You should have Docker and Docker Compose installed.
- You should have a basic understanding of how Apache and Docker work together.
- Do not hardcode any paths or ports - please use environment variables from .env.

**Creating your pull request**
- You should submit all contributions through a pull request.
- The pull request should have a meaningful title and description.
- You should mention any issues you reference using GitHub keywords (Fixes #1, Closes #2, etc.).
- Prevent your PR from being too cumbersome by ensuring they are atomic addressing one topic.

**App examples**

If you are contributing a new stack or a use-case:
- Your example should go in docs/examples/.
- You should link it from the README when applicable.

By submitting your contribution, you grant your work under the MPL-2.0 license, which is the license of the base project.