# Conda-pack for SLAC

This documentation describes the conda-pack workflow used for assembling production compliant custom Python environments. 

Requirements for effective use:
- Comprehensive pytest module capturing all intented behaviors of your environment
- Github action yaml defined in the `.github/workflows` folder of your project repository
- Conda environment described in the `environment.yml` file in the root of your project repository
- pytest must be included in `environment.yml` requirements

Recommendations:
- Pin only essential packages
- Use only packages available on conda-forge
