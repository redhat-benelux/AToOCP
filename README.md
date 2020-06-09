# Install Ansible Tower on AToOCP for demo purposes only

The AT installer has a specific version to deploy on OCP: https://releases.ansible.com/ansible-tower/setup_openshift/ 
Once the archive is unpacked, the only file to be modified is the "inventory file" on a host with Ansible installed.

Here is a quick and dirty inventory file for minimal understanding of the options:
```
localhost ansible_connection=local ansible_python_interpreter="/usr/bin/env python3"
[all:vars]

# This will create or update a default admin (superuser) account in Tower
admin_user=admin
admin_password='mytowerpassword'              # <== to be modified
secret_key='mytowersecret' # to modify in production and keep safely

pg_username='admin'
pg_password='mypgpassword'
pg_database='tower'
pg_port=5432
pg_sslmode='prefer'  

openshift_host=https://api_url_ocp:30569      # <== to be modified
openshift_project=ansibletower                # <== to be modified
openshift_user=myUserOnOCP                    # <== to be modified
openshift_token=api_token                     # <== to be modified
# openshift_skip_tls_verify=false
openshift_pg_emptydir=true                    # <== to only use in demo
```

With the above "inventory" file, run the installer as such:
```
./setup_openshift.sh
```


