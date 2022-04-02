
 role_openshift_upgrade_check
=========

This role allows you to perform upgrade pre-checks on your OpenShift cluster, launching an upgrade on the cluster and perform post-checks to verify that the cluster upgraded correctly. Each checks can be represented by a single task in the role, that can be skipped to avoid redundancy when relaunching the checks.
To skip a task use the ```--skip-tags``` option. 

Requirements
------------
Create an Ansible vault file for your openshift credentials and servicenow credentials:

```bash
cd role_upgrade_check/defaults/
ansible-vault create openshift_credentials.yml
```

Add your credentials to the file just created:

```bash
role_upgrade_check_openshift_username: $username
role_upgrade_check_openshift_password: $pass
role_upgrade_check_snow_username: $snowusername
role_upgrade_check_snow_password: $snowpass
role_upgrade_check_snow_instance: $snowinstance
```

Role Variables
--------------

| Variable | Default | Comments | Examples |
|----------|---------|----------|----------|
|role_prometheus_alerts| see defaults/prometheus_alerts.yml | Holds a list of critical alerts that can be modified if needed | see defaults/prometheus_alerts.yml

Create an inventory with all your clusters to upgrade or to perform the pre-checks on and specify the following variables for each cluster ( add ansible_host variable to run the upgrade from several machines ): 

```bash
    clustername1:
      ansible_connection: local
      role_openshift_upgrade_check_api: {{ insert api here }}
      role_openshift_upgrade_check_validate_certs: {{ insert yes or no here to specify wether to validate certificates }}
      type: {{ insert the type of check. It can be full_upgrade, pre_check or post_check }}
      full_run: {{ set true to stop at the first error and false for the plabook to continue witherrors }}
      ocp_version: {{ specify the version you want to upgrade the cluster to }}
      snow: {{ set to true to connect to service now and create incidents when the checks fail }} 
      role_upgrade_channel_name: {{ specify the upgrade channel }}
```

Specify on the main.yml the upgrade strategy: 

- Modify the serial parameter to upgrade 1 cluster at a time sequentially ( set it up to 1 ) or to upgrade 2, 3, 4, .. at a time. You can comment this parameter to update all clusters at the same time. The parameter can be set up as a list as well, check the following documentation fo further information: https://docs.ansible.com/ansible/latest/user_guide/playbooks_strategies.html#setting-the-batch-size-with-serial
```bash
- name: ocp-upgrade-checks
  hosts: all
  serial: 1
```
- Specify the group of clusters to upgrade, set all to upgrade all cluster otherwise specify the cluster group in your inventory ( ex. devclusters )
```bash
- name: ocp-upgrade-checks
  hosts: devclusters
```

Requirements
------------
This role has the following Pre-requisites:
```bash
pip install -r requirements.txt
```
   
 This role requires the following collection:
* [Openshift collection](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/openshift) (v2.0.1): `ansible-galaxy collection install redhat.openshift`

In case you cannot install the collection with this method, download the tarball and install it using the following command:

```bash
ansible-galaxy collection install extras/redhat-openshift-2.0.1.tar.gz -p ~/.ansible/collections
```

Be sure to check prerequisites of this collection too (kubernetes python for example).
**NOTE**: This collection requires the ansible version to be 2.9.17 or higher.

Run the Playbook on Windows
------------
To run the playbook, create a virtual python environment:

```bash
python3 -m pip install --user virtualenv
python3 -m venv env
source env/bin/activate
```

Run the Upgrade Checks
------------

```bash
ansible-playbook main.yml --ask-vault-pass -v
```
Fill in the values you are prompted for with:
* role_openshift_upgrade_check_api: The cluster's api URL (for example: https://api.erd5.paas.amadeus.net:6443)
* role_openshift_upgrade_check_validate_certs: Whether to validate certificates when authenticating to openshift. 
* full_run: Wheher to stop the run at the first error (no) or do a full run, ignoring errors, and send the results to a report. 
* type: Type of check you wish to perform ( pre_check or post_check )

What the playbook does
-------

( to be updated )

You can review the tasks performed by the playbook in the directory ```role_upgrade_check/tasks```. Depending on the values submitted to the playbook, the workflow of the playbook run will look like one of the following: 

**Upgrade Pre-Checks**

![alt text](https://rndwww.nce.amadeus.net/git/projects/MTPAAS/repos/4.x_upgradeprechecksrh/raw/images/_Upgrade%20Pre-Checks%20(full_run_%20yes).png?at=refs%2Fheads%2Fmaster)

![alt text](https://rndwww.nce.amadeus.net/git/projects/MTPAAS/repos/4.x_upgradeprechecksrh/raw/images/Upgrade%20Pre-Checks%20(full_run_%20no%20).png?at=refs%2Fheads%2Fmaster)

**Upgrade Post-Checks**

![alt text](https://rndwww.nce.amadeus.net/git/projects/MTPAAS/repos/4.x_upgradeprechecksrh/raw/images/Upgrade%20Post-Checks%20(full_run_%20yes).png?at=refs%2Fheads%2Fmaster)

![alt text](https://rndwww.nce.amadeus.net/git/projects/MTPAAS/repos/4.x_upgradeprechecksrh/raw/images/Upgrade%20Post-Checks%20(full_run_%20no).png?at=refs%2Fheads%2Fmaster)

License
-------

BSD

TODO
-------

* Get the openshift_credentials.yml out of the role variables.
* Get the main.yml out of the role variables.
* sanitize the SSL verification on the uri module

Tasks TODO: 
* Check for System out of memory events.
* Verify API health point 
* Verify all ETCD members are available 
* Update tasks to use single rescue block
* Test the process
* set up ignore_errors: true for servicenow incident creation
* rename full_run variable to stop_at_first_error
* rename snow variable to service_now

In fact everything should be passed as extra-vars, making the role completely agnostic from the platform on which the prechecks will be launched in, this will really ease an integration with Ansible Tower for example while these tasks are trivials to do.
