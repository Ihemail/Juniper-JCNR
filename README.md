# Juniper JCNR - VMM - Single Node Deployment

# Juniper JCNR(22.2-20) Deployment with Jdeployer K8s Infra and Minikube via_Shell

Note: Single/Multiple Shell Scripts are customized to run in RHEL console(preferably) bash shell in VMM. 

1. Load single Node JCNR topology file in vmm pod and start the topology:

  ```ruby
  podxx-vmm:~ $ vmm config vmm-jcnr-1.cfg -g vmm-default
  podxx-vmm:~ $ vmm start
  ```

2. All the necessary scripts and yaml files are avaiable in 'jcnr-pods-all.tgz' 

3. Transfer 'jcnr-pods-all.tgz' to 'vm_rhel84_1' server node 

  ```ruby
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

5. Login to 'vm_rhel84_1' via console/serial & Create a install script 'install.sh' at Root directory:

  ```ruby
  [root@rhel85 ~]# vi start.sh
  #!/bin/bash

  tar -xvf jcnr-pods-all.tgz
  mv jcnr-pods-all/k8s.io.sh ~/
  mv jcnr-pods-all/mini-2.sh ~/
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
  2022-09-10 15:12:28,259 p=10687 u=root n=ansible | TASK [create dummy file] *******************************************************
  2022-09-10 15:12:29,317 p=10687 u=root n=ansible | changed: [10.49.123.202]
  2022-09-10 15:12:29,350 p=10687 u=root n=ansible | PLAY RECAP *********************************************************************
  2022-09-10 15:12:29,351 p=10687 u=root n=ansible | 10.49.123.202              : ok=26   changed=9    unreachable=0    failed=0    skipped=33   rescued=0    ignored=0
  2022-09-10 15:12:29,351 p=10687 u=root n=ansible | 127.0.0.1                  : ok=16   changed=8    unreachable=0    failed=0    skipped=26   rescued=0    ignored=0
  INFO     provisioner executed successfully....
  Helm v3.9.4 is already latest
  ***  installing helm operator ************************
  . . .
  
  NAME: jcnr
  LAST DEPLOYED: Sat Sep 10 15:28:42 2022
  NAMESPACE: default
  STATUS: deployed
  REVISION: 1
  TEST SUITE: None
  NAMESPACE         NAME                                     READY   STATUS              RESTARTS      AGE
  contrail-deploy   contrail-k8s-deployer-7b5dd699b9-7x7j6   0/1     Init:0/1            0             10s
  contrail          apply-contrail--1-8mknq                  1/1     Running             0             10s
  kube-system       calico-node-7dsps                        1/1     Running             0             19m
  kube-system       coredns-68ddd5bcc7-kx5zp                 1/1     Running             0             18m
  kube-system       dns-autoscaler-6b7fcc87b9-gxfgn          1/1     Running             0             18m
  kube-system       kube-apiserver-rhel84                    1/1     Running             1 (17m ago)   20m
  kube-system       kube-controller-manager-rhel84           1/1     Running             2 (17m ago)   20m
  kube-system       kube-crpd-worker-ds-vslth                0/1     Init:1/2            0             10s
  kube-system       kube-multus-ds-amd64-r94vn               1/1     Running             0             18m
  kube-system       kube-proxy-7bpkr                         1/1     Running             0             19m
  kube-system       kube-scheduler-rhel84                    1/1     Running             2 (17m ago)   20m
  kube-system       nodelocaldns-vxlcg                       1/1     Running             0             18m
  kube-system       syslog-ng-54749b7b77-d6lfh               0/1     ContainerCreating   0             10s
  \============  K8s Infra Install Complete :)  ============\
  [root@rhel84 ~]#
  [root@rhel84 ~]#  
  ```


6. Verify the JCNR(vRouter + cRPD) pods are running properly:

  ```ruby
  [root@rhel84 ~]# kubectl get pods -A
  NAMESPACE         NAME                                     READY   STATUS              RESTARTS      AGE
  contrail-deploy   contrail-k8s-deployer-7b5dd699b9-7x7j6   0/1     Init:0/1            0             10s
  contrail          apply-contrail--1-8mknq                  1/1     Running             0             10s
  kube-system       calico-node-7dsps                        1/1     Running             0             19m
  kube-system       coredns-68ddd5bcc7-kx5zp                 1/1     Running             0             18m
  kube-system       dns-autoscaler-6b7fcc87b9-gxfgn          1/1     Running             0             18m
  kube-system       kube-apiserver-rhel84                    1/1     Running             1 (17m ago)   20m
  kube-system       kube-controller-manager-rhel84           1/1     Running             2 (17m ago)   20m
  kube-system       kube-crpd-worker-ds-vslth                0/1     Init:1/2            0             10s
  kube-system       kube-multus-ds-amd64-r94vn               1/1     Running             0             18m
  kube-system       kube-proxy-7bpkr                         1/1     Running             0             19m
  kube-system       kube-scheduler-rhel84                    1/1     Running             2 (17m ago)   20m
  kube-system       nodelocaldns-vxlcg                       1/1     Running             0             18m
  kube-system       syslog-ng-54749b7b77-d6lfh               0/1     ContainerCreating   0             10s
  \============  K8s Infra Install Complete :)  ============\
  [root@rhel84 ~]#
  [root@rhel84 ~]#
  ```
  
7. Close all Terminal app window once work is complete.
