# kubepug-installer GitHub Action

This action enables you to install and use [kubepug](https://github.com/kubepug/kubepug) in your GitHub action workflow.

## Usage

Add the following entry to your Github workflow YAML file:

```yaml
uses: kubepug/kubepug-installer@main
with:
  kubepug-release: '1.7.1' # optional
```

Example using a pinned version:

```yaml
jobs:
  test_action:
    runs-on: ubuntu-latest

    permissions:
      actions: none
      checks: none
      contents: none
      deployments: none
      issues: none
      packages: none
      pull-requests: none
      repository-projects: none
      security-events: none
      statuses: none

    name: Install kubepug and test presence in path
    steps:
      - name: Install kubepug
        uses: kubepug/kubepug-installer@main
        with:
          kubepug-release: '1.7.1'
      - name: Check install!
        run: kubepug --version
```

Example using the default version:

```yaml
jobs:
  test_action:
    runs-on: ubuntu-latest

    permissions:
      actions: none
      checks: none
      contents: none
      deployments: none
      issues: none
      packages: none
      pull-requests: none
      repository-projects: none
      security-events: none
      statuses: none

    name: Install kubepug and test presence in path
    steps:
      - name: Install kubepug
        uses: kubepug/kubepug-installer@main
      - name: Check install!
        run: kubepug --version
```

This action does not need any GitHub permission to run, however, if your workflow needs to update, create or perform any
action against your repository, then you should change the scope of the permission appropriately.

For example, if you are using the `gcr.io` as your registry to push the images you will need to give the `write` permission
to the `packages` scope.

Example of a simple workflow:

```yaml
jobs:
  test_action:
    runs-on: ubuntu-latest

    env:
      HELM_VERSION: "v3.2.4"
      K8S_TARGET_VERSION: "v1.16.0"

    permissions:
      actions: none
      checks: none
      contents: none
      deployments: none
      issues: none
      packages: write
      pull-requests: none
      repository-projects: none
      security-events: none
      statuses: none
      id-token: write # needed for signing the images with GitHub OIDC **not production ready**

    name: Install kubepug
    steps:
      - uses: actions/checkout@master
        with:
          fetch-depth: 1

      - uses: azure/setup-helm@v1
        with:
          version: '<version>' # default is latest stable
        id: install

      - name: Install kubepug
        uses: kubepug/kubepug-installer@main

      - name: Run Kubepug with your Helm Charts Repository
        run: |
          find charts -mindepth 1 -maxdepth 1 -type d | xargs -t -n1 -I% /bin/bash -c 'helm template % --api-versions ${K8S_TARGET_VERSION} | kubepug --error-on-deprecated --error-on-deleted --k8s-version ${K8S_TARGET_VERSION} --input-file /dev/stdin'
```

### Optional Inputs
The following optional inputs:

| Input | Description |
| --- | --- |
| `kubepug-release` | `kubepug` version to use instead of the default. |
