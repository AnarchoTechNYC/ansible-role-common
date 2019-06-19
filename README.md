# Anarcho-Tech NYC: Common [![Build Status](https://travis-ci.org/AnarchoTechNYC/ansible-role-common.svg?branch=master)](https://travis-ci.org/AnarchoTechNYC/ansible-role-common)

This role provides a [hardened-by-default baseline](#hardened-baselines) configuration for Ansible-managed nodes. Notably, this role has been tested with [Raspbian](https://www.raspbian.org/) on [Raspberry Pi](https://www.raspberrypi.org/) hardware. This role's purpose is to make it simple to prepare a host to function as any kind of server that can be securely managed using additional Ansible roles.

## Hardened baselines

This role incorporates a number of security considerations that are not often found in "out-of-the-box" configurations. Special attention has been paid to ensure these security considerations function as intended on Raspberry Pi hardware. These considerations include:

* Recent Linux kernel with [AppArmor LSM](https://en.wikipedia.org/wiki/AppArmor) loaded and enabled, either via GRUB default boot menu entry or compiled-in when running on Raspberry Pi hardware.
* [Disk quota](#configuring-disk-quotas) support enabled with configurable per-user or per-group disk utilization limits (on `ext3`, `ext4`, or `xfs` filesystems).
* :constrution: TK-TODO: Describe additional security considerations.

## Role variables

* `hardened_hosts`: Boolean indicating whether or not to run baseline [hardening](#hardened-configuration) tasks. Defaults to `true`.
* `enable_root_fs_disk_quotas`: Whether or not to turn on disk quotas for the root filesystem. Defaults to the value of `hardened_hosts`.

### Hardened configuration

When `hardened_hosts` is `true` (the default), this role will perform baseline hardening tasks. You can further configure how the managed nodes will be hardened by setting any of the following variables:

* `sshd_allow_group`: Determines the Operating System user account to restrict SSH access. For example:
    ```yaml
    hardened_hosts: true        # Perform system hardening.
    sshd_allow_group: ssh-users # Limit SSH access to users in the `ssh-users` group.
    ```
    If `hardened_hosts` is `true` but `sshd_allow_group` is undefined, system hardening will proceed but the procedure will not restrict SSH access by user group.
* `disk_quotas_users`: List of disk utilization limits imposed on a per-filesystem basis for a given Operating System user account. Each list item is a dictionary with the following structure:
    * `name`: The name of the user account to set a quota for.
    * `block_soft`: The soft limit for the amount of disk space that the given user can take up. Setting this to `0` means "no limit." The suffixes `K`, `M`, `G`, and `T` can be used to express kibibytes, mebibytes, gibibytes, and tebibytes, respectively. See the manual page for `setquota(8)` for more details.
    * `block_hard`: The hard limit for disk space used. The same semantics apply as for `block_soft`.
    * `inode_soft`: The soft limit for the number of files and directories that the given user can create. The same semantics apply as for `block_soft`.
    * `inode_hard`: The hard limit for number of files and directories created. The same semantics as above apply.
    * `filesystem`: The mount point of the filesystem to apply the quota on.
* `disk_quotas_groups`: List of disk utilization limits imposed on a per-filesystem basis for a given Operating System user group. Each list item is a dictionary whose structure is identical to the `disk_quotas_users` list items.

## Configuring disk quotas

It is often important to [impose disk utilization limits](https://wiki.archlinux.org/index.php/Disk_quota) on a certain [user account or user group](https://wiki.archlinux.org/index.php/Users_and_groups) to ensure that a rogue process or compromised service can not eat up all the available space on a given [filesystem](https://wiki.archlinux.org/index.php/File_systems). This can be trivially configured with the `disk_quotas_users` and `disk_quotas_groups` lists. For example, this snippet will configure the server to allow the `www-data` user (the account under which a typical Web server runs) access to no more than 50 gibibytes of space on the default filesystem:

```yaml
disk_quotas_users:
  - name: www-data
    block_hard: 50G
```
