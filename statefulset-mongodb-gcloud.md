          HQSML-151665:cluster pkrish00c$ git clone https://github.com/thesandlord/mongo-k8s-sidecar.git
          Cloning into 'mongo-k8s-sidecar'...
          remote: Counting objects: 306, done.
          remote: Total 306 (delta 0), reused 0 (delta 0), pack-reused 306
          Receiving objects: 100% (306/306), 328.12 KiB | 396.00 KiB/s, done.
          Resolving deltas: 100% (155/155), done.
          HQSML-151665:cluster pkrish00c$ cd /mongo-k8s-sidecar/example/StatefulSet/
          -bash: cd: /mongo-k8s-sidecar/example/StatefulSet/: No such file or directory
          HQSML-151665:cluster pkrish00c$ ls
          OWNERS				common.sh			juju				kubemark			openstack-heat			test-e2e.sh			vsphere
          README.md			gce				kube-down.sh			lib   				options.md			test-network.sh
          addons				get-kube-binaries.sh		kube-push.sh			libvirt-coreos			ovirt				test-smoke.sh
          aws				get-kube-local.sh		kube-up.sh			local 				photon-controller		ubuntu
          azure				get-kube.sh			kube-util.sh			log-dump.sh			rackspace			update-storage-objects.sh
          azure-legacy			gke				kubeadm.sh			mesos 				restore-from-backup.sh		vagrant
          centos				images				kubectl.sh			mongo-k8s-sidecar		skeleton			validate-cluster.sh
          HQSML-151665:cluster pkrish00c$ cd mongo-k8s-sidecar/example/StatefulSet/
          HQSML-151665:StatefulSet pkrish00c$ ls
          README.md		azure_hdd.yaml		azure_ssd.yaml		googlecloud_hdd.yaml	googlecloud_ssd.yaml	mongo-statefulset.yaml
          HQSML-151665:StatefulSet pkrish00c$ kubectl apply -f googlecloud_ssd.yaml
          storageclass "fast" created
          HQSML-151665:StatefulSet pkrish00c$ kubectl apply -f mongo-statefulset.yaml
          service "mongo" created
          statefulset "mongo" created
          HQSML-151665:StatefulSet pkrish00c$ kubectl get pvc
          NAME                               STATUS    VOLUME    CAPACITY   ACCESSMODES   AGE
          mongo-persistent-storage-mongo-0   Pending                                      5s
          mongo-persistent-storage-mongo-1   Pending                                      5s
          mongo-persistent-storage-mongo-2   Pending                                      5s
          HQSML-151665:StatefulSet pkrish00c$ kubectl get pv
          No resources found.
          HQSML-151665:StatefulSet pkrish00c$ kubectl delete -f mongo-statefulset.yaml
          service "mongo" deleted
          statefulset "mongo" deleted
          HQSML-151665:StatefulSet pkrish00c$ kubectl delete -f googlecloud_ssd.yaml
          storageclass "fast" deleted
          HQSML-151665:StatefulSet pkrish00c$ ls
          README.md		azure_hdd.yaml		azure_ssd.yaml		googlecloud_hdd.yaml	googlecloud_ssd.yaml	mongo-statefulset.yaml
          HQSML-151665:StatefulSet pkrish00c$ cd ..
          HQSML-151665:example pkrish00c$ ls
          Makefile				StatefulSet				emptydir_pod_examples 			mongo-controller-template.yaml
          README.md				ceph_rbd_pod_examples			mongo-controller-flocker-template.yaml	mongo-service-template.yaml
          HQSML-151665:example pkrish00c$ cd ..
          HQSML-151665:mongo-k8s-sidecar pkrish00c$ ls
          Dockerfile	LICENSE		README.md	example		package.json	src
          HQSML-151665:mongo-k8s-sidecar pkrish00c$ pwd
          /Users/pkrish00c/statefulsets/kubernetes/cluster/mongo-k8s-sidecar
          HQSML-151665:mongo-k8s-sidecar pkrish00c$ cd ..
          HQSML-151665:cluster pkrish00c$ rm -fr mongo-k8s-sidecar/
