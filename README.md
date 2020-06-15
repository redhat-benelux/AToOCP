# Ansible Tower on OpenShift Container Platform

This repository is based on the Ansible roles provided with the Red Hat Ansible Tower installer for OpenShift
available at this [link](https://releases.ansible.com/ansible-tower/setup_openshift/).

The following modification have been done to the official roles:

- make them independent of the "setup_openshift.sh" script which allows a direct usage without downloading the installer or included in a pipeline for redeployment or included int Ansible Tower with a runner to do the backup and restore.
- remove all the non OpenShift context to focus and improve deployment speed
- add the missing creation of the PVC when deploying the database within OpenShift meaning that there is no need to do any extra manual steps prior to the deployment 

# Usage

## Installation
First do a git clone of this repo:
```
git clone https://github.com/redhat-benelux/tower-deploy_on-ocp.git
```

Then modify the "inventory" file to match your needs. This file has been slighty reorganized for the sake of clarity and the comments are self-explanatory: 
```
localhost ansible_connection=local 
#ansible_python_interpreter="/usr/bin/env python3"

[all:vars]

# Ansible Tower Settings
# ======================
# admin_user and admin_password: this will create or update a default admin (superuser) account in Tower
# secret_key: It's *very* important that this stay the same between upgrades or you will lose the ability to decrypt your credentials
admin_user=admin
admin_password='temp password'
secret_key='secret_key'

# Database Settings
# =================
# Three possible setup:
# 1. Bring Your Own (BYO) DB; fill in pg_hostname and pg_* variables for Tower connection purposes
# 2. Demo deploy DB on OCP; don't fill in pg_hostname, uncomment openshift_pg_emptydir, fill pg_* 
#    for Tower connection purposes
# 3. Production deploy with DB on OCP and auto PVC creation; fill pg_* for Tower connection purposes,
#    let openshift_pg_emptydir commented and fill openshift_pg_pvc_name with the desired PVC 
# Note: if PVC already exist, it is assumed that it is 


#pg_hostname=
#openshift_pg_pvc_name='mypvcname'
#openshift_pg_emptydir=true
pg_username='admin'
pg_password='pg_pasword'
pg_database='tower'
pg_port=5432
pg_sslmode='prefer'  # set to 'verify-full' for client-side enforced SSL

# Deploy into Openshift
# =====================

openshift_host=https://ocp_api_endpoint:port
# openshift_skip_tls_verify=false
openshift_project=project_namespace
openshift_user=user_with_cluster_admin
openshift_token=api_token
```

With the "inventory" file being customized to your environment, run the following command to execute the installation role: 
```
ansible-playbook -i inventory install.yml 
```

## Backup
With the same "inventory" file being customized to your environment, run the following command to execute the backup role:
```
ansible-playbook -i inventory backup.yml
``` 
This will create 2 files each time:

- one backup file with the timestamp of the backup
- one backup file called "latest" replacing the previous "latest" file

## Restore
With the same "inventory" file being customized to your environment, run the following command to execute the restore role:
```
ansible-playbook -i inventory restore.yml
``` 
By default, the restore role will pickup the "latest" available backup.
