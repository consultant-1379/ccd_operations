# Introduction

## CCD pipeline playbooks and Ansible Vault

Most playbooks require access to data encrypted with Ansible Vault. The Vault
password must be passed in to Ansible or the playbook will fail. There are two
ways to do this.

Ansible will prompt for the password in an interactive prompt:

    ansible-playbook --ask-vault-pass <playbook-filename.yml>

Provide the path to a file containing the password for no prompt:

    ansible-playbook --vault-password-file <path_to_secret> <playbook-filename.yml>

> 📝 The rest of this README uses the interactive prompt in the examples.

When running many playbooks it could be helpful to define an alias:

    alias ap='ansible-playbook --vault-password-file <path_to_secret>'

> 📝 For security reasons, the password file option must not be used when
running playbooks on a shared server.

---------------------------------------
Example of additions to this README.md: 
---------------------------------------
# Description of staging-version-flavor-template.yml
  Finds CCD Flavor and CCD Staging Version in DTT then finds the relevant CCD flavor template file in MinIO and
  updates the current version in the template to the staging version from DTT.
  This playbook is not a stage in the ccd pipeline

## Prequisites for playbook

1. DTT entry exists for <deployment_id> and is fully updated
2. CCD Flavor Template file exists on MinIO

## Run staging-version-flavor-template.yml playbook

    ansible-playbook staging-version-flavor-template.yml --ask-vault-pass -e deployment_id=<deployment_id>

-------------------------------------
