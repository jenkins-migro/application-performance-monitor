# Jenkins to GitHub Actions Migration Report

## Summary

Migrated five Jenkins pipelines to GitHub Actions workflows and archived the original Jenkinsfiles under `.github/ci-archive/`.

## Source Pipelines

| Original Jenkinsfile | Pipeline type | Migrated workflow |
| --- | --- | --- |
| `Jenkinsfile` | Scripted Jenkins pipeline with Gradle, SonarQube, artifact archiving, and Docker publish stages | `.github/workflows/gradle-sonarqube-docker.yml` |
| `laravelgithubapi/Jenkinsfile` | Declarative Laravel/PHP pipeline with MySQL credentials and GitHub API status updates | `.github/workflows/laravel-github-api.yml` |
| `matrixbuilds/Jenkinsfile` | Declarative matrix build across platforms and Java versions | `.github/workflows/matrix-builds.yml` |
| `scriptedinspection/Jenkinsfile` | Scripted runner inspection pipeline | `.github/workflows/jenkins-inspection.yml` |
| `sn/Jenkinsfile` | Declarative file decoding and artifact archival pipeline | `.github/workflows/input-file-archive.yml` |

## Archive Locations

- `.github/ci-archive/root/Jenkinsfile`
- `.github/ci-archive/laravelgithubapi/Jenkinsfile`
- `.github/ci-archive/matrixbuilds/Jenkinsfile`
- `.github/ci-archive/scriptedinspection/Jenkinsfile`
- `.github/ci-archive/sn/Jenkinsfile`

## Conversion Notes

- Jenkins `checkout scm` steps were converted to `actions/checkout`.
- Jenkins shell and batch steps were converted to GitHub Actions `run` steps. Matrix builds use native GitHub Actions matrix-style fan-out via explicit included combinations.
- Jenkins `publishTestResults` and `archiveArtifacts` steps were converted to artifact uploads for test reports and packaged JARs.
- Jenkins SonarQube environment and quality gate behavior were converted to secret-backed SonarQube analysis and quality gate polling.
- Jenkins Docker registry publishing was converted to Docker CLI login, build, tag, and push steps guarded by branch and credential availability.
- Jenkins MySQL credential usage was converted to GitHub Actions secrets with a MySQL service container.
- Jenkins GitHub status updates were replaced by native GitHub Actions checks and statuses. The Jenkins behavior that closed pull requests on failed builds was intentionally not migrated because GitHub Actions should report failures without automatically closing contributor work.
- Jenkins `sn` file input placeholder was converted to a manual `workflow_dispatch` input to avoid committing embedded file content.

## Required Secrets and Variables

| Name | Required for | Notes |
| --- | --- | --- |
| `SONAR_TOKEN` | `gradle-sonarqube-docker.yml` | Enables SonarQube analysis and quality gate polling. |
| `SONAR_HOST_URL` | `gradle-sonarqube-docker.yml` | SonarQube server URL. |
| `REGISTRY_USERNAME` | `gradle-sonarqube-docker.yml` | Docker registry username. |
| `REGISTRY_PASSWORD` | `gradle-sonarqube-docker.yml` | Docker registry password or token. |
| `MYSQL_USER` | `laravel-github-api.yml` | Optional; defaults to `root` for the workflow service container. |
| `MYSQL_PASSWORD` | `laravel-github-api.yml` | Optional; defaults to `root` for the workflow service container. |

## Marketplace Actions

All marketplace actions are from the verified `actions` publisher and are pinned to immutable commit SHAs:

- `actions/checkout` v4.2.2: `11bd71901bbe5b1630ceea73d27597364c9af683`
- `actions/setup-java` v4.7.1: `c5195efecf7bdfc987ee8bae7a71cb8b11521c00`
- `actions/upload-artifact` v4.6.2: `ea165f8d65b6e75b540449e92b4886f43607fa02`
- `actions/download-artifact` v4.3.0: `d3f86a106a0bac45b974a628896c90dbdf5c8093`

## Validation

- Workflow syntax was validated with `actionlint`.
- Changed files were scanned for secrets before commit.
