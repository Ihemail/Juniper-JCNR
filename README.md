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
   [login: root/contrail123]

  ```ruby
  [root@rhel84 ~]# cat /etc/hostname
  rhel85
  [root@rhel84 ~]# reboot
  ```

5. Login to 'vm_rhel84_1' via console/serial & Create a install script 'install.sh' at Root directory:

  ```ruby
  [root@rhel85 ~]# vi install.sh
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

6. Next start the install.sh script to start the K8s Infra and JCNR-22.2 deployment on the same RHEL server:

  ```ruby
  [root@rhel85 ~]# sh install.sh
  jcnr-pods-all/
  jcnr-pods-all/external_config.yaml
  jcnr-pods-all/JCNR-22.2/
  jcnr-pods-all/JCNR-22.2/helm_charts/
  . . . .
  ```
  Note: Once you start the execution of 'install.sh' script it will continue for 30min-1hr with many jdeployer & ansible logs on the console. You can monitor those logs for any issues. 
  
7. Upon Successful Installation ending will look like this:
  
  ```ruby
  2022-09-12 11:53:35,646 p=19035 u=root n=ansible | TASK [execute on all remote hosts] *********************************************
  2022-09-12 11:53:35,734 p=19035 u=root n=ansible | included: /root/juimp/jdeployer/config_manager/ansible/jdeployer/roles/remote_execution5
  2022-09-12 11:53:35,747 p=19035 u=root n=ansible | TASK [create dummy file] *******************************************************
  2022-09-12 11:53:36,935 p=19035 u=root n=ansible | changed: [10.53.56.46]
  2022-09-12 11:53:36,980 p=19035 u=root n=ansible | PLAY RECAP *********************************************************************
  2022-09-12 11:53:36,980 p=19035 u=root n=ansible | 10.53.56.46                : ok=26   changed=9    unreachable=0    failed=0    skipped=
  2022-09-12 11:53:36,981 p=19035 u=root n=ansible | 127.0.0.1                  : ok=16   changed=8    unreachable=0    failed=0    skipped=
  INFO     provisioner executed successfully....
  Helm v3.9.4 is already latest
  ***  installing helm operator ************************

  --2022-09-12 11:53:37--  https://raw.githubusercontent.com/fluxcd/helm-operator/1.2.0/deploy/crds.yaml
  Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 2606:50c0:8000::154, 2606:50c0:8001::154, 2606:50c0:8002::154, ...
  Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|2606:50c0:8002::154|:443... failed: Connection timed out.
  Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|2606:50c0:8003::154|:443...
  Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.109.133|:443... connected.
  HTTP request sent, awaiting response... 200 OK
  Length: 17919 (17K) [text/plain]
  Saving to: ‘crds.yaml’

       0K .......... .......                                    100% 18.7M=0.001s

  2022-09-12 12:02:23 (18.7 MB/s) - ‘crds.yaml’ saved [17919/17919]
  . . .
  NAMESPACE     NAME                              READY   STATUS    RESTARTS      AGE
  kube-system   calico-node-vxsmt                 1/1     Running   0             16m
  kube-system   coredns-68ddd5bcc7-jcbx7          1/1     Running   0             15m
  kube-system   dns-autoscaler-6b7fcc87b9-ffb8m   1/1     Running   0             15m
  kube-system   kube-apiserver-rhel85             1/1     Running   1 (14m ago)   17m
  kube-system   kube-controller-manager-rhel85    1/1     Running   2 (14m ago)   17m
  kube-system   kube-multus-ds-amd64-wwdd2        1/1     Running   0             16m
  kube-system   kube-proxy-kzd78                  1/1     Running   0             16m
  kube-system   kube-scheduler-rhel85             1/1     Running   2 (14m ago)   17m
  kube-system   nodelocaldns-m2mwj                1/1     Running   0             15m
  # =====  JCNR - 22.2 (vRouter:22.2-20 + cRPD:22.2R1-S1.5) : Initialization Script  ===== #
  secret/jcnr-secrets created
  NAME: jcnr
  LAST DEPLOYED: Tue Sep 13 02:01:09 2022
  NAMESPACE: default
  STATUS: deployed
  REVISION: 1
  TEST SUITE: None
  NAMESPACE         NAME                                     READY   STATUS    RESTARTS      AGE
  contrail-deploy   contrail-k8s-deployer-7b5dd699b9-sqzbx   1/1     Running   0             2m
  contrail          contrail-vrouter-masters-2dbsb           3/3     Running   0             101s
  kube-system       calico-node-vxsmt                        1/1     Running   0             18m
  kube-system       coredns-68ddd5bcc7-jcbx7                 1/1     Running   0             17m
  kube-system       dns-autoscaler-6b7fcc87b9-ffb8m          1/1     Running   0             17m
  kube-system       kube-apiserver-rhel85                    1/1     Running   1 (16m ago)   19m
  kube-system       kube-controller-manager-rhel85           1/1     Running   2 (16m ago)   19m
  kube-system       kube-crpd-worker-ds-lmxhq                1/1     Running   0             2m
  kube-system       kube-multus-ds-amd64-wwdd2               1/1     Running   0             18m
  kube-system       kube-proxy-kzd78                         1/1     Running   0             18m
  kube-system       kube-scheduler-rhel85                    1/1     Running   2 (16m ago)   19m
  kube-system       nodelocaldns-m2mwj                       1/1     Running   0             17m
  kube-system       syslog-ng-54749b7b77-c4c9f               1/1     Running   0             2m
  \========  K8s Infra + JCNR 22.2 Installation Complete :)  ========\
  \============  K8s Infra Install Complete :)  ============\
  [root@rhel85 ~]#
  [root@rhel85 ~]#  
  ```

8. Verify the JCNR(vRouter + cRPD) pods are running properly:

  ```ruby
  [root@rhel85 ~]# kubectl get pods -A
  NAMESPACE         NAME                                     READY   STATUS    RESTARTS        AGE
  contrail-deploy   contrail-k8s-deployer-7b5dd699b9-sqzbx   1/1     Running   0               16m
  contrail          contrail-vrouter-masters-2dbsb           3/3     Running   0               16m
  kube-system       calico-node-vxsmt                        1/1     Running   0               22m
  kube-system       coredns-68ddd5bcc7-jcbx7                 1/1     Running   0               21m
  kube-system       dns-autoscaler-6b7fcc87b9-ffb8m          1/1     Running   0               21m
  kube-system       kube-apiserver-rhel85                    1/1     Running   1 (21m ago)     25m
  kube-system       kube-controller-manager-rhel85           1/1     Running   2 (21m ago)     25m
  kube-system       kube-crpd-worker-ds-lmxhq                1/1     Running   0               16m
  kube-system       kube-multus-ds-amd64-wwdd2               1/1     Running   0               22m
  kube-system       kube-proxy-kzd78                         1/1     Running   0               22m
  kube-system       kube-scheduler-rhel85                    1/1     Running   2 (21m ago)     24m
  kube-system       nodelocaldns-m2mwj                       1/1     Running   0               21m
  kube-system       syslog-ng-54749b7b77-c4c9f               1/1     Running   0               16m
  [root@rhel85 ~]# 
  [root@rhel85 ~]#
  ```
  
9. Close all Terminal app window once work is complete.
