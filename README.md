# Fairdata Ansible Download Service

Ansible role for deployment of Fairdata Download Service.

## Setting up Molecule test environment

### Build the docker images

First, create folder .config to repository root, then add an ssh-key with gitlab.ci.csc.fi access under it. Replace private_key in the second docker build command with your private key-file name.

`docker build --rm -t local/centos7-systemd -f Dockerfile-systemd .`
`docker build --rm --build-arg REPO_SSH_KEY=.config/private_key -t local/centos7-systemd-sshd -f Dockerfile-ssh .`

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

### Debugging the role

Run `mol converge -- --extra-vars="debug=True"` 

## Role Default variables

### Control variables

| name                | default | notes |
| :------------------ | ------- | ----- |
| prepare_application | True    |       |
| deploy_application  | True    |       |
| configure_nginx     | True    |       |
| delete_deployment   | False   |       |

### Deployment variables

| name             | default                         | notes |
| ---------------- | ------------------------------- | ----- |
| deployer         | "'{{ lookup('env', 'USER') }}'" |
| instance_domain  | 'fairdata.fi'                   |
| deployment_name  | 'download'                      |
| host_domain_name | 'ida.fairdata.fi'               |
| instance_name    | 'fairdata-download'             |

### Directory variables

| name                       | default                                               | notes |
| -------------------------- | ----------------------------------------------------- | ----- |
| directory_config           | '/etc/fairdata-download-service'                      |
| directory_source_root      | '/opt'                                                |
| directory_run              | '/run'                                                |
| directory_log              | '/var/log/fairdata-download-service'                  |
| directory_venv             | '/usr/local/download-venvs/fairdata-download-service' |
| directory_cache_root       | '/mnt/download-service-cache'                         |
| directory_ida_storage_root | '/mnt/download-ida-storage'                           |

### Repository variables

| name                | default                                                                   | notes |
| ------------------- | ------------------------------------------------------------------------- | ----- |
| repo_install_method | 'pull'                                                                    |
| repo_version        | 'master'                                                                  |
| repo_dest           | '/opt/fairdata-download-service'                                          |
| repo_url            | 'ssh://git@gitlab.ci.csc.fi:10022/fairdata/fairdata-download-service.git' |
| repo_deploy_key     | ~/.ssh/id_rsa                                                             |       |

### Python virtualenv variables
| name               | default           | notes |
| ------------------ | ----------------- | ----- |
| virtualenv_command | 'python3 -m venv' |

### RabbitMQ variables

| name           | default     | notes |
| -------------- | ----------- | ----- |
| rabbitmq_host  | 'localhost' |
| rabbitmq_vhost | 'download'  |
| rabbitmq_user  | 'download'  |
| rabbitmq_pass  | 'download'  |

### Application variables

| name                              | default                                       | notes |
| --------------------------------- | --------------------------------------------- | ----- |
| application_user                  | 'download'                                    |
| application_group                 | 'download'                                    |
| application_cache_purge_threshold | 120000000000000                               | 15TB  |
| application_cache_purge_target    | 8000000000000                                 | 10TB  |
| application_service_settings      | '/etc/fairdata-download-service/settings.cfg' |
| application_environment           | 'development'                                 |
| application_jwt_secret            | 'secret'                                      |
| application_service_metax_url     | 'https://metax.fairdata.fi'                   |
| application_service_metax_user    | 'download'                                    |
| application_service_metax_pass    | 'download'                                    |

### Nginx variables

| name                      | default                                   | notes |
| ------------------------- | ----------------------------------------- | ----- |
| nginx_url_prefix          | '/v1'                                     |
| nginx_public_port         | 4430                                      |
| nginx_internal_port       | 4431                                      |
| nginx_custom_certificates | False                                     |
| nginx_ssl_certificate     | '/etc/pki/tls/certs/download.local.crt'   |       |
| nginx_ssl_certificate_key | '/etc/pki/tls/private/download.local.key' |       |

### Gunicorn variables
| name             | default                              | notes |
| ---------------- | ------------------------------------ | ----- |
| gunicorn_socket  | '/run/fairdata-download-server.sock' |
| gunicorn_workers | 1                                    |
| gunicorn_threads | 4                                    |

### Celery variables

| name                | default      | notes |
| ------------------- | ------------ | ----- |
| celeryd_nodes       | 'generator1' |
| celeryd_min_workers | 1            |
| celeryd_max_workers | 10           |
| celeryd_log_level   | 'INFO'       |