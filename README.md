cdn-version-tree-by-tag-builder
==============

[GitHub Action](https://github.com/features/actions) for publishing a tag to a branch
and its contents to another git repository.

Publish files to a git repository from a tag in semantic tree structure
i.e. tag v1.10.19 is submitted
1. split on first dot (this is major)
2. create folder using first part {major}
3. create folder '{major}.x.x-latest' inside said {major} folder and copy files (or delete and re-create)
4. split on second dot (this is minor)
6. inside {major folder} create folder '{major}.{minor}.x-latest' and copy files (or delete and re-create)
7. inside {major folder} create folder of exact tag nam'{tag} and copy files (or delete and re-create)

Structure would be:
```text
v1
|- v1.x.x-latest
|- v1.10.x-latest
|- v1.10.19
```

And it should build a tree like so:
```text
|- v1
|- |- v1.x.x-latest
|- |- v1.10.x-latest
|- |- v1.10.19
|- |- v1.10.20
|- |- v1.11.x-latest
|- |- v1.11.1
|- |- v1.11.2
|- v2
|- |- v2.x.x-latest
|- |- v2.0.x-latest
|- |- v2.0.0
|- |- v2.0.1
|- |- v2.0.2
```

**NOTE**: GitHub currently requires the use of a Personal Access Token for
pushing to other repositories. Pushing to the current repository should work
with the always-available GitHub Token (available via
`{{ secrets.GITHUB_TOKEN }}`. If pushing to another repository, a Personal
Access Token will need to be [created](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line) and assigned to the
workflow [secrets](https://help.github.com/en/articles/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables).

Inputs
------

- `git_ref`: The github reference (required). '${{ github.ref }}'
- `repository`: Destination repository (default: current repository).
- `branch`: Destination branch (required).
- `host`: Destination git host (default: `github.com`).
- `github_token`: GitHub Token (required; use `secrets.GITHUB_TOKEN`).
- `github_pat`: Personal Access Token or other https credentials.
- `source_folder`: Source folder in workspace to copy (default: workspace root).
- `target_folder`: Target folder in destination branch to copy to (default: repository root).
- `commit_author`: Override commit author (default: `{github.actor}@users.noreply.github.com`).
- `commit_message`: Set commit message (default: `[workflow] Publish from [repository]:[branch]/[folder]`).
- `dry_run`: Does not push if non-empty (default: empty).
- `working_directory`: Location to checkout repository (default: random location in `${HOME}`)
- `initial_source_folder`: Source folder in workspace to copy if branch didn't exist (default: `source_folder` value)
- `initial_commit_message`: Commit message if branch didn't exist (default: `Initial commit`)

Outputs
-------

- `commit_hash`: SHA hash of the new commit.
- `working_directory`: Working directory of git clone of repository.

License
-------

MIT License. See [LICENSE](LICENSE) for details.

Usage Example
-------------

```yaml
jobs:
  - name: Publish
    uses: qld-gov-au/gha-cdn-version-tree-by-tag-builder@master
    with:
      repository: ${{ secrets.TARGET_REPO }}
      git_ref: '${{ github.ref }}'
      branch: 'release'
      github_token: '${{ secrets.GH_TOKEN }}'
      github_pat: '${{ secrets.GH_PAT }}'
    if: success()
```
