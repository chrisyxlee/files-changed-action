# files-changed-action

This action diffs 2 configured commits and outputs whether any of the files match the regex passed into the workflow. Exclusions are applied before inclusions. This action may be useful for determining whether subsequent actions should run. The regex style is whatever [grep](https://man7.org/linux/man-pages/man1/grep.1.html) accepts.

## Usage

Make sure you set the correct `fetch-depth` for `actions/checkout`. Otherwise, you may attempt to diff against a commit that does not exist in the repository running in the action. See more at [actions/checkout](https://github.com/actions/checkout).

Also refer to [events that trigger workflows](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows) to understand which fields in the GitHub metadata contains the SHAs of commits you want to compare.

```yaml
steps:
- uses: actions/checkout@v3
  with:
    fetch-depth: 2
- uses: chrisyxlee/files-changed-action@v0.0.4
  with:
    # A list of files to look for.
    include: |
      .*\.go$
      go.sum$
      go.mod$
    # A list of files to exclude from diffs.
    exclude: |
      .*_test.go$
    # The commit containing the latest changes.
    head_ref: ${{ github.event.pull_request.head.sha }}
    # The commit containing the base changes to compare against.
    base_ref: ${{ github.event.pull_request.base.sha }}
    # Suppress logs if you anticipate many files changing.
    output_diffs: ''
```

## Scenarios

```yaml
steps:
- uses: actions/checkout@v3
  with:
    fetch-depth: 2
- uses: chrisyxlee/files-changed-action@v0.0.4
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
  if: steps.files-changed.outputs.modified == 'true'
  run: |
    echo "File was changed."
```
