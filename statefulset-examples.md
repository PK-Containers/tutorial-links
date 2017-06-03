https://kubernetes.io/docs/tutorials/stateful-application/basic-stateful-set/

https://thenewstack.io/deploy-highly-available-wordpress-instance-statefulset-kubernetes-1-5/

http://blog.kubernetes.io/2017/01/running-mongodb-on-kubernetes-with-statefulsets.html

### Dynamic provisioning
http://blog.kubernetes.io/2016/10/dynamic-provisioning-and-storage-in-kubernetes.html

### Uninstall Vagrant on MAC

https://stackoverflow.com/questions/34027853/uninstall-vagrant-on-mac

To clean vagrant network interface drive
$ vagrant ssh <vagrant name>
[vagrant@localhost ~]$ sudo rm -rf /etc/sysconfig/network-scripts/ifcfg-enp0s9
[vagrant@localhost ~]$ sudo systemctl start network.service

To get list of vagrant names
vagrant status

Local vagrant setup
https://github.com/kubernetes/community/blob/master/contributors/devel/local-cluster/vagrant.md

If salt provisioning fails on latest k8s release/stable -> try 1.5.7
https://groups.google.com/forum/#!topic/kubernetes-sig-openstack-bugs/t0oQvcoTMdM

Access kubectl ui >
kubectl proxy
http://localhost:8001/ui 

https://en.m.wikipedia.org/wiki/Nudge_(book)

====
https://www.habitat.sh/tutorials/getting-started/next-steps/
https://fabric8.io/

