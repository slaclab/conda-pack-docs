# Conda pack for custom LCLS Python environments

A GitHub action has been defined to the creation and testing of a Python environment using a RHEL6 Docker image aimed at replicating the installation environment of LCLS prod machines. 

In order to use this project, the repository must be packaged with a root level `environment.yml` file constructed using the [conda template](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually). The action creates an environment from this file within the context of the RHEL6 docker image specified by [lcls-rhel6-conda-docker](https://github.com/slaclab/lcls-rhel6-conda-docker) and will store the artifact.

The use of this action in a worklow requires defining the environment variable `ENVIRONMENT_NAME`, which should match the name specified in the `environment.yml`. Optionally (but encouraged), a test bash script may be packaged with the environment repository and indicated to the docker image using `TEST_FILE` environment variable.

```yaml
name: Create conda environment for my-environment

on:
  push:
    branches:
      - main

jobs:
  build:
    env:
      ENVIRONMENT_NAME: my-environment
      TEST_FILE: my-test-file.sh
    runs-on: ubuntu-latest
    name: Build environment
    steps:
      - name: Build environment
        uses: jacquelinegarrahan/lcls-rhel6-conda-pack@v1.0.1
```

The packed and tarred environment is then available in the working directory of the action with the naming scheme `${ENVIRONMENT_NAME}.tar.gz` For example, the following action will upload the packed environment on pushed tags. 

```yaml
name: Publish release

on:  
  push:
    tags:
      - '*'

jobs:
  build:
    env:
      ENVIRONMENT_NAME: my-environment
    runs-on: ubuntu-latest
    name: Build environment
    steps:
      - name: Build environment
        uses: jacquelinegarrahan/lcls-rhel6-conda-pack@v0.2
      - name: Upload artifact to release
        uses: svenstaro/upload-release-action@v2
        with:
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          file: ${{ env.ENVIRONMENT_NAME }}.tar.gz
          tag: ${{ github.ref }}
          overwrite: true
          file_glob: true
```