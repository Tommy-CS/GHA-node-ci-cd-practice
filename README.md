## CI/CD Pipeline

This project uses GitHub Actions to validate changes before they are merged into `main`.

The workflow automatically:
- Installs project dependencies
- Runs the Node.js test suite across multiple Node versions
- Builds a Docker image to confirm the application can be containerized successfully

The `main` branch is protected by a GitHub ruleset, so pull requests must pass all required checks before merging.