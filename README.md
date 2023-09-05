openshift_upgrade_checks
=========

This role allows you to perform pre-checks on your OpenShift cluster prior launching an upgrade on it. Each checks can be represented by a single task in the role, that can be skipped to avoid redundancy when relaunching the checks.

Supported OpenShift versions
=========

This role is intended to be run on any OpenShift 4.X cluster. OpenShift 3.X is not supported by the role.

Variables required
------------
This role requires some vars to function properly. You can pass them as extra vars, through a var file or a vault one.

| Variable | Comments | Examples |
|----------|----------|----------|
|openshift_upgrade_checks_api_url | Holds the cluster api URL | `https://api.cluster.domain.com:6443`
|openshift_upgrade_checks_validate_certs | Should the API and Prometheus certs be validated against the system CA ? | `yes/no` 
|openshift_upgrade_checks_username | Holds the username of the user that will perform the checks | `admin-viewer`
|openshift_upgrade_checks_password | Holds the password of the user that will perform the checks | `really-long-and-secure-password` 

Example Playbook
----------------

Create a playbook based on this example, do not forget to fill in the required vars values : 
```yaml
- name: Test upgrade role
  hosts: localhost 
  vars:
    openshift_upgrade_checks_api_url: "<YOUR_API_URL>"
    openshift_upgrade_checks_validate_certs: <yes/no>
    # We strongly advise to use a vault file to save those vars
    openshift_upgrade_checks_username: "<username>"
    openshift_upgrade_checks_password: "<password>"
  tasks: 
    - name: Test role 
      include_role: 
        name: openshift_upgrade_checks
```

Run the playbook :
```
ansible-playbook main.yml -v 
```

Role Variables
--------------

| Variable | Default | Comments | Examples |
|----------|---------|----------|----------|
|openshift_upgrade_checks_prometheus_alerts| see defaults/prometheus_alerts.yml | This variable holds a list of critical alerts, that can be modified if needed | see defaults/prometheus_alerts.yml


Requirements
------------
This role requires the following collection:
 - [Openshift collection](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/openshift) (v2.0.1)
 
Be sure to check prerequisites of this collection too (kubernetes python for example).

Conducted checks
----------------

Detailed checks documentation can be found in the [`docs/checks.md`](docs/checks.md) file.

License
-------

BSD

TODO
-------

- Get the main.yml out of the role variables.
- add a no log parameters on tasks
- sanitize the SSL verification on the uri module

In fact everything should be passed as extra-vars, making the role completely agnostic from the platform on which the prechecks will be launched in, this will really ease an integration with Ansible Tower for example while these tasks are trivials to do.
