Proxmox Template
=========

Ansible task sequence to create Proxmox Virtual Machine template from a base image.
Features:
  - cloud-init user-data file deployment.
  - Add/update cloud-init drive.

Requirements
------------

- Full privilege API Credentials to a proxmox host
- Privileges to install package requirements on proxmox host, see vars/main.yml.
- Privileges to install pip package requirements on proxmox host, see vars/main.yml.

Role Variables
--------------

See defaults/main.yml


Playbook - proxmox_vm_templater_meta_host: false
----------------

    - hosts: proxmox_hosts
      roles:
        - role: coreyramirezgomez.proxmox_vm_templater
          proxmox_vm_templater_meta_host: false
          ### API vars; required (with defaults)
          proxmox_vm_templater_target_host: "{{ inventory_hostname }}"
          proxmox_vm_templater_target_node: "{{ inventory_hostname_short }}"
          ### API vars; required
          proxmox_vm_templater_root_user_password: "PASSWORD"
          proxmox_vm_templater_api_token_id: "ansible"
          proxmox_vm_templater_api_token_secret: "MUCH_SECRET_WOW"
          proxmox_vm_templater_api_user: "root@pam"
          ### Base image handling vars; required (with defaults)
          proxmox_vm_templater_cleanup: true
          proxmox_vm_templater_base_image_name: "jammy-server-cloudimg-amd64.img"
          proxmox_vm_templater_base_image_url: "https://cloud-images.ubuntu.com/jammy/current/{{ proxmox_vm_templater_base_image_name }}"
          ### cloud-init handling vars; required (with defaults)
          proxmox_vm_templater_configure_ci: true 
          proxmox_vm_templater_ci_ipconfig:
            ipconfig0: "ip=dhcp,ip6=dhcp"
          proxmox_vm_templater_ci_user_data_file_path: "/var/lib/vz/snippets/"
          proxmox_vm_templater_ci_user_data_file_name: "{{ inventory_hostname_short }}-user-data"
          proxmox_vm_templater_ci_user_data_volume: "local-lvm"
          proxmox_vm_templater_ci_user_data_src_file: "files/user-data.txt"
          ### Template VM handling vars; required (with defaults)
          proxmox_vm_templater_vm_cores: 2
          proxmox_vm_templater_vm_sockets: 1
          proxmox_vm_templater_vm_memory: 2048
          proxmox_vm_templater_vm_format: "raw"
          proxmox_vm_templater_vm_network:
            net0: "virtio,bridge=vmbr0"
          ### Template VM handling vars; required
          proxmox_vm_templater_vm_hostname: "template"
          proxmox_vm_templater_vm_id: 999
          proxmox_vm_templater_vm_storage: "local"

Playbook - proxmox_vm_templater_meta_host: true
----------------
  1. In your inventory, define the new VM:

    ...
    [proxmox_templates]
    template-name
    ...

  2. In your inventory, create a host_vars for the new VM:

    inventories/default
    |-- host_vars
    |   |-- template-name
    |   |   |-- coreyramirezgomez.proxmox_vm_templater.yml

  3. Place all the configuration for the new template into the file coreyramirezgomez.proxmox_vm_templater.yml:

    ---
    proxmox_vm_templater_meta_host: false
    ### API vars; required (with defaults)
    proxmox_vm_templater_target_host: "{{ inventory_hostname }}"
    proxmox_vm_templater_target_node: "{{ inventory_hostname_short }}"
    ### API vars; required
    proxmox_vm_templater_root_user_password: "PASSWORD"
    proxmox_vm_templater_api_token_id: "ansible"
    proxmox_vm_templater_api_token_secret: "MUCH_SECRET_WOW"
    proxmox_vm_templater_api_user: "root@pam"

    ### Base image handling vars; required (with defaults)
    proxmox_vm_templater_cleanup: true
    proxmox_vm_templater_base_image_name: "jammy-server-cloudimg-amd64.img"
    proxmox_vm_templater_base_image_url: "https://cloud-images.ubuntu.com/jammy/current/{{ proxmox_vm_templater_base_image_name }}"
    ### cloud-init handling vars; required (with defaults)
    proxmox_vm_templater_configure_ci: true 
    proxmox_vm_templater_ci_ipconfig:
      ipconfig0: "ip=dhcp,ip6=dhcp"
    proxmox_vm_templater_ci_user_data_file_path: "/var/lib/vz/snippets/"
    proxmox_vm_templater_ci_user_data_file_name: "{{ inventory_hostname_short }}-user-data"
    proxmox_vm_templater_ci_user_data_volume: "local-lvm"
    proxmox_vm_templater_ci_user_data_src_file: "files/user-data.txt"
    ### Template VM handling vars; required (with defaults)
    proxmox_vm_templater_vm_cores: 2
    proxmox_vm_templater_vm_sockets: 1
    proxmox_vm_templater_vm_memory: 2048
    proxmox_vm_templater_vm_format: "raw"
    proxmox_vm_templater_vm_network:
      net0: "virtio,bridge=vmbr0"
    ### Template VM handling vars; required
    proxmox_vm_templater_vm_hostname: "template"
    proxmox_vm_templater_vm_id: 999
    proxmox_vm_templater_vm_storage: "local"

  4. Define a playbook to deploy with the role:

    - hosts: proxmox_templates
      become: true
      gather_facts: false # This is required otherwise ansible will try to establish connection to the VM, but we haven't even created it yet. So skip over it.
      roles:
        - role: coreyramirezgomez.proxmox_vm_templater
