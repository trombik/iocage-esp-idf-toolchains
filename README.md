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

# Maintaining packages

`project_ports_branch` should be updated to the latest quarterly branch at
each quarterly.
