# Local Installation

      ==> node-3:     Duration: 4.911 ms
      ==> node-3:      Changes:
      ==> node-3:               ----------
      ==> node-3:               diff:
      ==> node-3:                   New file
      ==> node-3:               mode:
      ==> node-3:                   0755
      ==> node-3:
      ==> node-3: Summary
      ==> node-3: -------------
      ==> node-3: Succeeded: 40 (changed=36)
      ==> node-3: Failed:     0
      ==> node-3: -------------
      ==> node-3: Total states run:     40
      ==> node-3: Last metadata expiration check: 0:02:49 ago on Sat Jun  3 00:28:06 2017.
      ==> node-3: Package socat-1.7.2.4-5.fc23.x86_64 is already installed, skipping.
      ==> node-3: Dependencies resolved.
      ==> node-3: ================================================================================
      ==> node-3:  Package          Arch            Version                 Repository       Size
      ==> node-3: ================================================================================
      ==> node-3: Installing:
      ==> node-3:  ethtool          x86_64          2:4.0-2.fc23            fedora          125 k
      ==> node-3:
      ==> node-3: Transaction Summary
      ==> node-3: ================================================================================
      ==> node-3: Install  1 Package
      ==> node-3:
      ==> node-3: Total download size: 125 k
      ==> node-3: Installed size: 351 k
      ==> node-3: Downloading Packages:
      ethtool-4.0-2.fc23.x86_64.rpm                    66 kB/s | 125 kB     00:01       --:-- ETA
      ==> node-3: --------------------------------------------------------------------------------
      ==> node-3: Total                                            32 kB/s | 125 kB     00:03
      ==> node-3: Running transaction check
      ==> node-3: Transaction check succeeded.
      ==> node-3: Running transaction test
      ==> node-3: Transaction test succeeded.
      ==> node-3: Running transaction
        Installing  : ethtool-2:4.0-2.fc23.x86_64                                 1/1
        Verifying   : ethtool-2:4.0-2.fc23.x86_64                                 1/1
      ==> node-3:
      ==> node-3: Installed:
      ==> node-3:   ethtool.x86_64 2:4.0-2.fc23
      ==> node-3:
      ==> node-3: Complete!
      ==> node-3: Last metadata expiration check: 0:02:55 ago on Sat Jun  3 00:28:06 2017.
      ==> node-3: Dependencies resolved.
      ==> node-3: Nothing to do.
      ==> node-3: Complete!
      Cluster "vagrant" set.
      User "vagrant" set.
      Context "vagrant" set.
      Switched to context "vagrant".
      User "vagrant-basic-auth" set.
      Wrote config for vagrant to /Users/pkrish00c/.kube/config
      Each machine instance has been created/updated.
        Now waiting for the Salt provisioning process to complete on each machine.
        This can take some time based on your network, disk, and cpu speed.
        It is possible for an error to occur during Salt provision of cluster and this could loop forever.
      Validating master
      Validating node-1
      Validating node-2
      Validating node-3

      Waiting for each node to be registered with cloud provider
      Flag --api-version has been deprecated, flag is no longer respected and will be deleted in the next release
      Flag --api-version has been deprecated, flag is no longer respected and will be deleted in the next release
      Flag --api-version has been deprecated, flag is no longer respected and will be deleted in the next release
      Validating we can run kubectl commands.
      No resources found.
      Connection to 127.0.0.1 closed.

      Kubernetes cluster is running.

      The master is running at:

        https://10.245.1.2

      Administer and visualize its resources using Cockpit:

        https://10.245.1.2:9090

      For more information on Cockpit, visit http://cockpit-project.org

      The user name and password to use is located in /Users/pkrish00c/.kube/config

      ... calling validate-cluster
      Found 3 node(s).
      NAME                STATUS    AGE
      kubernetes-node-1   Ready     28m
      kubernetes-node-2   Ready     15m
      kubernetes-node-3   Ready     57s
      Validate output:
      NAME                 STATUS    MESSAGE              ERROR
      scheduler            Healthy   ok
      controller-manager   Healthy   ok
      etcd-0               Healthy   {"health": "true"}
      etcd-1               Healthy   {"health": "true"}
      Cluster validation succeeded
      Done, listing cluster services:

      Kubernetes master is running at https://10.245.1.2
      Heapster is running at https://10.245.1.2/api/v1/proxy/namespaces/kube-system/services/heapster
      KubeDNS is running at https://10.245.1.2/api/v1/proxy/namespaces/kube-system/services/kube-dns
      kubernetes-dashboard is running at https://10.245.1.2/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard
      Grafana is running at https://10.245.1.2/api/v1/proxy/namespaces/kube-system/services/monitoring-grafana
      InfluxDB is running at https://10.245.1.2/api/v1/proxy/namespaces/kube-system/services/monitoring-influxdb



      To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

      Kubernetes binaries at /Users/pkrish00c/statefulsets/kubernetes/cluster/
      You may want to add this directory to your PATH in $HOME/.profile
      Installation successful!
