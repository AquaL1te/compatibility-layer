# Ansible role/playbooks for installing the compatibility layer

This directory contains an Ansible role (`compatibility-layer`) in the subdirectory `roles` which has
all functionality for installing the compatibility layer into an existing Gentoo Prefix installation.
It adds a given overlay to the installation and installs a list of package sets and list of additional packages.
The playbook `install.yml` will execute this role on a given server. 

## Configuration

Before running the playbook, make sure the following settings are correct, and override them if necessary:

### Overlay settings
| Variable | Description | Default value |
| --- | --- | --- |
| custom_overlay_name | Repository name for the custom overlay | eessi |
| custom_overlay_source | Source of the custom overlay | git |
| custom_overlay_url | URL to the custom overlay | https://github.com/EESSI/gentoo-overlay.git |

### CVMFS settings
| Variable | Description | Default value |
| --- | --- | --- |
| cvmfs_start_transaction | Whether a CVMFS transaction should be start at the start | False |
| cvmfs_publish_transaction | Whether a CVMFS transaction should be published at the end | False |
| cvmfs_abort_transaction_on_failures | Whether a CVMFS transaction should be aborted on failures | False |
| cvmfs_repository | Name of your CVMFS repository (used for the transaction) | pilot.eessi-hpc.org |

### Prefix and packages
| Variable | Description | Default value |
| --- | --- | --- |
| gentoo_prefix_path | Path to the root of your Gentoo Prefix installation | /cvmfs/pilot.eessi-hpc.org/compat/x86_64 |
| package_sets | List of package sets to be installed | 2020 |
| prefix_packages | List of additional packages to be installed | - |

## Running the playbook 

The playbook can be run using:
```
ansible-playbook -i hosts -K install.yml
```
The `-K` option will ask for your sudo password, and you have to supply a valid hosts file (here named `hosts`).
By default, the playbook will only run on the host listed in the `cvmfsstratum0servers` section of your hosts file.

### Test playbook with Molecule
[Molecule](https://molecule.readthedocs.io/en/latest/) is designed to test playbooks inside e.g. a [Podman](https://podman.io/getting-started/installation.html) container for idempotency, errors, syntax and custom checks.

On a Fedora 32 system you can get started by the following steps.
1. `sudo dnf in podman python3-molecule`
2. `sudo setsebool -P container_manage_cgroup on`
3. `cd playbooks`
4. `molecule create` to create the container, `molecule converge` to create the container and run the playbook, `molecule test` to do what converge does, but test for idempotency and other tests like e.g. linting, `molecule login` to login into the container interactively. See `molecule --help` for more subcommands.

For more information, check [part 1](https://www.ansible.com/blog/developing-and-testing-ansible-roles-with-molecule-and-podman-part-1) and [part 2](https://www.ansible.com/blog/developing-and-testing-ansible-roles-with-molecule-and-podman-part-2) from the Red Hat Ansible blog or [this](https://redhatnordicssa.github.io/test-ansible-role-molecule-podman) quickstart guide.
