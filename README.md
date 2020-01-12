# signoff-new-commit

A GitHub Action to merge a PR after signing off on new commits without wiping previous reviews.

It is common practice to require a certain number of approvals (e.g. 2) on a pull request before it can be merged in. Similarly, most organizations do not allow code to be merged to master without having been reviewed. GitHub's repository settings allow you to automatically dismiss previous approvals when a new commit is pushed. However, this introduces delay in that the pull request submitter has to find new X number of reviews on code that is - presumably - mostly the same. Simply having 1 additional review to approve the subsequent changes could be sufficient.

This action serves to fulfill this use case. Instead of dismissing past reviews, repository owners can set a requirement for X number of reviews on a pull request and use this action to enforce any code added after receiving that requisite number of requests is still reviewed before merging the code to master.

Example:

PR #1 is created with 3 commits. It receives 2 approving reviews with comments suggesting minor improvements. GitHub allows this PR to be merged since it has received 2 approvals. However, the committer updates the code based on those suggestions and pushes a new commit. This Action is triggered and notices that a commit has been pushed since the last approval and fails. The repository has set this Action as a required status check. Therefore, PR #1 can't be merged until it receives 1 additional approval, at which point this Action passes.

The number of necessary approvals to allow this Action to pass can be customized in the `approvals` input parameter documented below.

## Requirements

This action must be triggered from a pull request event: `on: [pull_request]`. It can be run for specific `pull_request` states but the intended usage is for a general `[pull_request]`. This Action will fail if it is not triggered from a pull request.

## Inputs

### `token`

**Required** A GitHub token authorizing requests to the GitHub API. This comes pre-populated in the `${{ secrets.GITHUB_TOKEN }}` variable by GitHub Actions.

### `approvals`

**Default: `1`**

Optional. The number of required approvals since the last commit.

## Outputs

None

## Example Usage

```yaml
name: Sign off on Commits

on: [pull_request]

jobs:
  approve:
    runs-on: ubuntu-latest

    steps:
      - uses: artis3n/signoff-new-commit@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
```
