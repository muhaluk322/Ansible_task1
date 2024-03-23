# Ansible Task 1

## Task Description

It is required to develop a code (Configuration as a Code) using any tool from the list: Ansible (preferred), Chef, Puppet, that configures LVM on a new disk, adds PV, VG, LV, formats volume as ext4, then mounts it as replacement of /var (with /etc/fstab entry), preserving all data from source /var in the way, the OS works as before operation (OS still works and writes to logs).

### Acceptance Criteria

- Code should implement its changes only once, regardless of future runs (idem potency)
- OS works without any issues that were not present before changes, and logs are stored on /var/log mount point and are available for OS and daemons to write, and for the root user to read
- No error related to operation appears in logs after a task is completed
- /var is mounted by /etc/fstab and OS is resistant to a situation when a disk is disconnected (log continuity doesn't matter)
- Acceptance tests should be executed in a simple environment (dependency list is described)
- Code is stored in GIT

### Pre-requirements

- A VM with Linux.
- A Configuration as Code (CaC) tool with an available agent (if required) and verified connectivity to the infrastructure environment.
- Git and the CaC tool installed on a local machine.
- Access to the Linux VM established via SSH keys.

## Setup Instructions

1. **Prepare Your Inventory**: Update the IP address of your VM's host in the inventory file. Copy the SSH key to your target machine using:
`ssh-copy-id <IP>`

2. **Disk Preparation**: Attach a disk of at least 10GB to your VM. Verify the disk name in the system  using `lsblk`(likely `/dev/sdb` for a second disk) and update it in `vars.yml`.

3. **Run the Ansible Playbook**:
Execute the playbook with the following command:

`ansible-playbook -i inventory.yml main.yml --ask-become-pass`

4. **Verification**:
On the host machine, verify that the LV is mounted as `/var` by executing `lsblk`. The output should resemble:

```
lsblk
NAME              MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                 8:0    0  42,3G  0 disk
├─sda1              8:1    0     1M  0 part
├─sda2              8:2    0   513M  0 part /boot/efi
└─sda3              8:3    0  41,8G  0 part /var/snap/firefox/common/host-hunspell
                                            /
sdb                 8:16   0  10,2G  0 disk
└─sdb1              8:17   0  10,2G  0 part
  └─vg_var-lv_var 253:0    0  10,2G  0 lvm  /var
sr0                11:0    1  1024M  0 rom
```

Ensure that the mounted volume will persist after a reboot by verifying the /etc/fstab entry. The correct entry should look like this:
```
...
/dev/vg_var/lv_var /var ext4 defaults,nofail 0 2
```
