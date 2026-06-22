# GitHub Actions CI/CD Pipeline Practice

[![Node.js CI](https://github.com/Tommy-CS/GHA-node-ci-cd-practice/actions/workflows/node.js.yml/badge.svg)](https://github.com/Tommy-CS/GHA-node-ci-cd-practice/actions/workflows/node.js.yml)

## Project Overview

This project is a hands-on CI/CD lab using a Node.js Express application. The goal was to practice building a real software delivery workflow with GitHub Actions, branch protection, Docker, GitHub Container Registry, and Render deployment.

The pipeline validates pull requests before merging, builds a Docker image, publishes the image to GitHub Container Registry after changes are merged into `main`, and triggers a Render deployment through a deploy hook.

## Live Deployment

Deployed application:

```text
https://gha-node-ci-cd-practice.onrender.com
```

Container image:

```text
ghcr.io/tommy-cs/gha-node-ci-cd-practice:latest
```

## Tech Stack

- Node.js
- Express.js
- Jest
- Supertest
- Docker
- GitHub Actions
- GitHub Container Registry
- Render

## Application Routes

| Route | Method | Description |
|---|---|---|
| `/ping` | GET | Returns a simple health response |
| `/tasks` | GET | Returns the current task list |
| `/tasks` | POST | Adds a new task with a title and description |

Example response from `/ping`:

```text
Pong
```

## CI/CD Workflow Summary

The final workflow follows this process:

```text
- Developer opens a pull request
- GitHub Actions runs Node.js tests across multiple versions
- Docker image build is validated
- Required checks must pass before merging
- Pull request merges into main
- Docker image is published to GitHub Container Registry
- Render deploy hook is triggered
- Render redeploys the latest container image
```

## GitHub Actions Pipeline

The GitHub Actions workflow runs on pushes and pull requests targeting `main`.

```yaml
on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]
```

### Pull Request Validation

When a pull request is opened, GitHub Actions runs the required validation checks:

- Installs dependencies with `npm ci`
- Runs the test suite with `npm test`
- Tests across Node.js 18.x, 20.x, and 22.x
- Builds the Docker image to confirm the app can be containerized successfully

The required pull request checks are:

```text
Node.js CI / build (18.x)
Node.js CI / build (20.x)
Node.js CI / build (22.x)
Node.js CI / docker-build
```

The `docker-publish` and `render-deploy` jobs are skipped on pull requests because publishing and deployment should only happen after code is merged into `main`.

### Post-Merge Deployment

After a pull request is merged into `main`, the workflow runs again on the `push` event.

At that point, the pipeline:

1. Runs the Node.js test matrix
2. Builds the Docker image
3. Publishes the Docker image to GitHub Container Registry
4. Triggers a Render deployment using a deploy hook

## Branch Protection

The `main` branch is protected with a GitHub ruleset.

The ruleset requires:

- Pull requests before merging
- Required status checks to pass
- Branches to be up to date before merging
- Force pushes to be blocked

This prevents broken code from being merged directly into `main`.

## Docker

The project includes a `Dockerfile` that packages the Node.js application into a container image.

Build the Docker image locally:

```bash
docker build -t gha-node-ci-cd-practice .
```

Run the container locally:

```bash
docker run --rm -p 3000:3000 gha-node-ci-cd-practice
```

Then visit:

```text
http://localhost:3000/ping
```

## GitHub Container Registry

After changes are merged into `main`, GitHub Actions publishes the Docker image to GitHub Container Registry.

Published image:

```text
ghcr.io/tommy-cs/gha-node-ci-cd-practice:latest
```

The image is also tagged with a short commit SHA for traceability.

## Render Deployment

Render is used to host the containerized application as a web service.

The deployment uses the published GHCR Docker image. After GitHub Actions publishes a new image, the workflow triggers a Render deploy hook so the live application can redeploy with the latest container image.

Render service:

```text
https://gha-node-ci-cd-practice.onrender.com
```

## Key Files

| File | Purpose |
|---|---|
| `.github/workflows/node.js.yml` | GitHub Actions CI/CD workflow |
| `Dockerfile` | Defines how the Node.js app is containerized |
| `.dockerignore` | Excludes unnecessary files from the Docker build context |
| `package.json` | Defines dependencies and npm scripts |
| `src/app.js` | Express app routes and middleware |
| `src/index.js` | Starts the Express server |
| `tests/index.spec.js` | Jest and Supertest test file |

## Workflow Jobs

### `build`

Runs the Node.js test matrix across multiple Node versions.

```text
Node.js 18.x
Node.js 20.x
Node.js 22.x
```

### `docker-build`

Builds the Docker image to confirm the application can be packaged successfully.

This job runs after the `build` job passes.

### `docker-publish`

Publishes the Docker image to GitHub Container Registry.

This job only runs after changes are pushed to `main`.

### `render-deploy`

Triggers a Render deploy hook after the Docker image is published.

This job only runs after changes are pushed to `main`.

## What This Project Demonstrates

This project demonstrates:

- Creating a GitHub Actions CI workflow
- Running automated tests on pull requests
- Testing across multiple Node.js versions
- Protecting the `main` branch with required checks
- Blocking merges when required checks fail
- Building a Docker image locally and in CI
- Publishing a Docker image to GitHub Container Registry
- Deploying a containerized application to Render
- Triggering deployments automatically with a Render deploy hook

## Final Pipeline

```text
Pull Request
    |
    v
GitHub Actions CI
    |
    v
Node.js Tests Across 18.x, 20.x, 22.x
    |
    v
Docker Build Validation
    |
    v
Required Checks Pass
    |
    v
Merge Into main
    |
    v
Publish Docker Image to GHCR
    |
    v
Trigger Render Deploy Hook
    |
    v
Render Redeploys Application
```
