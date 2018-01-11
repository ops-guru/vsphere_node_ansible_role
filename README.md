# ops-guru.vsphere_node

operates vcenter nodes

# Requirements

1. Python module `pyvmomi` installed in your python interpreter
1. Python sample code from VMWare's PyVMomi git repository: [pyvmomi-community-samples](https://github.com/vmware/pyvmomi-community-samples)
    * Set either environment variable `PYVMOMI_SAMPLES` or extra variable `pyvmomi_samples`
        * it should point to the absolute path of folder  `/samples` under this repo's root
1. vault should contain at least:
    1. `vault_vcenter_username`
    1. `vault_vcenter_password`

## Role Variables

* No defaults
* Variables are defined in `vars/main.yml`
* Unusable sample candidate vault file is in `vars/vault.example.yml`

Yet the most convenient for manual/automated execution would be overriding the above with extra variables `JSoN` or `YaML`

## Dependencies

None

## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - name: setup vms
      hosts: localhost
      roles:
         - role:      ops_guru.vsphere_node
           operation: setup

## Opinions/Operations/Usage

**Opinions**

"Modal" means by setting a special variable `operation` you control what the role does. 
This role is "modal", please do not complain about this :) 

**Excuses:**

* There is re-use of some of the tasks lists, so splitting each operation into its own role would end up pulling ALL of them anyway.
* It is easier to have the code in 1 repo

**Supported Operations:**

* `setup` - spins up a list of vms from the same template on the same datacenter
* `revert` - reverts list of vms under the same vcenter folder from the same snapshot name (they end up powered off!) 
* `powered_on` - powers on list of vms from the same datacenter under the same folder
* `powered_off` - powers off list of vms from the same datacenter under the same folder

### Operation notes on: `powered_on`

Runs over the list of vm names `poweron_vms_list` and powers them on

#### Limitations
* default multiple VMs match is 'first' VM. you may want to experiment with `name_match` of `vmware_guest_snapshot` module

### Operation notes on: `powered_off`

Runs over the list of vm names `poweroff_vms_list` and powers them off

### Operation notes on: `revert`

Runs over the list of vm names `revert_vms_list` and reverts them to snapshot name `vm_snapshot_name`

#### Limitations

**WARNING:**

Please beware of multiple snapshots with the same name.
This code hopefully assumes there is no such atrocity in your vCenter.

### Operation notes on: `setup`

One of the things the role can do is set up networking on the target, if it is static.
For that the following variables needs to set (in vault or clear text, if you're "brave"):
* `vault_vm_username` (usually: `root`)
* `vault_vm_password`

**NOTE:**

        It is a bit dangerous hack, it creates network interface config file, but does not start networking.
        If you want to be on the safe side, i.e. to disable root,
        you may want to run some commands after the network file has been put to place.
        e.g., you may want to disable root's login altogether (granted you have a different user able to login
        task list `upload.yml` can be improved to upload arbitrary files, e.g. `authorized_keys`

## ToDo/PRs/Feedback

Please feel free to clean up the code, add more operations, and remove limitations (same datacenter, same folder)

You may:

1. open github issue for bugs/features/questions
1. handle open github issues and open pull requests
1. stick your nose into existing pull requests and express your opinion on them



# License

MIT

# Author Information

* Viktor Berlov <vktrbrlv@gmail.com>
* Max Kovgan <max@opsguru.io>
