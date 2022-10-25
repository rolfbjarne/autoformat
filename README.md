# Autoformat

This is a GitHub Action to automatically format source code in pull requests using `dotnet format`.

There are three ways of specifying what to format:

1. Passing a value for `script`. This is the path to a shell script that will be executed and should format the source code as desired (technically it can modify any files in the repository for any reason).
2. Passing a space-separated list of projects for `projects`. The action will call `dotnet format <project>` on each project (only if `script` isn't set).
3. The default is to run `dotnet format whitespace --folder <root directory>` (if neither `script` nor `project` is set). This will format the entire repository.

If the repository contains any modified files after the `dotnet format` command
(or the script) has executed, the action will create a patch with those changes.

The patch will be zipped up, and uploaded as an artifact.

If the patch should be automatically applied to the pull request, then use the
[rolfbjarne/autoformat-push](https://github.com/rolfbjarne/autoformat-push)
action.

## Usage

Add a new file named `autoformat.yml` in the `.github/workflows` directory in your repository:

```yaml
name: Autoformat code
on: pull_request

# This action only need a single permission in order to autoformat the code.
permissions:
  contents: read

jobs:
  autoformat-code:
    name: Autoformat code
    runs-on: ubuntu-latest

    steps:
    - name: 'Autoformat'
      uses: rolfbjarne/autoformat@v0.1
      with:
        projects: myproject.csproj
```

## Configuration

```yaml
uses: rolfbjarne/autoformat@v0.1
  with:
    # A script to execute that will run 'dotnet format' (or any other logic that changes any committed files)
    # If neither 'script' nor 'projects' is specified, the action will run 'dotnet format whitespace' on the entire repository.
    script: ''

    # Space-delimited list of projects (relative to the root of the repository) to format.
    # This option has no effect if 'script' is specified.
    # If neither 'script' nor 'projects' is specified, the action will run 'dotnet format whitespace' on the entire repository.
    projects: ''

    # The committer's email for the patch
    git_user_email: 'autoformat@example.com'

    # The committer's name for the patch
    git_user_name: 'GitHub Actions Autoformatter'
  
    # The commit message to use for the patch
    git_commit_message: 'Auto-format source code'

    # The name of the artifact where the patch is stored
    artifact: 'autoformat'
```
