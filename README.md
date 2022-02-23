# `iocage-esp-idf-toolchains`

A package builder for `iocage`. [`Vagrantfile`](Vagrantfile) creates a FreeBSD
VM, and bootstrap it with `ansible`. The `ansible` playbook installs
`poudriere-devel`, creates ports trees and jails. Then, it builds
`project_packages`. When building packages, packages in dependencies are
downloaded from the official FreeBSD package repository, instead of building
them.

The packages are available under `project_poudriere_basefs`.

A GitHub Actions workflow runs the build process on `macos` GitHub Actions
runner.

## Requirements

* `vagrant`
* `virtualbox`
* `python3` and `ansible`

## Usage

```console
ansible-galaxy role install -r requirements.yml
vagrant up
```

[`playbook.yml`](playbook.yml) defines role variables. Notable variables
includes:

* `poudriere_jails`, which is a list of FreeBSD versions
* `poudriere_ports`, which is a list of ports trees
* `project_packages`, which is a list of packages to build
* `project_ports_branch`, which is a branch of the official ports tree to
  use
* `project_binary_package_branch`, which is a branch of the official package
  repository

The default RAM of the VM is 8192 MB. If your machine has less memory, use
smaller value.

The packages are built with `poudriere bulk` with `-C` flags, which removes
built packages, if any, every time. To rebuild packages, run:

```console
vagrant provision
```

To destroy the VM, run:

```console
vagrant destroy
```

## Maintaining packages

`project_ports_branch` should be updated to the latest quarterly branch at
each quarterly.

## Signing key

The key to sign packages is defined in `poudriere_pkg_repo_signing_key`
variable, which is loaded from [`secret.yml`](secret.yml) during the play.
`secret.yml` is encrypted by `ansible-vault`. The `ansible-vault` password
must be in `.ansible_vault_key` file, which is not kept in the git repository
for obvious reasons.

If you are a maintainer, create `.ansible_vault_key` file after cloning the
repository.

```console
touch .ansible_vault_key
chmod 600 .ansible_vault_key
vi .ansible_vault_key
```

To edit `secret.yml`, use `ansible-vault`.

```console
ansible-vault edit secret.yml
```

If you have your own `ANSIBLE_VAULT_PASSWORD_FILE` environment variable
defined in your shell, make sure to `unset` `ANSIBLE_VAULT_PASSWORD_FILE`.

```console
env -u ANSIBLE_VAULT_PASSWORD_FILE ansible-vault edit secret.yml
```

The `ansible-vault` password must be set in `Settings` > `Secrets` >
`Actions`. Create a `Repository secrets` with name `ANSIBLE_VAULT_KEY`.

## `poudriere` web interface

`nginx` runs on `localhost` port 80. The port on the guest machine is
forwarded to port 8000 on the host machine. To see build status and packages,
visit http://localhost:8000/.
