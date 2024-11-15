# Custom Ansible Playbooks for SAP

This repository is intended for customized playbooks derived from [`ansible.playbooks_for_sap`](https://github.com/sap-linuxlab/ansible.playbooks_for_sap).


## Target audience
These playbooks are developed to serve as automation to build SAP systems for development and testing purpose.</br>
Included configurations are for cheaper and smaller servers, while retaining full suite of functionalities of used Ansible Roles.


## Changes compared to Ansible Playbooks for SAP
1. SAP Software download is replaced by sourcing from NFS filesystem with intent to:
    - Reduce cost associated with downloading same SAP Software again.
    - Reduce duration of SAP Software preparation, dependent on internet speed and bandwidth.
    - **NOTE**: You are responsible in providing correct software in NFS filesystem (examples are provided or each scenario).
2. Idempotency improvements by additional validation steps 
    - Skip `sap_general_preconfigure`, `sap_hana_preconfigure`, `sap_netweaver_preconfigure` if user `sidadm` is present and instance profile already exists.
    - Skip `sap_install_media_detect` if user `sidadm` is present and instance profile already exists.
    - Skip `sap_swpm` if user `sidadm` is present and instance profile already exists.
    - Skip `sap_hana_install` if HANA default profile already exists.
    - Skip `sap_ha_install_hana_hsr` if replication configuration is already present.
    - Skip `sap_ha_pacemaker_cluster` if cluster configuration is already present
3. Quality of Life improvements (tasks/)
    - `iscsi_client_setup` - (Optional) Setup of client for iSCSI SBD stonith
    - `iscsi_server_setup` - (Optional) Setup of target for iSCSI SBD stonith
    - `nfs_cleanup` - Cleanup of NFS mounts for Netweaver instances
    - `os_register` - Register and unregister BYOS images. Used by terminate playbook.
    - `prepare_inventory` - Creates dynamic inventory based on variable files. Usable for debugging without `sap_infrastructure` role.
    - `tag_resources` - Tag provisioned resources.
4. Development tools (tools/)
    - `cluster_setup.yml` allows you to setup HA cluster on provisioned servers. This is very useful for cluster testing and configuration changes.
    - `cluster_destroy.yml` allows you to remove HA cluster configuration, before executing `cluster_setup.yml`. **This is destructive tool!**
    - `terminate.yml` allows you to terminate resources provisioned in given scenario. **This is destructive tool!**

## Disclaimer
**All playbooks were tested by using `sap_vm_provision_iac_type: "ansible"` as method for provisioning**

**All playbooks were tested on `dev` branch with all recent features and fixes.**</br>
It mainly applies to: `sap_infrastructure` and `sap_install`. It is possible that some features rely on new changes in used roles.

## NFS Filesystem details
Software is provided by NFS filesystem, but there are some differences because of parallel execution.

**HANA**
- HANA HA installation is executed in parallel, which cannot be done using one source NFS.
- HANA software is copied during `sap_install_media_detect` from NFS to /software, then unpacked there.
- This can be seen by separately defined source and target variables
```yaml
sap_install_media_detect_source_directory
sap_install_media_detect_target_directory
```

**ASCS, ERS, PAS, AAS**
- ASCS/ERS/PAS/AAS software is opened directly from NFS filesystem, because they are not executed in parallel.
- This results in source NFS filesystem changes due to unpacking.
- **NOTE** These changes are not harmful because `sap_install_media_detect` will process packaged and unpacked again.

## Structure of this repository

```
ap4s
├── scenarios
│   ├── sap_hana_ha
│   │   ├── vars
|   │   │   ├── aws.yml
|   │   │   ├── ...
|   │   │   └── shared.yml
│   │   └── build.yml
│   │
│   ├── sap_ascs_ha
│   └── ...
│
├── tasks
│   ├── iscsi_client_setup.yml
│   ├── ...
│   └── prepare_inventory.yml
│
├── tools
|   ├── cluster_destroy.yml
|   ├── cluster_setup.yml
|   └── terminate.yml
└── vars
    ├── aws.yml
    ├── ....yml
    └── azure.yml
```

Playbooks:
- Each scenario contains scenario-specific playbook
- Tools folder contains playbooks that will execute on your system, by using same variables as during build

Tasks:
- Tasks cannot be executed by themselves, but they are called from playbooks in scenario and tool folders.

Variables:
- `./vars/` are scenario-wide platform specific that serve as place to store credentials, API user details, bastion details and cloud infrastructure details.
- `./scenario/*/vars/` are scenario specific platform variables that store host details, NFS sources, VIP addresses, etc.
    - `shared.yml` file is setup to allow build of exactly same system across different cloud platforms, with same SID and build parameters.


## Ansible Variable precedence
Ensure that you are familiar with Ansible variable precedence in [Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable).

Principle of executing Ansible Playbooks for SAP and these playbooks is to always use `external variables` with highest precedence.

Execution examples are show in specific order because of this:
1. Main platform variables are loaded
2. Shared scenario variables are loaded
3. Platform and scenario specific variables are loaded.
This way you can override some variables specified for all scenarios.

## Before starting
Ensure that you follow step by step instructions at [GETTING_STARTED](https://github.com/marcelmamula/ansible.custom_sap_playbooks/blob/main/GETTING_STARTED.md)

## Preparing variables before execution
Ensure that you populate all relevant variable files as:
- described in comments of particular variable file
- described in [`ansible.playbooks_for_sap`](https://github.com/sap-linuxlab/ansible.playbooks_for_sap)

## Executing playbooks
Example for building and terminating **SAP HANA HA Scale-up on AWS**.

### Build
```console
cd scenarios/sap_hana_ha
ansible-playbook build.yml -e @../../vars/aws.yml -e @./vars/shared.yml -e @./vars/aws.yml
```

### Destroy cluster
```console
cd scenarios/sap_hana_ha
ansible-playbook ../../tools/cluster_destroy.yml -e @../../vars/aws.yml -e @./vars/shared.yml -e @./vars/aws.yml
```

### Setup cluster
```console
cd scenarios/sap_hana_ha
ansible-playbook ../../tools/cluster_setup.yml -e @../../vars/aws.yml -e @./vars/shared.yml -e @./vars/aws.yml
```

### Terminate provisioned resources
```console
cd scenarios/sap_hana_ha
ansible-playbook ../../tools/terminate.yml -e @../../vars/aws.yml -e @./vars/shared.yml -e @./vars/aws.yml
```

**NOTE**: Loading order of external variable files ensures that:
1. global platform variables are loaded first
2. shared scenario specific variables are loaded second
3. scenario and platform specific variables are loaded last

## Recommended way to execute with custom variables
Updating variable files is easy method, but any future pull/fetch of repository will lead to losing them.</br>
**Following method was used during development and testing to keep all passwords and important information separate from files pushed to repository.**

1. Clone repository
2. Copy variable file you want to use and added `.local` before file type extension. Example: `cp vars/aws.yml vars/aws.local.yml`
3. Execute using new variables files. Example: `ansible-playbook build.yml -e @../../vars/aws.local.yml -e @./vars/shared.local.yml -e @./vars/aws.local.yml`

## Contributors
[Marcel Mamula](https://github.com/marcelmamula)