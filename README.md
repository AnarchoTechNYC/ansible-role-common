# Anarcho-Tech NYC: Common [![Build Status](https://travis-ci.org/AnarchoTechNYC/ansible-role-common.svg?branch=master)](https://travis-ci.org/AnarchoTechNYC/ansible-role-common)

This role provides a hardened-by-default baseline configuration for Ansible-managed nodes. Notably, this role has been tested with [Raspbian](https://www.raspbian.org/) on [Raspberry Pi](https://www.raspberrypi.org/) hardware. This role's purpose is to make it simple to prepare a host to function as any kind of server that can be securely managed using additional Ansible roles.

## Role variables

* `hardened_hosts`: Boolean indicating whether or not to run baseline [hardening](#hardened-configuration) tasks. Defaults to `true`.

### Hardened configuration

By default, this role will perform baseline hardening tasks. (You can have the role skip hardening tasks by setting `hardened_hosts` to `false`). You can further configure how the managed nodes will be hardened by setting any of the following variables:

* `sshd_allow_group`: Determines the Operating System user account to restrict SSH access. For example:
    ```yml
    hardened_hosts: true        # Perform system hardening.
    sshd_allow_group: ssh-users # Limit SSH access to users in the `ssh-users` group.
    ```
    If `hardened_hosts` is `true` but `sshd_allow_group` is undefined, system hardening will proceed but the procedure will not restrict SSH access by user group.
