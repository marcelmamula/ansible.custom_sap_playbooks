# Step by step for starting with Ansible playbooks for SAP
Below are step by step instructions that are based on [SAP LinuxLab documentation](https://github.com/sap-linuxlab/community.sap_install/tree/main/docs#readme) and personal experience.

## Select your execution server
These custom playbooks and original Ansible playbooks for SAP are all designed to be run from dedicated execution environment.
This can be:
- Server in your network with internet access
    - It can also be execution node of orchestration tool (like Ansible Automation Platform or SUSE Manager)
- Work laptop or computer

## Select your operating system
You can select any OS of your preference as long as it fits criteria:
- Availability to install fairly recent versions of both Python and Ansible
- Availability to install ansible collections

Playbooks were tested on `openSUSE Tumbleweed running in WSL2 container on Windows 10`

## Prepare your cloud environment
Each cloud platform requires base minimum infrastructure when using `ansible` instead of `terraform`.</br>
Follow [documentation on Ansible Playbooks for SAP](https://github.com/sap-linuxlab/ansible.playbooks_for_sap/tree/main/docs#prepare-infrastructure-platform-for-ansible-playbooks-for-sap) 

Example with resources required for AWS before execution:
- VPC
    - VPC Access Control List (ACL)
    - VPC Subnets
    - VPC Security Groups
- Route53 (Private DNS)
- Internet Gateway (SNAT)
- EFS (NFS)
- Bastion host (AWS EC2 VS)
- Key Pair for hosts

**NOTE:** It is possible to deploy most of this infrastructure by using Ansible Playbooks for SAP with `sap_vm_provision_iac_type == "ansible_to_terraform"`, but this is not documented in these custom playbooks.

## Install required packages
Install following packages with appropriate versions:
- Python 3.9 or higher
- Ansible 9
- Ansible 2.16
You can verify compatibility with [Ansible documentation](https://docs.ansible.com/ansible/latest/reference_appendices/release_and_maintenance.html#ansible-community-changelogs)

Playbooks were tested on:
- Python 3.11
- ansible 9.5.1
- ansible-core 2.16.6

**NOTE: Ansible 10+ and ansible-core 2.17+**
- Latest versions were tested and we found lot of issues caused by switch to `from __future__ import annotations` plugins.
- **This is causing dependency on target servers requiring Python 3.7 (potentially 3.9) and higher**
- You can find more information at https://github.com/ansible/ansible/issues/82068


## Install platform specific packages
Each platform requires different packages and their list is available at [ansible.playbooks_for_sap](https://github.com/sap-linuxlab/ansible.playbooks_for_sap/tree/main/docs#prepare-to-execute-ansible-playbooks-for-sap)</br>

- Required for all Ansible Playbooks, use `python -m pip install ansible-core beautifulsoup4 lxml requests passlib jmespath`
    - ansible-core 2.12.0+
    - beautifulsoup4 4.0+
    - lxml 4.0+
    - requests 2.0+
    - passlib 1.7+
    - jmespath 1.0.1+
- If Ansible Playbook provisioning to AWS, use `python -m pip install boto3`
- If Ansible Playbook provisioning to Google Cloud, use `python -m pip install google-auth`
- If Ansible Playbook provisioning to MS Azure, use `python -m pip install -r https://raw.githubusercontent.com/ansible-collections/azure/dev/requirements-azure.txt`
- If Ansible Playbook provisioning to IBM PowerVM, use `python -m pip install openstacksdk`
- If Ansible Playbook provisioning to OVirt, use `python -m pip install ovirt-engine-sdk-python`
- If Ansible Playbook provisioning to VMware, use `python -m pip install aiohttp`


## Install required Ansible Collections
**NOTE: Platform specific Ansible Collections are fully compatible together and caution is advised when trying to run multiple from same execution environment**</br>
You can find detailed list in [ansible_requirements.yml](https://github.com/sap-linuxlab/ansible.playbooks_for_sap/blob/main/deploy_scenarios/sap_hana_ha/ansible_requirements.yml) for respective Ansible Playbooks for SAP scenario.

How to install Ansible collections: `ansible-galaxy collection install community.sap_install`

General collections for all platforms
```yaml
  - name: community.general
    type: galaxy
    version: 6.1.0
  - name: fedora.linux_system_roles
    type: galaxy
    version: 1.82.0
  - name: community.sap_install
    type: galaxy
    version: 1.4.0
  - name: community.sap_launchpad
    type: galaxy
    version: 1.1.0
  - name: community.sap_infrastructure
    type: galaxy
    version: 1.0.1
```

Platform specific collections
```yaml
  - name: cloud.terraform
    type: galaxy
    version: 1.1.0
  - name: amazon.aws
    type: galaxy
    version: 7.2.0
  - name: community.aws
    type: galaxy
    version: 7.1.0
  - name: azure.azcollection
    type: galaxy
    version: 2.2.0
  - name: google.cloud
    type: galaxy
    version: 1.1.3
  - name: ibm.cloudcollection
    type: galaxy
    version: 1.51.0
  - name: ovirt.ovirt
    type: galaxy
    version: 3.1.2
  - name: openstack.cloud
    type: galaxy
    version: 2.1.0
  - name: vmware.vmware_rest
    type: galaxy
    version: 3.0.0
  - name: cloud.common
    type: galaxy
    version: 3.0.0
    ```
```

### Azure collections compatibility
Azure collections are recommended to be installed separately in dedicated Python virtual environment due to conflicts with other collections.</br>
**NOTE: Installing ansible in new virtual environment will default to latest Ansible 10. Please check note above**
