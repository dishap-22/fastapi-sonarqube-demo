# fastapi-sonarqube-demo

This repository contains a minimal **FastAPI** application instrumented for **SonarQube** static code analysis.

It includes:
- A small FastAPI app (`app/`)
- Unit tests (`tests/`)
- SonarQube configuration (`sonar-project.properties`)
- CI workflow that runs tests and triggers a SonarQube scan (`.github/workflows/sonarqube.yml`)

---

## Prerequisites

### 1) SonarQube Server
- A running SonarQube instance
- A SonarQube **project** created with the key defined in `sonar-project.properties`

### 2) SonarQube Token
Create an access token in SonarQube with permission to analyze the project.

### 3) Coverage generation (optional but enabled)
The SonarQube configuration expects a coverage report at `coverage.xml`:
- The GitHub Actions workflow runs: `pytest --cov=app --cov-report=xml`

---

## Project setup

### Install dependencies
```bash
pip install -r requirements.txt
```

### Run tests
```bash
pytest
```

---

## SonarQube configuration

Configuration lives in `sonar-project.properties`.

Key settings in this repo:
- `sonar.projectKey=dishap-22_fastapi-sonarqube-demo`
- `sonar.organization=dishap-22`
- `sonar.sources=app`
- `sonar.tests=tests`
- `sonar.python.coverage.reportPaths=coverage.xml`
- `sonar.python.version=3.12`

> If your SonarQube project key/organization differs, update these values.

---

## GitHub Actions integration (recommended)

The workflow `.github/workflows/sonarqube.yml`:
1. Runs on pushes to `main` and `development`, and on PRs targeting `main`
2. Installs dependencies
3. Runs tests and generates `coverage.xml`
4. Calls SonarQube scan action using secrets

### Required repository secrets
Add the following secrets in GitHub:
- `SONAR_TOKEN` — SonarQube authentication token
- `SONAR_HOST_URL` — e.g. `https://your-sonarqube-host` (no trailing slash recommended)

---

## Local SonarQube scan (manual)

You can also run the analysis locally using SonarScanner (not included in this repo).

1. Ensure you generate coverage first:
```bash
pytest --cov=app --cov-report=xml
```

2. Run SonarScanner from the repository root (example):
```bash
sonar-scanner \
  -Dsonar.projectBaseDir=. \
  -Dsonar.host.url="$SONAR_HOST_URL" \
  -Dsonar.login="$SONAR_TOKEN"
```

The repo’s `sonar-project.properties` will be picked up automatically.

---

## Expected behavior

After a successful scan:
- SonarQube will analyze Python sources under `app/`
- Unit test files under `tests/`
- Coverage will be imported from `coverage.xml`

---

## Notes

- `sonar.tests=tests` is set, but the repo’s CI only generates coverage; it does not generate separate test execution reports.
- If you change folder structure, update `sonar.sources` / `sonar.tests` accordingly.

