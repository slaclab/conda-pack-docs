## Issues

Unfortunately, despite all best efforts, cases will inevitable arise with external packages. This may be a function of undocumented dependencies, broken packages, or dependence on newer libraries unavailable on RHEL6.

The Slack `lcls-python-environments` channel will be used for discussing ongoing issues and we'll document all findings and workarounds here.

## Known issues
- Use of both Tensorflow and Scikit-Learn require mkl to be listed as a dependency. This issue may be identified with, `ImportError: dlopen: cannot load any more object with static TLS` during build.  
- PyQt6 has not yet been built on conda-forge. Until then, projects are constrained to PyQt5 and below  