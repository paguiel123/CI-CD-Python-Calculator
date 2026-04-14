# CI/CD Pipeline simple Python app

**Author:** Paguiel Javan Hossie

---

## Overview

This repository demonstrates a complete **CI/CD pipeline** for a simple Python application using GitHub Actions.

The objective is to validate modern DevSecOps practices through automated testing, security scanning, and container deployment.

---

## CI Workflow

The **CI pipeline** runs on every push to `main`/`master` and on manual trigger. It use the appropriate permissions on the `contents`, the `security-events` and the `actions`. Furthermore, it run on `ubuntu-latest` for jobs.

The first job is called `test` and have the following requirements:

*   It use a `matrix` strategy to test with Python version *3.8, 3.9, and 3.10*
*   The first step of the jon is named “checkout” and use `actions/checkout@v5`
*   The second step is named “Python ${{ matrix.python-version }}” and use `actions/setup-python@v6`
*   The third step is named “dependencies” and will install/upgrade pip, then install:
    *   `flake8` for code quality
    *   `pytest` for tests
*   The fourth step is called “flake8”
    *   For flake8, the first run:  
        *   Run on the current directory
        *   Count the number of errors
        *   Select the errors E9,F63,F7,F82 (check what these are)
        *   Show the source
        *   And display the statistics
    *   And for the second run:
        *   Run on the current directory
        *   Count the number of errors
        *   Return “0” even with errors
        *   And display the statistics
*   The fifth step will be called “pytest” and run tests on the `tests/` directory which is provided inside ressources

The second job is called `trivy-scan` and have the following requirements:

*   The first step is named “checkout” and use `actions/checkout@v5` to retrieve our code
*   The second step is named “trivy FS mode” and use `aquasecurity/trivy-action@0.33.1` for a file system scan, in a sarif format named “results.sarif” for critical and high severity
*   The third step is named “upload” and use `github/codeql-action/upload-sarif@v4` with the previously generated sarif file

---

## CD Workflow

The **CD pipeline** is triggered only after a successful CI run. It run automatically when your workflow “CI” is completed (check `workflow_run`)

The job will is named “build” and use ubuntu-latest to have the following requirements:

*   Run only if the CI pipeline is successful
*   The first step is named “checkout” and use `actions/checkout@v5`
*   The second step is named “login” and use `docker/login-action@v3` with the following secrets

| Name | Description |
| --- | --- |
| `DOCKER_USERNAME` | My Docker Hub username |
| `DOCKER_PASSWORD` | My Docker Hub password or token |

In our GitHub project settings, "Actions secrets and variables", "Repository secrets" - Create a Personal access token in read and write on Docker Hub and link your credz to GitHub

*   The third step is named “build and push” and use `docker/build-push-action@v6` with an id named “push”, the current directory context, the Dockerfile provided, the “push” defined to true and a tags (Be careful with the tags => put your username/xxx:latest)

## Step 3 - Test

1.  Push your code to GitHub
2.  Go to the Actions tab:
    *   Check that the CI pipeline runs and passes
    *   When it’s successful, verify that the CD pipeline runs
3.  Go to your Docker Hub account → Check the pushed image

- Docker Hub authentication via repository secrets
- Docker image build using the project’s Dockerfile
- Push to Docker Hub under:  
  `docker.io/<DOCKER_USERNAME>/calculator:latest`


---

## References

GitHub Actions • Pytest • Flake8 • Trivy • Docker Build‑Push Action


