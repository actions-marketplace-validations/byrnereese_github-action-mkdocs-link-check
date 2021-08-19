# GitHub Action - Markdown link check 🔗✔️

This GitHub action checks all Markdown files in your repository for broken links. It has been especially designed for mkdocs, but can also work for any markdown-based website. 

This action uses [byrnereese/linkchecker-mkdocs](https://github.com/byrnereese/linkchecker-mkdocs), and is heavily inspired by:

* [tcort/markdown-link-check](https://github.com/tcort/markdown-link-check)
* [gaurav-nelson/github-action-markdown-link-check](https://github.com/gaurav-nelson/github-action-markdown-link-check)

## How to use

1. Create a new file in your repository `.github/workflows/action.yml`.
1. Copy-paste the following workflow in your `action.yml` file:

   ```yml
   name: Check Markdown links
   
   on: push
   
   jobs:
     mkdocs-link-check:
       runs-on: ubuntu-latest
       steps:
       - uses: actions/checkout@master
       - uses: byrnereese/github-action-mkdocs-link-check@v1
   ```

### Real-life usage samples

Following is a list of some of the repositories which are using GitHub Action -
Markdown link check.

1. [RingCentral API documentation](https://github.com/ringcentral/ringcentral-api-docs/)
   
*(Edit this file and add your name here)*

## Configuration

- [Custom variables](#custom-variables)
- [Scheduled runs](#scheduled-runs)
- [Disable check for some links](#disable-check-for-some-links)

### Custom variables

You customize the action by using the following variables:

| Variable | Description | Default value |
|:----------|:--------------|:-----------|
|`use-quiet-mode`| Specify `yes` to only show errors in output.| `no`|
|`use-verbose-mode`|Specify `yes` to show detailed HTTP status for checked links. |`no` |
|`config-file`|Specify a [custom configuration file](https://github.com/tcort/markdown-link-check#config-file-format) for markdown-link-check. You can use it to remove false-positives by specifying replacement patterns and ignore patterns.|`mlc_config.json`|
|`folder-path` |By default the `github-action-markdown-link-check` action checks for all markdown files in your repository. Use this option to limit checks to only specific folders. Use comma separated values for checking multiple folders. |`.` |
|`max-depth` |Specify how many levels deep you want to check in the directory structure. The default value is `-1` which means check all levels.|`-1` |
|`check-modified-files-only` |Use this variable to only check modified markdown files instead of checking all markdown files. The action uses `git` to find modified markdown files. Only use this variable when you run the action to check pull requests.|`no`|
|`base-branch`|Use this variable to specify the branch to compare when finding modified markdown files. |`master`|
|`file-extension`|By default the `github-action-markdown-link-check` action checks files in your repository with the `.md` extension. Use this option to specify a different file extension such as `.markdown` or `.mdx`.|`.md`|
|`file-path` | Specify additional files (with complete path and extension) you want to check. Use comma separated values for checking multiple files. See [Check multiple directories and files](#check-multiple-directories-and-files) section for usage.| - |

#### Sample workflow with variables

```yml
name: Check Markdown links

on: push

jobs:
  markdown-link-check:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - uses: gaurav-nelson/github-action-markdown-link-check@v1
      with:
        use-quiet-mode: 'yes'
        use-verbose-mode: 'yes'
        config-file: 'mlc_config.json'
        folder-path: 'docs/markdown_files'
        max-depth: 2
```

### Scheduled runs
In addition to checking links on every push, or pull requests, its also a good
hygine to check for broken links regularly as well. See
[Workflow syntax for GitHub Actions - on.schedule](https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions#onschedule)
for more details.

#### Sample workflow with scheduled job

```yml
name: Check Markdown links

on: 
  push:
    branches:
    - master
  schedule:
  # Run everyday at 9:00 AM (See https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07)
  - cron: "0 9 * * *"

jobs:
  markdown-link-check:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - uses: gaurav-nelson/github-action-markdown-link-check@v1
      with:
        use-quiet-mode: 'yes'
        use-verbose-mode: 'yes'
        config-file: 'mlc_config.json'
        folder-path: 'docs/markdown_files'
```

### Disable check for some links
You can include the following HTML comments into your markdown files to disable
checking for certain links in a markdown document.

1. `<!-- markdown-link-check-disable -->` and `<!-- markdown-link-check-enable-->`: Use these to disable links for all links appearing between these
    comments.
   - Example:
     ```md
     <!-- markdown-link-check-disable -->
     ## Section
     
     Disbale link checking in this section. Ignore this [Bad Link](https://exampleexample.cox)
     <!-- markdown-link-check-enable -->
     ```
2. `<!-- markdown-link-check-disable-next-line -->` Use this comment to disable link checking for the next line.
3. `<!-- markdown-link-check-disable-line -->` Use this comment to disable link
   checking for the current line.

### Check only modified files in a pull request

Use the following workflow to only check links in modified markdown files in a
pull request. 

When
you use this variable, the action finds modififed files between two commits:
- latest commit in you PR
- latest commit in the `master` branch. If you are suing a different branch to
  merge PRs, specify the branch using `base-branch`.

> **NOTE**: We can also use GitHub API to get all modified files in a PR, but that
> would require tokens and stuff, create an issue or PR if you need that.

```yml
on: [pull_request]
name: Check links for modified files
jobs:
  markdown-link-check:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - uses: gaurav-nelson/github-action-markdown-link-check@v1
      with:
        use-quiet-mode: 'yes'
        use-verbose-mode: 'yes'
        check-modified-files-only: 'yes'

```

### Check multiple directories and files

```yml
on: [pull_request]
name: Check links for modified files
jobs:
  markdown-link-check:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - uses: gaurav-nelson/github-action-markdown-link-check@v1
      with:
        use-quiet-mode: 'yes'
        folder-path: 'md/dir1, md/dir2'
        file-path: './README.md, ./LICENSE, ./md/file4.markdown'
```

### Too many requests
Use `retryOn429`, `retry-after`, `retryCount`, and `fallbackRetryDelay` in your custom configuration file.
See https://github.com/tcort/markdown-link-check#config-file-format for details.

Or mark 429 status code as alive:
```json
{
  "aliveStatusCodes": [429, 200]
}
```

## Versioning

GitHub Action - Markdown link check follows the [GitHub recommended versioning strategy](https://github.com/actions/toolkit/blob/master/docs/action-versioning.md). 

1. To use a specific released version of the action ([Releases](https://github.com/byrnereese/github-action-mkdocs-link-check/releases)):
   ```yml
   - uses: byrnereese/github-action-mkdocs-link-check@1.0.1
   ```
1. To use a major version of the action:
   ```yml
   - uses: byrnereese/github-action-mkdocs-link-check@v1
   ```
1. You can also specify a [specific commit SHA](https://github.com/byrnereese/github-action-mkdocs-link-check/commits/master) as an action version:
   ```yml
   - uses: byrnereese/github-action-mkdocs-link-check@44a942b2f7ed0dc101d556f281e906fb79f1f478
   ```
