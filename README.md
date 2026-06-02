# Laravel Cloud Deploy

This GitHub Action triggers a deployment on [Laravel Cloud](https://laravel.cloud) and waits for it to finish.

It fires the deploy hook, then polls the Laravel Cloud API until the deployment for this commit reaches a terminal status. The job fails if the deployment fails or the timeout is reached, so a green job actually proves the release shipped — not just that the hook was accepted.

Check out the [documentation](https://cloud.laravel.com/docs/deployments#deploy-hooks) to retrieve your deploy hook URL.

## Usage

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest
    steps:
      - name: Deploy
        uses: deriegle/laravel-cloud-deploy@v1
        with:
          webhook: ${{ secrets.LARAVEL_CLOUD_WEBHOOK }}
          api-token: ${{ secrets.LARAVEL_CLOUD_API_TOKEN }}
          environment-id: ${{ secrets.LARAVEL_CLOUD_ENVIRONMENT_ID }}
```

Retrieve the **API token** from your Laravel Cloud account settings, and the **environment ID** from the environment you are deploying to.

## Inputs

| Input            | Required | Default      | Description                                                      |
| ---------------- | -------- | ------------ | ---------------------------------------------------------------- |
| `webhook`        | yes      | —            | The deploy hook URL to trigger the deployment.                   |
| `api-token`      | yes      | —            | Laravel Cloud API token, used to poll deployment status.        |
| `environment-id` | yes      | —            | Laravel Cloud environment ID to poll.                           |
| `commit`         | no       | `github.sha` | Commit SHA to match the deployment against.                     |
| `timeout`        | no       | `180`        | Maximum number of seconds to wait for the deployment to finish. |
| `interval`       | no       | `10`         | Number of seconds between deployment status polls.              |

The action treats `deployment.succeeded` as success and `build.failed`, `deployment.failed`, `failed` and `cancelled` as terminal failures.

> **Note:** `commit` defaults to `${{ github.sha }}`, which is the pushed commit on `push` events. On `pull_request` events `github.sha` is the merge commit, so pass `commit: ${{ github.event.pull_request.head.sha }}` to match what Laravel Cloud deploys.
