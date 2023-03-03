# files-changed-action

This action diffs 2 configured commits and outputs whether any of the files match the regex passed into the workflow. Exclusions are applied before inclusions. This action may be useful for determining whether subsequent actions should run.

## Usage

Make sure you set the correct `fetch-depth` for `actions/checkout`. Otherwise, you may attempt to diff against a commit that does not exist in the repository running in the action. See more at [actions/checkout](https://github.com/actions/checkout).

Also refer to [events that trigger workflows](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows) to understand which fields in the GitHub metadata contains the SHAs of commits you want to compare.

```yaml
steps:
- uses: actions/checkout@v3
  with:
    fetch-depth: 2
- uses: chrisyxlee/files-changed-action@main
  with:
    # A list of files to look for.
    include: |
      .*\.go$
      go.sum$
      go.mod$
    # A list of files to exclude from diffs.
    exclude: |
      .*_test.go$
    # The commit containing the newest changes.
    new: ${{ github.event.pull_request.head }}
    # The commit containing the old changes to compare against.
    old: ${{ github.event.pull_request.base }}
```

## Scenarios

```yaml
steps:
- uses: actions/checkout@v3
  with:
    fetch-depth: 2
- uses: chrisyxlee/files-changed-action@main
  id: files-changed
  with:
    # A list of files to look for.
    include: |
      .*\.go$
      go.sum$
      go.mod$
    exclude: |
      .*_test.go$
- name: other-action
  if: files-changed.outputs.modified == 'true'
  run: |
    echo "File was changed."
```
