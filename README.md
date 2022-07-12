Proxmox Template
=========

Task sequence with ansible to create Proxmox Virtual Machine template from a base image (cloud ready image at least) with optional cloud-init drive configuration.

Requirements
------------

- Full privilege API Credentials to a proxmox host
- Privileges to install package requirements on proxmox host, see vars/main.yml.
- Privileges to install pip package requirements on proxmox host, see vars/main.yml.

Role Variables
--------------

Coming soon...for now seed defaults/main.yml

Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: proxmox_hosts
      roles:
        - role: coreyramirezgomez.proxmox_vm_templater
          # Required
          proxmox_template_target_host: proxmox.example.com
          proxmox_template_target_node: proxmox
          proxmox_template_root_user_password: 'password' # Required to create VM
          proxmox_template_api_user: "root@pam"
          proxmox_template_api_token_id: "ansible"
          proxmox_template_api_token_secret: 'token'
          proxmox_template_vm_hostname: template
          proxmox_template_vm_storage: local-lvm
          # Optional - see defaults/main.yml
          proxmox_template_configure_ci: true
          proxmox_template_ci_user: admin
          ...
