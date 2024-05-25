# F5-Ansible-Notes
F5 BIG IP Ansible Notes


******
BIG-IP Facts Playbook
******


---
- name: GRAB F5 FACTS
  hosts: f5
  connection: local
  gather_facts: no

  tasks:

    - name: COLLECT BIG-IP FACTS
      bigip_device_info:
        gather_subset:
          - system-info
        provider:
          server: "{{private_ip}}"
          user: "{{ansible_user}}"
          password: "{{ansible_ssh_pass}}"
          server_port: 8443
          validate_certs: no
      register: device_facts

    - name: DISPLAY COMPLETE BIG-IP SYSTEM INFORMATION
      debug:
        var: device_facts

    - name: DISPLAY ONLY THE MAC ADDRESS
      debug:
        var: device_facts['system_info']['base_mac_address']

    - name: DISPLAY ONLY THE VERSION
      debug:
        var: device_facts['system_info']['product_version']

******
BIG-IP Nodes Playbook
******

---
- name: BIG-IP SETUP
  hosts: lb
  connection: local
  gather_facts: false
  
  tasks:

  - name: CREATE NODES
    bigip_node:
      provider:
        server: "{{private_ip}}"
        user: "{{ansible_user}}"
        password: "{{ansible_ssh_pass}}"
        server_port: 8443
        validate_certs: no
      host: "{{hostvars[item].ansible_host}}"
      name: "{{hostvars[item].inventory_hostname}}"
    loop: "{{ groups['webservers'] }}"


******
BIG-IP Pool Playbook
******

---
- name: BIG-IP SETUP
  hosts: lb
  connection: local
  gather_facts: false

  tasks:

  - name: CREATE POOL
    bigip_pool:
      provider:
        server: "{{private_ip}}"
        user: "{{ansible_user}}"
        password: "{{ansible_ssh_pass}}"
        server_port: 8443
        validate_certs: no
      name: "http_pool"
      lb_method: "round-robin"
      monitors: "/Common/http"
      monitor_type: "and_list"


******
BIG-IP Pool Members Playbook
******



---
- name: BIG-IP SETUP
  hosts: lb
  connection: local
  gather_facts: false

  tasks:

  - name: ADD POOL MEMBERS
    bigip_pool_member:
      provider:
        server: "{{private_ip}}"
        user: "{{ansible_user}}"
        password: "{{ansible_ssh_pass}}"
        server_port: 8443
        validate_certs: no
      state: "present"
      name: "{{hostvars[item].inventory_hostname}}"
      host: "{{hostvars[item].ansible_host}}"
      port: "80"
      pool: "http_pool"
    loop: "{{ groups['webservers'] }}"


******
BIG-IP Virtual Server Playbook
******


---
- name: BIG-IP SETUP
  hosts: lb
  connection: local
  gather_facts: false

  tasks:

  - name: ADD VIRTUAL SERVER
    bigip_virtual_server:
      provider:
        server: "{{private_ip}}"
        user: "{{ansible_user}}"
        password: "{{ansible_ssh_pass}}"
        server_port: 8443
        validate_certs: no
      name: "vip"
      destination: "{{destination_ip}}"
      port: "443"
      enabled_vlans: "all"
      all_profiles: ['http','clientssl','oneconnect']
      pool: "http_pool"
      snat: "Automap"


******
BIG-IP Save Configuration Playbook
******


---
- name: BIG-IP SETUP
  hosts: lb
  connection: local
  gather_facts: false

  tasks:

  - name: SAVE RUNNING CONFIG ON BIG-IP
    bigip_config:
      provider:
        server: "{{private_ip}}"
        user: "{{ansible_user}}"
        password: "{{ansible_ssh_pass}}"
        server_port: 8443
        validate_certs: no
      save: yes
