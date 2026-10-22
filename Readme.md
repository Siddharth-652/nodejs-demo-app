# nodejs-demo-app: CI/CD with GitHub Actions

A sample Node.js (Express) app with a CI/CD pipeline that tests, builds and pushes a Docker image to Docker Hub on every push to `main`.

**Docker Hub image:** `siddharthgupta652/nodejs-demo-app`

## Tech used
GitHub, GitHub Actions, Node.js (Express, Jest, Supertest), Docker, Docker Hub

## Pipeline (`.github/workflows/main.yml`)

| Job | What it does |
|-----|--------------|
| `test` | Checkout, setup Node 20, `npm ci`, `npm test` |
| `build-and-push` | Runs only if `test` passes (`needs: test`). Logs in to Docker Hub, builds the image, pushes `latest` and `<commit-sha>` tags |

**Trigger:** push to `main`.

## Setup
1. Create a Docker Hub access token (Read & Write).
2. Add repo secrets: `DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN`.
3. Push to `main`.

## Run locally
```bash
npm ci
npm test
npm start            # http://localhost:3000

docker build -t nodejs-demo-app .
docker run -p 3000:3000 nodejs-demo-app
```

## Verified behaviour
- **Success:** both jobs pass and the image appears on Docker Hub.
- **Failure:** I deliberately broke a test. `test` failed and `build-and-push` was skipped, so no broken image reached the registry. This run is visible in the Actions tab history.

## What I learned
- Secrets are stored in GitHub Secrets, never in code; I used an access token with an expiry instead of my password.
- I rotated a token that was exposed by mistake.
- Tagging with the commit SHA makes every image traceable and rollback-friendly.
- The Docker container runs as a non-root user.