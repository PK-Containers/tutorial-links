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
