# SSW shared GitHub Actions

Composite actions shared by SSW repositories. Each action lives in its own folder under `actions/`.

| Action | What it does |
| --- | --- |
| [`pr-deploy-gate`](actions/pr-deploy-gate/action.yml) | Gates a `/deploy` comment: write access, PR open, and refuses fork and deleted-fork PRs. |

## Using an action

Reference it by path and a ref. Pin to a full commit SHA in production workflows.

```yaml
on:
  issue_comment:
    types: [created]

jobs:
  validate:
    if: github.event.issue.pull_request && startsWith(github.event.comment.body, '/deploy')
    runs-on: ubuntu-latest
    permissions:
      issues: write
      pull-requests: write
    outputs:
      head_sha: ${{ steps.gate.outputs.head_sha }}
    steps:
      - id: gate
        uses: SSWConsulting/actions/actions/pr-deploy-gate@main
        with:
          token: ${{ github.token }}
          repository: ${{ github.repository }}
          pr_number: ${{ github.event.issue.number }}
          commenter: ${{ github.event.comment.user.login }}
          comment_id: ${{ github.event.comment.id }}
```

The action does not check out your code, so a caller that only gates does not need `actions/checkout`.

## Versioning

Tags will mark stable versions. Until the first tag, `@main` is the only ref.

## Adding an action

1. Create `actions/<name>/action.yml` with `runs.using: composite`.
2. Put every input into `env:` in the step and reference it as `$VAR` in the script. Do not interpolate `${{ inputs.* }}` inside `run:`.
3. Add a row to the table above.
