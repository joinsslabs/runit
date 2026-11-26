# RunIt app: shipped, measured and recovered (RunIt Weekend)

A small Python web app, containerised with Docker, deployed by a GitHub Actions pipeline, and run with the basics of reliability engineering.

## Live app
https://YOUR-APP-URL

## Architecture
GitHub push → GitHub Actions (build image, push to GitHub Container Registry, deploy) → Azure Container Apps (or Render) → users.

## SLO
**99.5% of requests to the home page succeed (HTTP 200), measured over 30 days.** That error budget is about 3.6 hours a month. `check.sh` is a quick spot check, not the SLO measurement itself: 20 requests are too few to measure 99.5%, so it raises the alarm when fewer than 19 of 20 succeed.

## Health check
`GET /health` returns `{"status": "ok"}` while the process is running. The Dockerfile's HEALTHCHECK uses it, so `docker ps` shows the container as healthy or unhealthy. It only proves the process answers: the incident drill shows it can stay green while every user gets an error.

## How to deploy
Push to `main`. The workflow in `.github/workflows/deploy.yml` builds, pushes and deploys. To roll back: `git revert <bad commit>` and push.

## Incident drill
I shipped a bad release on purpose, detected it with `check.sh` and the logs, rolled back, and wrote [postmortem.md](postmortem.md). Time to restore: __ minutes.
