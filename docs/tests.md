## Tests
The Docker image used by the pack flow will execute tests described in a bash script. 
```bash
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
```
Code developers should implement comprehensive testing for their packages. Production behavior can only be supported to the extent that the code behavior is described by in tests.

The bash script is intended to give flexibility to the project and allow for various testing frameworks and multiple test projects. An effort will be made downstream to accomodate the installation of testing packages outside of the project `environment.yml`, but for now testing packages like `pytest` should be included in the environment description.


## Example

An example test file running a pytest suite for a repository is given below:

```bash
#!/bin/bash
set -e

git clone https://github.com/slaclab/lume-epics.git
cd lume-epics
pytest
```

The flag:

```bash
#!/bin/bash
set -e
```
should be included at the top of the testing script to force exit on failed subcommands.


If testing multiple repositories, the script should take care to remove previously tested repositories as to not deplete space in the container.


```bash
#!/bin/bash
set -e

# test lume-epics
git clone https://github.com/slaclab/lume-epics.git
cd lume-epics
pytest

# remove already tested
cd ..
rm -rf lume-epics

# test lume-model
git clone https://github.com/slaclab/lume-model.git
cd lume-model
pytest
```

## Testing your code with pytest
Testing with [pytest](https://docs.pytest.org/en/6.2.x/) can make  existence significantly easier by identifying failures as you develop and reducing bug discovery time. Tests should at least cover the major data structures and functionality of your project. For many SLAC based projects, this may require a restructuring of essential functions into utility scripts rather than notebooks and packaging projects using [Python packaging](https://packaging.python.org/tutorials/packaging-projects/) standards.