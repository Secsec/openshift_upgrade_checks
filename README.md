role_openshift_upgrade_check
=========

This role allows you to perform pre-checks on your OpenShift cluster prior launching an upgrade on it. Each checks can be represented by a single task in the role, that can be skipped to avoid redundancy when relaunching the checks.

Requirements
------------
Pass as extra vars the following variables:
 - `role_openshift_upgrade_check_api`
 - `role_openshift_upgrade_check_validate_certs`

Be sure to have a proper `openshift_credentials.yml` or pass it as an extra-vars too. (check the TODO!)

Role Variables
--------------

| Variable | Default | Comments | Examples |
|----------|---------|----------|----------|
|role_prometheus_alerts| see defaults/prometheus_alerts.yml | This variable holds a list of critical alerts, that can be modified if needed | see defaults/prometheus_alerts.yml


Requirements
------------
This role requires the following collection:
 - [Openshift collection](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/openshift) (v2.0.1)
 
Be sure to check prerequisites of this collection too (kubernetes python for example).

Example Playbook
----------------
```
ansible-playbook main.yml -v --extra-vars='role_openshift_upgrade_check_api=<YOUR API URL role_openshift_upgrade_check_validate_certs=<yes/no>' --ask-vault
```


```yaml
- name: Test upgrade role
  hosts: localhost module_defaults:
    - redhat.openshift.openshift_auth:
        host: "{{ role_openshift_upgrade_check_api }}"
        validate_certs: "{{ role_openshift_upgrade_check_validate_certs }}"  
    - kubernetes.core.k8s_info:
        host: "{{ role_openshift_upgrade_check_api }}"
        validate_certs: "{{ role_openshift_upgrade_check_validate_certs }}"  
    - kubernetes.core.k8s: 
        host: "{{ role_openshift_upgrade_check_api }}" 
        validate_certs: "{{ role_openshift_upgrade_check_validate_certs }}"  
    - kubernetes.core.k8s_exec: 
        host: "{{ role_openshift_upgrade_check_api }}" 
        validate_certs: "{{ role_openshift_upgrade_check_validate_certs }}" 
    tasks: 
      - name: Test role 
        include_role: 
          name: role_upgrade_check
```

Conducted checks
----------------

Detailed checks documentation can be found in the [`docs/checks.md`](docs/checks.md) file.

License
-------

BSD

TODO
-------

- Get the openshift_credentials.yml out of the role variables.
- Get the main.yml out of the role variables.
- add a no log parameters on tasks
- sanitize the SSL verification on the uri module

In fact everything should be passed as extra-vars, making the role completely agnostic from the platform on which the prechecks will be launched in, this will really ease an integration with Ansible Tower for example while these tasks are trivials to do.
