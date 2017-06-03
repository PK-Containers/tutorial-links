
HQSML-151665:dynamic-provisioning pkrish00c$ kubectl create -f storage.yaml
storageclass "fast" created
HQSML-151665:dynamic-provisioning pkrish00c$ vi pvc.yaml
HQSML-151665:dynamic-provisioning pkrish00c$ kubectl create -f pvc.yaml
persistentvolumeclaim "claim1" created
HQSML-151665:dynamic-provisioning pkrish00c$ kubectl pvc
Error: unknown command "pvc" for "kubectl"
Run 'kubectl --help' for usage.
HQSML-151665:dynamic-provisioning pkrish00c$ kubectl get pvc
NAME         STATUS    VOLUME      CAPACITY   ACCESSMODES   AGE
claim1       Pending                                        11s


db-mysql-0   Bound     mysql-pv0   1Gi        RWX           6h


kind: StorageClass
db-mysql-1   Bound     mysql-pv1   1Gi        RWX           6h
db-mysql-2   Bound     mysql-pv2   1Gi        RWX           6h
HQSML-151665:dynamic-provisioning pkrish00c$ kubectl get pv
NAME        CAPACITY   ACCESSMODES   RECLAIMPOLICY   STATUS    CLAIM                REASON    AGE
mysql-pv0   1Gi        RWX           Recycle         Bound     default/db-mysql-0             6h
mysql-pv1   1Gi        RWX           Recycle         Bound     default/db-mysql-1             6h
mysql-pv2   1Gi        RWX           Recycle         Bound     default/db-mysql-2             6h


HQSML-151665:dynamic-provisioning pkrish00c$ kubectl get pvc
NAME         STATUS    VOLUME      CAPACITY   ACCESSMODES   AGE
claim1       Pending                                        22s
db-mysql-0   Bound     mysql-pv0   1Gi        RWX           6h
db-mysql-1   Bound     mysql-pv1   1Gi        RWX           6h
db-mysql-2   Bound     mysql-pv2   1Gi        RWX           6h

HQSML-151665:dynamic-provisioning pkrish00c$ kubectl describe pvc claim1
Name:		claim1
Namespace:	default
StorageClass:	fast
Status:		Pending
Volume:
Labels:		<none>
Capacity:
Access Modes:
Events:
  FirstSeen	LastSeen	Count	From				SubObjectPath	Type		Reason			Message
  ---------	--------	-----	----				-------------	--------	------			-------
  1m		2s		8	{persistentvolume-controller }			Warning		ProvisioningFailed	no volume plugin matched
HQSML-151665:dynamic-provisioning pkrish00c$ ls
pvc.yaml	storage.yaml
HQSML-151665:dynamic-provisioning pkrish00c$ vi storage.yaml
HQSML-151665:dynamic-provisioning pkrish00c$ vi storage-slow.yaml
HQSML-151665:dynamic-provisioning pkrish00c$ kubectl create -f storage-slow.yaml
storageclass "slow" created
HQSML-151665:dynamic-provisioning pkrish00c$ cp pvc.yaml pvc-slow.yaml
HQSML-151665:dynamic-provisioning pkrish00c$ kubectl create -f pvc-slow.yaml
Error from server (AlreadyExists): error when creating "pvc-slow.yaml": persistentvolumeclaims "claim1" already exists
HQSML-151665:dynamic-provisioning pkrish00c$ vi pvc-slow.yaml
HQSML-151665:dynamic-provisioning pkrish00c$ kubectl create -f pvc-slow.yaml
persistentvolumeclaim "claim2" created
HQSML-151665:dynamic-provisioning pkrish00c$ kubectl get pvc
NAME         STATUS    VOLUME      CAPACITY   ACCESSMODES   AGE
claim1       Pending                                        3m
claim2       Pending                                        6s
db-mysql-0   Bound     mysql-pv0   1Gi        RWX           7h
db-mysql-1   Bound     mysql-pv1   1Gi        RWX           7h
db-mysql-2   Bound     mysql-pv2   1Gi        RWX           7h

HQSML-151665:dynamic-provisioning pkrish00c$ kubectl describe pvc claim2
Name:		claim2
Namespace:	default
StorageClass:	fast
Status:		Pending
Volume:
Labels:		<none>
Capacity:
Access Modes:
Events:
  FirstSeen	LastSeen	Count	From				SubObjectPath	Type		Reason			Message
  ---------	--------	-----	----				-------------	--------	------			-------
  31s		12s		3	{persistentvolume-controller }			Warning		ProvisioningFailed	no volume plugin matched
