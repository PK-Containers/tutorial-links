## Setting up NFS locally

        HQSML-151665:statefulsets pkrish00c$ kubectl get nodes
        NAME                STATUS    AGE
        kubernetes-node-1   Ready     3h
        kubernetes-node-2   Ready     2h
        kubernetes-node-3   Ready     2h

        HQSML-151665:.kube pkrish00c$ cd /Users/pkrish00c/statefulsets/kubernetes/cluster/
        HQSML-151665:cluster pkrish00c$ ls
        OWNERS				centos				gke				kube-util.sh			local				photon-controller		test-smoke.sh
        README.md			common.sh			images				kubeadm.sh			log-dump.sh			rackspace			ubuntu
        addons				gce				juju				kubectl.sh			mesos				restore-from-backup.sh		update-storage-objects.sh
        aws				get-kube-binaries.sh		kube-down.sh			kubemark			openstack-heat			skeleton			vagrant
        azure				get-kube-local.sh		kube-push.sh			lib   				options.md			test-e2e.sh			validate-cluster.sh
        azure-legacy			get-kube.sh			kube-up.sh			libvirt-coreos			ovirt				test-network.sh			vsphere
        HQSML-151665:cluster pkrish00c$ vagrant status
        Current machine states:

        master                    running (virtualbox)
        node-1                    running (virtualbox)
        node-2                    running (virtualbox)
        node-3                    running (virtualbox)

        This environment represents multiple VMs. The VMs are all listed
        above with their current state. For more information about a specific
        VM, run `vagrant status NAME`.
        HQSML-151665:cluster pkrish00c$ vagrant ssh master
        Last login: Sat Jun  3 00:31:44 2017 from 10.0.2.2
        [vagrant@kubernetes-master ~]$ sudo -i
        [root@kubernetes-master ~]# mkdir -p /opt/data
        [root@kubernetes-master ~]# chmod 777 /opt/data
        [root@kubernetes-master ~]# echo "/opt/data 10.245.1.2/24(rw,sync,no_root_squash,no_all_squash)"  >> /etc/exports
        [root@kubernetes-master ~]# systemctl enable --now rpcbind
        [root@kubernetes-master ~]# systemctl enable --now nfs-server
        Created symlink from /etc/systemd/system/multi-user.target.wants/nfs-server.service to /usr/lib/systemd/system/nfs-server.service.
        [root@kubernetes-master ~]# systemctl start rpcbind
        [root@kubernetes-master ~]# systemctl start nfs-server
        [root@kubernetes-master ~]# mkdir -p /opt/data/vol/0
        [root@kubernetes-master ~]# mkdir -p /opt/data/vol/1
        [root@kubernetes-master ~]#
        [root@kubernetes-master ~]# mkdir -p /opt/data/vol/2
        [root@kubernetes-master ~]# mkdir -p /opt/data/content
        [root@kubernetes-master ~]# exit
        logout
        [vagrant@kubernetes-master ~]$ vagrant ssh node-1
        -bash: vagrant: command not found
        [vagrant@kubernetes-master ~]$ exit
        logout
        Connection to 127.0.0.1 closed.
        HQSML-151665:cluster pkrish00c$ vagrant ssh node-1
        Last login: Sat Jun  3 00:31:28 2017 from 10.0.2.2
        [vagrant@kubernetes-node-1 ~]$ sudo -i
        [root@kubernetes-node-1 ~]# systemctl start rpcbind nfs-mountd
        [root@kubernetes-node-1 ~]# systemctl enable rpcbind nfs-mountd
        [root@kubernetes-node-1 ~]# echo "10.245.1.2:/opt/data  /mnt/data  nfs      rw,sync,hard,intr  0    0" >> /etc/fstab
        [root@kubernetes-node-1 ~]# dnf -y install autofs
        Last metadata expiration check: 0:22:23 ago on Sat Jun  3 03:04:00 2017.
        Dependencies resolved.
        ============================================================================================================================================================================================================================================
         Package                                                Arch                                                   Version                                                         Repository                                              Size
        ============================================================================================================================================================================================================================================
        Installing:
         autofs                                                 x86_64                                                 1:5.1.1-3.fc23                                                  fedora                                                 708 k
         hesiod                                                 x86_64                                                 3.2.1-5.fc23                                                    fedora                                                  34 k

        Transaction Summary
        ============================================================================================================================================================================================================================================
        Install  2 Packages

        Total download size: 742 k
        Installed size: 3.5 M
        Downloading Packages:
        (1/2): hesiod-3.2.1-5.fc23.x86_64.rpm                                                                                                                                                                        55 kB/s |  34 kB     00:00
        (2/2): autofs-5.1.1-3.fc23.x86_64.rpm                                                                                                                                                                       337 kB/s | 708 kB     00:02
        --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
        Total                                                                                                                                                                                                       261 kB/s | 742 kB     00:02
        Running transaction check
        Transaction check succeeded.
        Running transaction test
        Transaction test succeeded.
        Running transaction
          Installing  : hesiod-3.2.1-5.fc23.x86_64                                                                                                                                                                                              1/2
          Installing  : autofs-1:5.1.1-3.fc23.x86_64                                                                                                                                                                                            2/2
          Verifying   : autofs-1:5.1.1-3.fc23.x86_64                                                                                                                                                                                            1/2
          Verifying   : hesiod-3.2.1-5.fc23.x86_64                                                                                                                                                                                              2/2

        Installed:
          autofs.x86_64 1:5.1.1-3.fc23                                                                                          hesiod.x86_64 3.2.1-5.fc23

        Complete!
        [root@kubernetes-node-1 ~]# echo "/-    /etc/auto.mount" >> /etc/auto.master
        [root@kubernetes-node-1 ~]# echo "/mnt/data -fstype=nfs,rw  10.245.1.2:/opt/data" >> /etc/auto.mount
        [root@kubernetes-node-1 ~]# systemctl start autofs
        [root@kubernetes-node-1 ~]#
        [root@kubernetes-node-1 ~]# systemctl enable autofs
        Created symlink from /etc/systemd/system/multi-user.target.wants/autofs.service to /usr/lib/systemd/system/autofs.service.
        [root@kubernetes-node-1 ~]# cd /mnt/data
        [root@kubernetes-node-1 data]# exit
        logout
        [vagrant@kubernetes-node-1 ~]$ exit
        logout
        Connection to 127.0.0.1 closed.
        HQSML-151665:cluster pkrish00c$ vagrant ssh node-2
        Last login: Sat Jun  3 00:31:34 2017 from 10.0.2.2
        [vagrant@kubernetes-node-2 ~]$ sudo -i
        [root@kubernetes-node-2 ~]# systemctl start rpcbind nfs-mountd
        [root@kubernetes-node-2 ~]# systemctl enable rpcbind nfs-mountd
        [root@kubernetes-node-2 ~]#
        [root@kubernetes-node-2 ~]# echo "10.245.1.2:/opt/data  /mnt/data  nfs      rw,sync,hard,intr  0    0" >> /etc/fstab
        [root@kubernetes-node-2 ~]# dnf -y install autofs
        Last metadata expiration check: 0:15:06 ago on Sat Jun  3 03:14:25 2017.
        Dependencies resolved.
        ============================================================================================================================================================================================================================================
         Package                                                Arch                                                   Version                                                         Repository                                              Size
        ============================================================================================================================================================================================================================================
        Installing:
         autofs                                                 x86_64                                                 1:5.1.1-3.fc23                                                  fedora                                                 708 k
         hesiod                                                 x86_64                                                 3.2.1-5.fc23                                                    fedora                                                  34 k

        Transaction Summary
        ============================================================================================================================================================================================================================================
        Install  2 Packages

        Total download size: 742 k
        Installed size: 3.5 M
        Downloading Packages:
        (1/2): hesiod-3.2.1-5.fc23.x86_64.rpm                                                                                                                                                                        87 kB/s |  34 kB     00:00
        (2/2): autofs-5.1.1-3.fc23.x86_64.rpm                                                                                                                                                                       368 kB/s | 708 kB     00:01
        --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
        Total                                                                                                                                                                                                       283 kB/s | 742 kB     00:02
        Running transaction check
        Transaction check succeeded.
        Running transaction test
        Transaction test succeeded.
        Running transaction
          Installing  : hesiod-3.2.1-5.fc23.x86_64                                                                                                                                                                                              1/2
          Installing  : autofs-1:5.1.1-3.fc23.x86_64                                                                                                                                                                                            2/2
          Verifying   : autofs-1:5.1.1-3.fc23.x86_64                                                                                                                                                                                            1/2
          Verifying   : hesiod-3.2.1-5.fc23.x86_64                                                                                                                                                                                              2/2

        Installed:
          autofs.x86_64 1:5.1.1-3.fc23                                                                                          hesiod.x86_64 3.2.1-5.fc23

        Complete!
        [root@kubernetes-node-2 ~]# echo "/-    /etc/auto.mount" >> /etc/auto.master
        [root@kubernetes-node-2 ~]# echo "/mnt/data -fstype=nfs,rw  10.245.1.2:/opt/data" >> /etc/auto.mount
        [root@kubernetes-node-2 ~]# systemctl start autofs
        [root@kubernetes-node-2 ~]# systemctl enable autofs
        Created symlink from /etc/systemd/system/multi-user.target.wants/autofs.service to /usr/lib/systemd/system/autofs.service.
        [root@kubernetes-node-2 ~]# exit
        logout
        [vagrant@kubernetes-node-2 ~]$ exit
        logout
        Connection to 127.0.0.1 closed.
        HQSML-151665:cluster pkrish00c$ vagrant ssh node-3
        Last login: Sat Jun  3 00:31:41 2017 from 10.0.2.2
        [vagrant@kubernetes-node-3 ~]$ sudo -i
        [root@kubernetes-node-3 ~]# systemctl start rpcbind nfs-mountd
        [root@kubernetes-node-3 ~]# systemctl enable rpcbind nfs-mountd
        [root@kubernetes-node-3 ~]# echo "10.245.1.2:/opt/data  /mnt/data  nfs      rw,sync,hard,intr  0    0" >> /etc/fstab
        [root@kubernetes-node-3 ~]# dnf -y install autofs
        Last metadata expiration check: 3:03:50 ago on Sat Jun  3 00:28:06 2017.
        Dependencies resolved.
        ============================================================================================================================================================================================================================================
         Package                                                Arch                                                   Version                                                         Repository                                              Size
        ============================================================================================================================================================================================================================================
        Installing:
         autofs                                                 x86_64                                                 1:5.1.1-3.fc23                                                  fedora                                                 708 k
         hesiod                                                 x86_64                                                 3.2.1-5.fc23                                                    fedora                                                  34 k

        Transaction Summary
        ============================================================================================================================================================================================================================================
        Install  2 Packages

        Total download size: 742 k
        Installed size: 3.5 M
        Downloading Packages:
        (1/2): hesiod-3.2.1-5.fc23.x86_64.rpm                                                                                                                                                                       155 kB/s |  34 kB     00:00
        (2/2): autofs-5.1.1-3.fc23.x86_64.rpm                                                                                                                                                                       411 kB/s | 708 kB     00:01
        --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
        Total                                                                                                                                                                                                       363 kB/s | 742 kB     00:02
        Running transaction check
        Transaction check succeeded.
        Running transaction test
        Transaction test succeeded.
        Running transaction
          Installing  : hesiod-3.2.1-5.fc23.x86_64                                                                                                                                                                                              1/2
          Installing  : autofs-1:5.1.1-3.fc23.x86_64                                                                                                                                                                                            2/2
          Verifying   : autofs-1:5.1.1-3.fc23.x86_64                                                                                                                                                                                            1/2
          Verifying   : hesiod-3.2.1-5.fc23.x86_64                                                                                                                                                                                              2/2

        Installed:
          autofs.x86_64 1:5.1.1-3.fc23                                                                                          hesiod.x86_64 3.2.1-5.fc23

        Complete!
        [root@kubernetes-node-3 ~]# echo "/-    /etc/auto.mount" >> /etc/auto.master
        [root@kubernetes-node-3 ~]# echo "/mnt/data -fstype=nfs,rw  10.245.1.2:/opt/data" >> /etc/auto.mount
        [root@kubernetes-node-3 ~]# systemctl start autofs
        [root@kubernetes-node-3 ~]# systemctl enable autofs
        Created symlink from /etc/systemd/system/multi-user.target.wants/autofs.service to /usr/lib/systemd/system/autofs.service.
        [root@kubernetes-node-3 ~]# cd /mnt/data
        [root@kubernetes-node-3 data]# exit
        logout
        [vagrant@kubernetes-node-3 ~]$ exit
        logout
        Connection to 127.0.0.1 closed.

## Setting up etcd, mysqldb

        HQSML-151665:cluster pkrish00c$ git clone https://github.com/janakiramm/wp-statefulset.git
        Cloning into 'wp-statefulset'...
        remote: Counting objects: 23, done.
        remote: Total 23 (delta 0), reused 0 (delta 0), pack-reused 23
        Unpacking objects: 100% (23/23), done.
        HQSML-151665:cluster pkrish00c$ ls
        OWNERS				common.sh			juju				kubemark			options.md			test-network.sh			wp-statefulset
        README.md			gce				kube-down.sh			lib   				ovirt				test-smoke.sh
        addons				get-kube-binaries.sh		kube-push.sh			libvirt-coreos			photon-controller		ubuntu
        aws				get-kube-local.sh		kube-up.sh			local 				rackspace			update-storage-objects.sh
        azure				get-kube.sh			kube-util.sh			log-dump.sh			restore-from-backup.sh		vagrant
        azure-legacy			gke				kubeadm.sh			mesos 				skeleton			validate-cluster.sh
        centos				images				kubectl.sh			openstack-heat			test-e2e.sh			vsphere
        HQSML-151665:cluster pkrish00c$ kubectl version
        Client Version: version.Info{Major:"1", Minor:"5", GitVersion:"v1.5.2", GitCommit:"08e099554f3c31f6e6f07b448ab3ed78d0520507", GitTreeState:"clean", BuildDate:"2017-01-12T04:57:25Z", GoVersion:"go1.7.4", Compiler:"gc", Platform:"darwin/amd64"}
        Server Version: version.Info{Major:"1", Minor:"5", GitVersion:"v1.5.7", GitCommit:"8eb75a5810cba92ccad845ca360cf924f2385881", GitTreeState:"clean", BuildDate:"2017-04-27T09:42:05Z", GoVersion:"go1.7.5", Compiler:"gc", Platform:"linux/amd64"}
        HQSML-151665:cluster pkrish00c$ vagrant ssh master
        Last login: Sat Jun  3 03:19:24 2017 from 10.0.2.2
        [vagrant@kubernetes-master ~]$ cat /etc/exports
        /opt/data 10.245.1.2/24(rw,sync,no_root_squash,no_all_squash)
        [vagrant@kubernetes-master ~]$ cd /opt/data
        [vagrant@kubernetes-master data]$ ls
        content  vol
        [vagrant@kubernetes-master data]$ cd vol
        [vagrant@kubernetes-master vol]$ ls
        0  1  2
        [vagrant@kubernetes-master vol]$ cd ../content/
        [vagrant@kubernetes-master content]$ ls
        [vagrant@kubernetes-master content]$ exit
        logout
        Connection to 127.0.0.1 closed.
        HQSML-151665:cluster pkrish00c$ vagrant ssh node-1
        Last login: Sat Jun  3 03:25:31 2017 from 10.0.2.2
        [vagrant@kubernetes-node-1 ~]$ cat /etc/fstab

        #
        # /etc/fstab
        # Created by anaconda on Sat Jul 30 06:43:15 2016
        #
        # Accessible filesystems, by reference, are maintained under '/dev/disk'
        # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
        #
        /dev/mapper/fedora-root /                       xfs     defaults        0 0
        UUID=1cdd6a93-78a8-484c-a044-edb40514df37 /boot                   ext4    defaults        1 2
        /dev/mapper/fedora-swap swap                    swap    defaults        0 0
        10.245.1.2:/opt/data  /mnt/data  nfs      rw,sync,hard,intr  0    0
        [vagrant@kubernetes-node-1 ~]$ cd /mnt/data
        [vagrant@kubernetes-node-1 data]$ ls
        content  vol
        [vagrant@kubernetes-node-1 data]$ kubectl proxy
        The connection to the server localhost:8080 was refused - did you specify the right host or port?
        [vagrant@kubernetes-node-1 data]$ exit
        logout
        Connection to 127.0.0.1 closed.
        HQSML-151665:cluster pkrish00c$ kubectl proxy
        Starting to serve on 127.0.0.1:8001^C
        HQSML-151665:cluster pkrish00c$ vagrant status
        Current machine states:

        master                    running (virtualbox)
        node-1                    running (virtualbox)
        node-2                    running (virtualbox)
        node-3                    running (virtualbox)

        This environment represents multiple VMs. The VMs are all listed
        above with their current state. For more information about a specific
        VM, run `vagrant status NAME`.
        HQSML-151665:cluster pkrish00c$ cd wp-statefulset/
        HQSML-151665:wp-statefulset pkrish00c$ ls
        README.md	etcd.yml	mysql.yml	nfs-master.sh	nfs-node.sh	volumes.yml	wordpress.yml
        HQSML-151665:wp-statefulset pkrish00c$ kubectl create -f etcd.yml
        service "etcd" created
        service "etcd-client" created
        pod "etcd-0" created
        service "etcd-0" created
        pod "etcd-1" created
        service "etcd-1" created
        pod "etcd-2" created
        service "etcd-2" created
        HQSML-151665:wp-statefulset pkrish00c$ kubectl get pods
        NAME      READY     STATUS    RESTARTS   AGE
        etcd-0    0/1       Pending   0          6s
        etcd-1    0/1       Pending   0          5s
        etcd-2    0/1       Pending   0          5s
        HQSML-151665:wp-statefulset pkrish00c$ kubectl get pods
        NAME      READY     STATUS    RESTARTS   AGE
        etcd-0    0/1       Pending   0          8s
        etcd-1    0/1       Pending   0          7s
        etcd-2    0/1       Pending   0          7s
        HQSML-151665:wp-statefulset pkrish00c$ kubectl describe pods etcd-0
        Name:		etcd-0
        Namespace:	default
        Node:		/
        Labels:		app=etcd
                        etcd_node=etcd-0
        Status:		Pending
        IP:
        Controllers:	<none>
        Containers:
          etcd-0:
            Image:	quay.io/coreos/etcd:latest
            Ports:	2379/TCP, 2380/TCP, 4001/TCP, 7001/TCP
            Command:
              /usr/local/bin/etcd
              --name
              etcd-0
              --initial-advertise-peer-urls
              http://etcd-0:2380
              --listen-peer-urls
              http://0.0.0.0:2380
              --listen-client-urls
              http://0.0.0.0:2379
              --advertise-client-urls
              http://etcd-0:2379
              --initial-cluster
              etcd-0=http://etcd-0:2380,etcd-1=http://etcd-1:2380,etcd-2=http://etcd-2:2380
              --initial-cluster-state
              new
            Requests:
              cpu:	100m
            Volume Mounts:
              /var/run/secrets/kubernetes.io/serviceaccount from default-token-rsg6b (ro)
            Environment Variables:	<none>
        Conditions:
          Type		Status
          PodScheduled 	False
        Volumes:
          default-token-rsg6b:
            Type:	Secret (a volume populated by a Secret)
            SecretName:	default-token-rsg6b
        QoS Class:	Burstable
        Tolerations:	<none>
        Events:
          FirstSeen	LastSeen	Count	From			SubObjectPath	Type		Reason			Message
          ---------	--------	-----	----			-------------	--------	------			-------
          22s		7s		6	{default-scheduler }			Warning		FailedScheduling	pod (etcd-0) failed to fit in any node
        fit failure summary on nodes : MatchNodeSelector (3)
        HQSML-151665:wp-statefulset pkrish00c$ kubectl delete -f etcd.yml
        service "etcd" deleted
        service "etcd-client" deleted
        pod "etcd-0" deleted
        service "etcd-0" deleted
        pod "etcd-1" deleted
        service "etcd-1" deleted
        pod "etcd-2" deleted
        service "etcd-2" deleted
        HQSML-151665:wp-statefulset pkrish00c$ vagrant status
        Current machine states:

        master                    running (virtualbox)
        node-1                    running (virtualbox)
        node-2                    running (virtualbox)
        node-3                    running (virtualbox)

        This environment represents multiple VMs. The VMs are all listed
        above with their current state. For more information about a specific
        VM, run `vagrant status NAME`.
        HQSML-151665:wp-statefulset pkrish00c$ vagrant ssh master
        Last login: Sat Jun  3 04:09:32 2017 from 10.0.2.2
        [vagrant@kubernetes-master ~]$ sudo -i
        [root@kubernetes-master ~]# kubectl get nodes
        NAME                STATUS    AGE
        kubernetes-node-1   Ready     4h
        kubernetes-node-2   Ready     4h
        kubernetes-node-3   Ready     4h
        [root@kubernetes-master ~]# kubectl get rc
        [root@kubernetes-master ~]# exit
        logout
        [vagrant@kubernetes-master ~]$ exit
        logout
        Connection to 127.0.0.1 closed.
        HQSML-151665:wp-statefulset pkrish00c$ kubectl get rc
        No resources found.
        HQSML-151665:wp-statefulset pkrish00c$ kubectl describe nodes --namespace=kube-system
        Name:			kubernetes-node-1
        Role:
        Labels:			beta.kubernetes.io/arch=amd64
                                beta.kubernetes.io/os=linux
                                kubernetes.io/hostname=kubernetes-node-1
        Taints:			<none>
        CreationTimestamp:	Fri, 02 Jun 2017 20:03:23 -0400
        Phase:
        Conditions:
          Type			Status	LastHeartbeatTime			LastTransitionTime	      		Reason		Message
          ----			------	-----------------			------------------	      		------		-------
          OutOfDisk 		False 	Sat, 03 Jun 2017 00:39:09 -0400 	Fri, 02 Jun 2017 20:03:23 -0400 	KubeletHasSufficientDisk 	kubelet has sufficient disk space available
          MemoryPressure 	False 	Sat, 03 Jun 2017 00:39:09 -0400 	Fri, 02 Jun 2017 20:03:23 -0400 	KubeletHasSufficientMemory 	kubelet has sufficient memory available
          DiskPressure 		False 	Sat, 03 Jun 2017 00:39:09 -0400 	Fri, 02 Jun 2017 20:03:23 -0400 	KubeletHasNoDiskPressure 	kubelet has no disk pressure
          Ready 		True 	Sat, 03 Jun 2017 00:39:09 -0400 	Fri, 02 Jun 2017 20:03:33 -0400 	KubeletReady 			kubelet is posting ready status
        Addresses:		10.245.1.3,10.245.1.3,kubernetes-node-1
        Capacity:
         alpha.kubernetes.io/nvidia-gpu:	0
         cpu:					4
         memory:				2049056Ki
         pods:					110
        Allocatable:
         alpha.kubernetes.io/nvidia-gpu:	0
         cpu:					4
         memory:				2049056Ki
         pods:					110
        System Info:
         Machine ID:			c71908e7c7824a2bb065d21c39c909c7
         System UUID:			3E4CBFE9-CE78-469B-9823-6059EF560FA4
         Boot ID:			b5fbe1f2-161a-4aa4-950a-34cbe9afd931
         Kernel Version:		4.2.3-300.fc23.x86_64
         OS Image:			Fedora 23 (Twenty Three)
         Operating System:		linux
         Architecture:			amd64
         Container Runtime Version:	docker://1.10.3
         Kubelet Version:		v1.5.7
         Kube-Proxy Version:		v1.5.7
        ExternalID:			kubernetes-node-1
        Non-terminated Pods:		(5 in total)
          Namespace			Name						CPU Requests	CPU Limits	Memory Requests	Memory Limits
          ---------			----						------------	----------	---------------	-------------
          kube-system			heapster-v1.2.0.1-2320000070-bzxsk		288m (7%)	288m (7%)	595776Ki (29%)	595776Ki (29%)
          kube-system			kube-dns-2185667875-h4l7z			260m (6%)	0 (0%)		140Mi (6%)220Mi (10%)
          kube-system			kube-proxy-kubernetes-node-1			100m (2%)	0 (0%)		0 (0%)		0 (0%)
          kube-system			kubernetes-dashboard-3543765157-798hh		100m (2%)	100m (2%)	50Mi (2%)	50Mi (2%)
          kube-system			monitoring-influxdb-grafana-v4-l7vg9		200m (5%)	200m (5%)	600Mi (29%)600Mi (29%)
        Allocated resources:
          (Total limits may be over 100 percent, i.e., overcommitted.
          CPU Requests	CPU Limits	Memory Requests	Memory Limits
          ------------	----------	---------------	-------------
          948m (23%)	588m (14%)	1404736Ki (68%)	1486656Ki (72%)
        No events.


        Name:			kubernetes-node-2
        Role:
        Labels:			beta.kubernetes.io/arch=amd64
                                beta.kubernetes.io/os=linux
                                kubernetes.io/hostname=kubernetes-node-2
        Taints:			<none>
        CreationTimestamp:	Fri, 02 Jun 2017 20:15:55 -0400
        Phase:
        Conditions:
          Type			Status	LastHeartbeatTime			LastTransitionTime	      		Reason		Message
          ----			------	-----------------			------------------	      		------		-------
          OutOfDisk 		False 	Sat, 03 Jun 2017 00:39:10 -0400 	Fri, 02 Jun 2017 20:15:55 -0400 	KubeletHasSufficientDisk 	kubelet has sufficient disk space available
          MemoryPressure 	False 	Sat, 03 Jun 2017 00:39:10 -0400 	Fri, 02 Jun 2017 20:15:55 -0400 	KubeletHasSufficientMemory 	kubelet has sufficient memory available
          DiskPressure 		False 	Sat, 03 Jun 2017 00:39:10 -0400 	Fri, 02 Jun 2017 20:15:55 -0400 	KubeletHasNoDiskPressure 	kubelet has no disk pressure
          Ready 		True 	Sat, 03 Jun 2017 00:39:10 -0400 	Fri, 02 Jun 2017 20:16:05 -0400 	KubeletReady 			kubelet is posting ready status
        Addresses:		10.245.1.4,10.245.1.4,kubernetes-node-2
        Capacity:
         alpha.kubernetes.io/nvidia-gpu:	0
         cpu:					4
         memory:				2049056Ki
         pods:					110
        Allocatable:
         alpha.kubernetes.io/nvidia-gpu:	0
         cpu:					4
         memory:				2049056Ki
         pods:					110
        System Info:
         Machine ID:			c71908e7c7824a2bb065d21c39c909c7
         System UUID:			87D37373-CB5B-4BB2-BBE2-811C3A918255
         Boot ID:			4fb34d53-ba0f-4a37-bf27-5fa6882e98cb
         Kernel Version:		4.2.3-300.fc23.x86_64
         OS Image:			Fedora 23 (Twenty Three)
         Operating System:		linux
         Architecture:			amd64
         Container Runtime Version:	docker://1.10.3
         Kubelet Version:		v1.5.7
         Kube-Proxy Version:		v1.5.7
        ExternalID:			kubernetes-node-2
        Non-terminated Pods:		(1 in total)
          Namespace			Name					CPU Requests	CPU Limits    	Memory Requests	Memory Limits
          ---------			----					------------	----------    	---------------	-------------
          kube-system			kube-proxy-kubernetes-node-2		100m (2%)	0 (0%)	      	0 (0%)		0 (0%)
        Allocated resources:
          (Total limits may be over 100 percent, i.e., overcommitted.
          CPU Requests	CPU Limits	Memory Requests	Memory Limits
          ------------	----------	---------------	-------------
          100m (2%)	0 (0%)		0 (0%)		0 (0%)
        No events.


        Name:			kubernetes-node-3
        Role:
        Labels:			beta.kubernetes.io/arch=amd64
                                beta.kubernetes.io/os=linux
                                kubernetes.io/hostname=kubernetes-node-3
        Taints:			<none>
        CreationTimestamp:	Fri, 02 Jun 2017 20:30:49 -0400
        Phase:
        Conditions:
          Type			Status	LastHeartbeatTime			LastTransitionTime	      		Reason		Message
          ----			------	-----------------			------------------	      		------		-------
          OutOfDisk 		False 	Sat, 03 Jun 2017 00:39:16 -0400 	Fri, 02 Jun 2017 20:30:49 -0400 	KubeletHasSufficientDisk 	kubelet has sufficient disk space available
          MemoryPressure 	False 	Sat, 03 Jun 2017 00:39:16 -0400 	Fri, 02 Jun 2017 20:30:49 -0400 	KubeletHasSufficientMemory 	kubelet has sufficient memory available
          DiskPressure 		False 	Sat, 03 Jun 2017 00:39:16 -0400 	Fri, 02 Jun 2017 20:30:49 -0400 	KubeletHasNoDiskPressure 	kubelet has no disk pressure
          Ready 		True 	Sat, 03 Jun 2017 00:39:16 -0400 	Fri, 02 Jun 2017 20:30:59 -0400 	KubeletReady 			kubelet is posting ready status
        Addresses:		10.245.1.5,10.245.1.5,kubernetes-node-3
        Capacity:
         alpha.kubernetes.io/nvidia-gpu:	0
         cpu:					4
         memory:				2049056Ki
         pods:					110
        Allocatable:
         alpha.kubernetes.io/nvidia-gpu:	0
         cpu:					4
         memory:				2049056Ki
         pods:					110
        System Info:
         Machine ID:			c71908e7c7824a2bb065d21c39c909c7
         System UUID:			7BDF13F9-D72C-4234-9AB3-950333BD8868
         Boot ID:			64bc652e-0db9-4df2-90fd-48891b95359e
         Kernel Version:		4.2.3-300.fc23.x86_64
         OS Image:			Fedora 23 (Twenty Three)
         Operating System:		linux
         Architecture:			amd64
         Container Runtime Version:	docker://1.10.3
         Kubelet Version:		v1.5.7
         Kube-Proxy Version:		v1.5.7
        ExternalID:			kubernetes-node-3
        Non-terminated Pods:		(1 in total)
          Namespace			Name					CPU Requests	CPU Limits    	Memory Requests	Memory Limits
          ---------			----					------------	----------    	---------------	-------------
          kube-system			kube-proxy-kubernetes-node-3		100m (2%)	0 (0%)	      	0 (0%)		0 (0%)
        Allocated resources:
          (Total limits may be over 100 percent, i.e., overcommitted.
          CPU Requests	CPU Limits	Memory Requests	Memory Limits
          ------------	----------	---------------	-------------
          100m (2%)	0 (0%)		0 (0%)		0 (0%)
        No events.
        HQSML-151665:wp-statefulset pkrish00c$ kubectl get nodes --show-labels
        NAME                STATUS    AGE       LABELS
        kubernetes-node-1   Ready     4h        beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=kubernetes-node-1
        kubernetes-node-2   Ready     4h        beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=kubernetes-node-2
        kubernetes-node-3   Ready     4h        beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/hostname=kubernetes-node-3

        HQSML-151665:wp-statefulset pkrish00c$ kubectl label nodes kubernetes-node-1 name=node-1
        node "kubernetes-node-1" labeled
        HQSML-151665:wp-statefulset pkrish00c$ kubectl label nodes kubernetes-node-2 name=node-2
        node "kubernetes-node-2" labeled
        HQSML-151665:wp-statefulset pkrish00c$ kubectl label nodes kubernetes-node-3 name=node-3
        node "kubernetes-node-3" labeled
        HQSML-151665:wp-statefulset pkrish00c$ pwd
        /Users/pkrish00c/statefulsets/kubernetes/cluster/wp-statefulset
        HQSML-151665:wp-statefulset pkrish00c$ cd ../../../..
        HQSML-151665:~ pkrish00c$ pwd
        /Users/pkrish00c
        HQSML-151665:~ pkrish00c$ docker pull perconalab/percona-xtradb-cluster:5.6
        5.6: Pulling from perconalab/percona-xtradb-cluster
        a3ed95caeb02: Pull complete
        da71393503ec: Downloading  17.3 MB/70.58 MB
        da71393503ec: Pull complete
        c462b27599b4: Pull complete
        f3554efb1640: Pull complete
        045c442ea10c: Pull complete
        05faccfa1b10: Pull complete
        fd7ac75d6ce0: Pull complete
        c72b6bf602cc: Pull complete
        e16681490a70: Pull complete
        b1b0b21c9109: Pull complete
        Digest: sha256:e8e7a47ff2326c49045366c4a7baf82595f9983d4b64ea8e2d046ddd0c3440f4
        Status: Downloaded newer image for perconalab/percona-xtradb-cluster:5.6
        HQSML-151665:~ pkrish00c$ clear
        HQSML-151665:~ pkrish00c$ kubectl proxy
        Starting to serve on 127.0.0.1:8001
        ^C
