# GitHub Actions

![](https://i.imgur.com/waxVImv.png)
### [View all Roadmaps](https://github.com/nholuongut/all-roadmaps) &nbsp;&middot;&nbsp; [Best Practices](https://github.com/nholuongut/all-roadmaps/blob/main/public/best-practices/) &nbsp;&middot;&nbsp; [Questions](https://www.linkedin.com/in/nholuong/)
<br/>

GitHub Actions master template & GitHub Actions Reusable Workflows library.

- [main.yaml](https://github.com/nholuongut/github-actions/blob/master/main.yaml) - GitHub Actions master workflow template
- [.github/workflows/](https://github.com/nholuongut/github-actions/tree/master/.github/workflows) - GitHub Actions Reusable Workflows Library

See [Documentation](https://docs.github.com/en/actions/using-workflows/reusing-workflows#calling-a-reusable-workflow) for how to call these workflows directly from your own GitHub Actions workflow.

Fork this repo to have full control over all updates via Pull Requests. Create environment branches to stage updates across dev/staging/production.

Forked from [nholuongut/Templates](https://github.com/nholuongut/templates), for which this is now a submodule.

To see GitHub Contexts available, including undocumented fields, see [nholuongut/github-actions-Contexts](https://github.com/nholuongut/github-actions-Contexts).

## Examples

In your GitHub repo, import these workflows by adding small yaml files to the `.github/workflows/` directory.

## Scan for Secrets and Security issues

[![Semgrep](https://github.com/nholuongut/github-actions/actions/workflows/semgrep.yaml/badge.svg)](https://github.com/nholuongut/github-actions/actions/workflows/semgrep.yaml)
Alerts appear under the GitHub repo's Security tab -> Code scanning alerts.

[![Semgrep Cloud](https://github.com/nholuongut/github-actions/actions/workflows/semgrep-cloud.yaml/badge.svg)](https://github.com/nholuongut/github-actions/actions/workflows/semgrep-cloud.yaml) Alerts appear in the <https://semgrep.dev> dashboard

Create `.github/workflows/semgrep.yaml` for local repo alerts:

```yaml
on: [push]
jobs:
  semgrep:
    uses: nholuongut/github-actions/.github/workflows/semgrep.yaml@master
```

or `.github/workflows/semgrep-cloud.yaml` for <https://semgrep.dev> alerts:

```yaml
on: [push]
jobs:
  semgrep:
    uses: nholuongut/github-actions/.github/workflows/semgrep-cloud.yaml@master
    secrets:
      SEMGREP_APP_TOKEN: ${{ secrets.SEMGREP_APP_TOKEN }}
```

## Analyze your Terraform code security & best practices

[![tfsec](https://github.com/nholuongut/Terraform/actions/workflows/tfsec.yaml/badge.svg)](https://github.com/nholuongut/Terraform/actions/workflows/tfsec.yaml)
Alerts appear under Security -> Code scanning alerts.

Create `.github/workflows/tfsec.yaml`:

```yaml
on: [push]
jobs:
  tfsec:
    uses: nholuongut/github-actions/.github/workflows/tfsec.yaml@master
```

## Terraform Plan & Apply

Plans - updates Pull Requests with the results of validation, format check and full Change Plan outputs

Apply - applies when merged to default branch, eg. `master` or `main`

```yaml
on: [push, pull_request]
jobs:
  terraform:
    uses: nholuongut/github-actions/.github/workflows/terraform.yaml@master
    with:
      dir: path/to/terraform/code
    secrets:
      ...
```

For more sophisticated examples including approvals, secrets, branch and path selection etc. see my [Terraform repo](https://github.com/nholuongut/Terraform)'s templates for [terraform-plan.yaml](https://github.com/nholuongut/Terraform/blob/master/.github/workflows/terraform-plan.yaml.template) and [terraform-apply.yaml](https://github.com/nholuongut/Terraform/blob/master/.github/workflows/terraform-apply.yaml.template)

## Docker Build and push to DockerHub

[![Docker Build DevOps Bash Tools (Ubuntu)](https://github.com/nholuongut/Dockerfiles/actions/workflows/docker_build_devops_bash_tools_ubuntu.yaml/badge.svg)](https://github.com/nholuongut/Dockerfiles/actions/workflows/docker_build_devops_bash_tools_ubuntu.yaml)

Create `.github/workflows/dockerhub_build.yaml`:

```yaml
on: [push]
jobs:
  docker_build:
    uses: nholuongut/github-actions/.github/workflows/dockerhub_build.yaml@master
    with:
      repo: user/repo  # your DockerHub user/repo
      tags: latest v1.1
    secrets:
      DOCKERHUB_USER: ${{ secrets.DOCKERHUB_USER }}
      DOCKERHUB_TOKEN: ${{ secrets.DOCKERHUB_TOKEN }}
```

## Docker Build and push to AWS ECR

Create `.github/workflows/docker_build_aws_ecr.yaml`:

```yaml
on: [push]
jobs:
  docker_build:
    uses: nholuongut/github-actions/.github/workflows/docker_build_aws_ecr.yaml@master
    with:
      repo: MY_ECR_REPO
    secrets:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      AWS_DEFAULT_REGION: ${{ secrets.AWS_DEFAULT_REGION }}
```

Creates several useful tags, supports multi-stage build caching, see [README](https://github.com/nholuongut/github-actions/blob/master/.github/workflows/README.md) for details.

## Check for Broken URL Links

[![URL Links](https://github.com/nholuongut/github-actions/actions/workflows/url_links.yaml/badge.svg)](https://github.com/nholuongut/github-actions/actions/workflows/url_links.yaml)

Create `.github/workflows/url_links.yaml`:

```yaml
on: [push]
jobs:
  url_links:
    uses: nholuongut/github-actions/.github/workflows/url_links.yaml@master
```

See [README](https://github.com/nholuongut/github-actions/blob/master/.github/workflows/README.md) for details on ignoring inaccessible / partially constructed links or those containing variables

## Auto-Merge Production hotfixes back to Staging

Merges via a Pull Request for full auditing.

Create `.github/workflows/merge_production_to_staging.yaml`:

```yaml
on: [push]
jobs:
  merge:
    if: github.ref_name == 'production'
    uses: nholuongut/github-actions/.github/workflows/merge-branch.yaml@master
    with:
      head: production  # from
      base: staging     # to
```

## Mirror Repos to GitLab for DR Backups

Mirrors all/given GitHub repos to GitLab - including all branches and tags, and GitHub repo description

```yaml
on:
  schedule:
    # mirror to GitLab hourly
    - cron: '0 0 * * *'

jobs:
  gitlab_mirror:
    uses: nholuongut/github-actions/.github/workflows/gitlab-mirror.yaml@master
    with:
      #organization: my-org    # optional: mirror your company's repos instead of your personal repos
      #repos: repo1 repo2 ...  # list of repos to mirror, space separated, rather than all repos
    secrets:
      GH_TOKEN: ${{ secrets.GH_TOKEN }}
      GITLAB_TOKEN: ${{ secrets.GITLAB_TOKEN }}
```

## AWS CodeArtifact - Publish a Python Package

```yaml
on:
  tags:
    - v*

jobs:
  aws_codeartifact_python_publish:
    uses: nholuongut/github-actions/.github/workflows/codeartifact_python_publish.yaml@master
    with:
      domain: mycompany     # your AWS CodeArtifact service domain name
      repo: mycompany-core  # your CodeArtifact repo name
      #command: make publish_package  # default. Can be any command using CODEARTIFACT_AUTH_TOKEN and CODEARTIFACT_REPO_URL
    secrets:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      AWS_DEFAULT_REGION: ${{ secrets.AWS_DEFAULT_REGION }}
```

## Production

### Option 1 - Hashref

Import the reusable workflows from this repo as shown above, replacing `@master` with `@<hashref>` to fix to an immutable version (tags are not immutable). This is [GitHub Actions Security Best Practice](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions#using-third-party-actions).

### Option 2 - Public Fork (fully automated)

Fork this repo for more control and visibility over all updates.

Enable the [fork-sync](https://github.com/nholuongut/github-actions/blob/master/.github/workflows/fork-sync.yaml) github actions workflow in your fork to keep the master branch sync'd every few hours.

You can then create tags or environment branches in your forked repo to stage updates across dev/staging/production.

If using environment branches enable the [fork-update-pr](https://github.com/nholuongut/github-actions/blob/master/.github/workflows/fork-update-pr.yaml) github actions workflow to automatically raise GitHub Pull Requests from master to your environment branches to audit, authorize & control updates.

### Option 3 - Private Copy (manual)

Copy `.github/workflows` to a private repo. Not recommended as it's the most manual legacy approach.

You will be responsible for committing and reconciling any divergences in your local copies.

# 🚀 I'm are always open to your feedback.  Please contact as bellow information:
### [Contact ]
* [Name: nho Luong]
* [Skype](luongutnho_skype)
* [Github](https://github.com/nholuongut/)
* [Linkedin](https://www.linkedin.com/in/nholuong/)
* [Email Address](luongutnho@hotmail.com)
* [PayPal.me](https://www.paypal.com/paypalme/nholuongut)

![](https://i.imgur.com/waxVImv.png)
![](Donate.png)
[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/nholuong)

# License
* Nho Luong (c). All Rights Reserved.🌟



