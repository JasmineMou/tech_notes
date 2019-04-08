## kubernetes
### structure
[[[[[[index.php] web server] container] pod] replication controller] service & volume] namespace]

### pods
- a runnable unit of work, usually run a single container inside of a pod; tightly coupled containers may run more than one container inside the same pod. 
- kubernetes connect the pod to the network and the rest of the kubernetes' ecosystem. 
- a pod can hold any number of containers, but usually only holds 2. since we pretend one of those containers doesn't exist, so it usually only has one. 

### replication controllers
- provides a pod template for creating any number of pod copies. 
- provides logic for scaling the pod up and down.
- can be used for rolling deploys and monitoring. 

### K8 service
- is persistent on IP address and ports regardless of the pods' existence. 
- provides service discovery for pods. 
- load balancers.
- provides VIP layer.
- identifies pods by label selector. 

### volumes
- a location for the container to access and store info. 
- a part of the local file system backed by local storage. providers expose both persistent and ephmeral storage of EBS, Ceph, Gluster.
- pods can mount volumes like filesystems. 

### namespace
- segment pods, rcs, volumes, secrets from each other.
- grouping mechanism.
- not security minded, but changes services discovery. 


# daemon
dfc btw daemon and service
dfc btw daemon and background process
