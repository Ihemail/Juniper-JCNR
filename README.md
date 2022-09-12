# Juniper JCNR - VMM - Single Node Deployment

# Juniper JCNR(22.2-20) Deployment with Jdeployer K8s Infra and Minikube via_Shell

Note: Single/Multiple Shell Scripts are customized to run from RHEL console(preferably) bash shell in VMM. 

1. Load single Node JCNR topology file in vmm pod and start the topology:
  ```ruby
  podxx-vmm:~ $ vmm config vmm-jcnr-1.cfg -g vmm-default
  podxx-vmm:~ $ vmm start
  ```
2. All the necessary scripts and yaml files are avaiable in 'jcnr-pods-all.tgz' 
3. Transfer 'jcnr-pods-all.tgz' to 'vm_rhel84_1' server node 
  ```ruby
  [xxxx@q-pod13-vmm ~]$ mkdir homes
  podxx-vmm:~> vmm ip
  vm_rhel84_1 10.53.56.46
  vmx_1 10.53.32.99
  vmx_1_MPC0 10.53.46.12
  vm_openwrt_1 10.53.45.35
  podxx-vmm:~> scp jcnr-pods-all.tgz root@10.53.56.46:/root/

  ```
4. If you need to change the server hostname then modofy the '/etc/hostname' file and reboot the server via conosle:
  login: root/contrail123
  ```ruby
  [root@rhel84 ~]# cat /etc/hostname
  rhel85
  [root@rhel84 ~]# reboot
  ```
3. Login to 'vm_rhel84_1' via console/serial & Create a install script 'install.sh' at Root directory:
  ```ruby
  [root@rhel85 ~]# vi start.sh
  #!/bin/bash

  tar -xvf jcnr-pods-all.tgz
  mv jcnr-pods-all/k8s.io.sh ~/
  mv jcnr-pods-all/mini-2.sh ~/
  sh mini-2.sh
  echo '\============  Minikube Install Complete :)  ============\'
  sh k8s.io.sh
  echo '\============  K8s Infra Install Complete :)  ============\'
  
  [root@rhel85 ~]#
  ```
4. Next start the install.sh script to start the K8s Infra and JCNR-22.2 deployment on the same RHEL server:

  ```ruby
  [root@rhel85 ~]# sh install.sh
  jcnr-pods-all/
  jcnr-pods-all/external_config.yaml
  jcnr-pods-all/JCNR-22.2/
  jcnr-pods-all/JCNR-22.2/helm_charts/
  . . . .
  ```
  Note: Once you start the execution of 'install.sh' script it will continue for 30min-1hr with many jdeployer & ansible logs on the console. You can monitor those logs for any issues. 
  
5. Successfull Installation ending will look like this:
  
  ```ruby
  MacOS:ansible xxxx$ ansible-playbook vmm-conf.yaml
  
  PLAY RECAP *****************************************************************************************************
  localhost                  : ok=15   changed=13   unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
  ```


6. Verify the JCNR(vRouter + cRPD) pods are running properly:

  ```ruby
  MacOS:ansible xxxx$ ansible-playbook vmm-base.yaml
  ```
  
7. Close all Terminal app window once work is complete.
