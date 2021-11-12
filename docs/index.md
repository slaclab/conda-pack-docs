# Conda-pack for SLAC

This documentation describes the conda-pack workflow used for assembling production compliant custom Python environments. We use a GitHub action to build a conda environment and execute tests within a Docker image aimed to capture the constraints of a production machine. At present, the action supports environments for LCLS RHEL6 machines. Researchers may package their code with a designated `environment.yml` file describing the Python environment relevant to their project or create a designated repository for an environment supporting many projects. 

## Building an environment

Steps:  

* Describe the conda environment in the `environment.yml` file in the root of your project repository. Pin versions for only essential packages. Attempt to use only [conda-forge](https://conda-forge.org/) maintained packages.  

* Write a bash script capturing all intended behaviors of your environment. You may assume your generated Python environment will be active. See: [tests](tests.md)  

* Add [publish](action.md) Github action yaml in the `.github/workflows` folder of your project repository.  

* Create release.  

## Creating a release

Two example actions are outlined in [actions](actions.md): 1. a generic testing build executed on all pushed commits to the main branch and 2. a publish workflow that executes the build on a tagged release then publishes a tarred environment to the release tag. Releases and tags must be used to preserve versioning and identify incompatibilities.

## Using the environment

A published environment will be available as a file attached to a release. Following download, the environment may be untarred then unpacked:

```
$ mkdir my_environment
$ tar -xvf my_environment.tar.gz -C my_environment
$ source my_environment/bin/activate
$ my_environment/bin/conda-unpack
```
The environment will not work if moved after running unpack. Make sure to perform the above in the same location you'd like the environment to live. The resulting environment may then be used by sourcing `source path/to/my_environment/bin/activate` going forward.


## Deploying to production

In order to deploy your release to production, post in the `lcls-python-environments` Slack channel. We will work with the developer on executing additional tests on dev beore deploying to prod. Functionality cannot be guaranteed beyond that described in testing. 

All deployments will be tracked through jobs submitted on CATER #152805.


## Notes
This workflow will attempt to support all [conda-forge](https://conda-forge.org/) maintained packages. Other packages (pip etc.) may be accepted given sufficient warning to (post in `lcls-python-environments` channel for consult); however, miscellaneous support cannot be guaranteed.