# Fairdata Ansible Download Service

Ansible role for deployment of Fairdata Download Service.

## Setting up Molecule test environment

### Build the docker images

`docker build --rm -t local/centos7-systemd -f Dockerfile-systemd .`
`docker build --rm -t local/centos7-systemd-sshd -f Dockerfile-ssh .`

### Install Python Dependencies

This repository uses Poetry for managing Python dependencies securely. Poetry generates very strict requirements.txt files, while enabling easy update of minor security and bug patches from pip with `pyproject.toml` defined version constraints. Generated requirements.txt is guaranteed to lock all dependencies and sub-dependencies. Poetry file `poetry.lock` stores hashes of all dependencies, if the integrity of the dependency-tree ever needs to be verified. 

For full documentation of Poetry, visit the [official documentation](https://python-poetry.org/docs/)

#### Install Poetry

First, install [pipx](https://github.com/pypa/pipx). Pipx is a system-wide Python application installer, that creates virtualenv for every package installed and automatically includes them to path. It can also uninstall any package installed using pipx.  With pipx installed, install Poetry with `pipx install poetry`. After installation, you will have poetry available system-wide. 

#### Installing Dependencies

With virtualenv activated, you can install dependencies either with `pip install -r requirements.txt` or `poetry install`, if you have poetry in the system path.

### Running tests

Molecule is used for testing the ansible role. For full documentation, visit the [Read the Docs](https://molecule.readthedocs.io/en/latest/index.html). Version 2 [Read the Docs](https://molecule.readthedocs.io/en/2.13.1/index.html) has some additional notes.

Run `mol test` to run entire test suite including creating and destroying Docker environment. Run `mol converge` to only run tests after `mol create`, that will provision the docker environment. `mol destroy` will destroy provisioned Docker environment