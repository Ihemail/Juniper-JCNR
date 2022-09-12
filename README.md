# Juniper-JCNR - VMM - Single node Deployment

# Juniper JCNR(22.2-20) Deployment with Jdeployer K8s Infra and Minikube via_Shell

Note: Single/Multiple Shell Scripts are customized to run from RHEL console(preferably) bash shell in VMM. 

1. Copy SSH Key to any Quincy Pod:
  ```ruby
  MacOS:~ $ ssh-copy-id -i ~/.ssh/id_rsa.pub userid@q-pod13-vmm.englab.juniper.net
  ```
2. Login to any vmm Quincy Pod, and verify passwordless ssh login.
3. Create a folder named 'homes' in your vmm Quincy Pod Home directory:
  ```ruby
  [xxxx@q-pod13-vmm ~]$ mkdir homes
  ```
4. Open test editor and modify the `vmm-conf.yaml` file with your own userid:

  ```ruby
  5    vars:
  6      - vmm_ansible_conf_sh: "{{ lookup('file', 'scripts/vmm_ansible_conf.sh').splitlines() }}"
  7      - vmm_commands_1_sh: "{{ lookup('file', 'scripts/vmm_commands_1.sh').splitlines() }}"
  8      - vmm_list:
  9          p1: userid@q-pod05-vmm.englab.juniper.net
 10          p2: userid@q-pod08-vmm.englab.juniper.net
 11          p3: userid@q-pod13-vmm.englab.juniper.net
  ```
  
5. Exit from VMM Pod and execute the ansible-playbook:
  
  ```ruby
  MacOS:ansible xxxx$ ansible-playbook vmm-conf.yaml
  
  PLAY RECAP *****************************************************************************************************
  localhost                  : ok=15   changed=13   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
  ```


6. Similar login only Ansible Script for Server console & ssh to vQFX/vMX nodes:

  ```ruby
  MacOS:ansible xxxx$ ansible-playbook vmm-base.yaml
  ```
  
7. Close all Terminal app window once work is complete.
