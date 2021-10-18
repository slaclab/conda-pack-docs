# Conda-pack for SLAC

This documentation describes the conda-pack workflow used for assembling production compliant custom Python environments. We use a Github action to build a conda environment and execute tests within a Docker image aimed to capture the constraints of a production machine. At present, the action supports environments compatable with LCLS RHEL6 machines. Researchers may package their code with a designated `environment.yml` file describing the Python environment relevant to their project or create a designated repository for an environment supporting many projects. 

## Building an environment

Steps:
- Describe the conda environment in the `environment.yml` file in the root of your project repository. Pin versions for only essential packages. Attempt to use only [conda-forge](https://conda-forge.org/) maintained packages.
- Write a bash script capturing all intended behaviors of your environment. You may assume your generated Python environment will be active. 
- Add publish Github action yaml in the `.github/workflows` folder of your project repository.
- Create release.

## Creating a release

Two example actions are outlined in [actions](actions.md): a generic testing build executed on all pushed commits to the main branch and a publish workflow that executes the build on a tagged release then publishes a tarred environment to the release tag. Releases and tags must be used in order to preserve versioning and identify incompatibilities.

## Deploying to production

Production deployment requires a passing build and the file transfer will be handled by Jackie Garrahan (jgarra@slac.stanford.edu, or Jackie Garrahan via SLAC slack). Before migrating to prod, an attempt will be made to work with the developer on executing dev tests. Functionality cannot be guaranteed beyond that described in testing. 

All deployments will be tracked through jobs submitted on CATER #152805.


## Notes
This workflow will attemopt to support all [conda-forge](https://conda-forge.org/) maintained packages. Other packages (pip etc.) may be accepted given sufficient warning to Jackie Garrahan (jgarra@slac.stanford.edu, or Jackie Garrahan via SLAC slack); however, miscellaneous support cannot be guaranteed.


# lcls-rhel6-conda-docker

The Docker image used by the conda-pack action aims replicate an LCLS production RHEL6 machines for environment build and test. While resolved conda environments are largely transferable, there are some instances of virtual packages that may break a packed environment. [Virtual packages](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-virtual.html) include `__cuda`, `__osx`, `__glibc`, `__linux`, `__unix`, `__win`. Of particular importance to our conda environment builds is the `glibc` version. The RHEL6 `glibc=2.12`, may be incompatible with some compiled code. For this reason, we aim to pack our environments in as-similar-a-machine as possible. Additional requirements and tests may be added as needed, with the aim of a robust testing tool before deployment.

The image requires mounting of an environment file to `/tmp/environment.yml`, mounting of the repository to `/tmp/project`, mounting of a testing script to `/tmp/run-test.sh`, and setting of the environment variable `ENVIRONMENT_NAME`; however, these are all handled by the GitHub action [`slaclab/lcls-rhel6-conda-pack`](https://github.com/slaclab/lcls-rhel6-conda-pack) and aren't intended to be used directly except in local testing.

Releases to this repository trigger a workflow that builds the docker image using the repository Dockerfile. The resulting image is published to [dockerhub](https://hub.docker.com/repository/docker/jgarrahan/lcls-rhel6-conda-docker) using the release version as a tag. 

## Tests
The Docker image specified by this repository expects tests defined and run using a bash script mounted to `/tmp/run-test.sh`. An effort should be made by code developers to implement comprehensive testing for their packages with the understanding that the efficacy of this pack-and-test process is a function of their effort in doing so.


#mambaforge