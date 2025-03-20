- n Kubernetes, anything a user creates and retains is referred to as an “**Object**.”.
- Kubernetes objects are ==persistent entities== in the Kubernetes system. Kubernetes uses these entities to represent the state of your cluster
- A Kubernetes object is a "record of intent"--once you create the object, the Kubernetes system will constantly work to ensure that the object exists.
- By creating an object, you're effectively telling the Kubernetes system what you want your cluster's workload to look like; this is your cluster's **_desired state_**.



The following table shows the important native Kubernetes object types organized in categories.

|Category|Kubernetes Objects|
|---|---|
|**Workload**|1. Pods  <br>2. ReplicaSets  <br>3. Deployments  <br>4. StatefulSets  <br>5. DaemonSets  <br>6. Jobs  <br>7. CronJobs  <br>8. Horizontal Pod Autoscaler  <br>9. Vertical Pod Autoscaler|
|**Service & Networking**|1. Services  <br>2. Ingress  <br>3. IngressClasses  <br>4. Network Policies  <br>5. Endpoints  <br>6. EndpointSlices|
|**Storage**|1. PersistentVolumes  <br>2. PersistentVolumeClaims  <br>3. StorageClasses|
|**Configuration & Management**|1. ConfigMaps  <br>2. Namespaces  <br>3. ResourceQuotas  <br>4. LimitRanges  <br>5. Pod Disruption Budgets (PDB)  <br>6. Pod Priority and Preemption|
|**Security**|1. Secrets  <br>2. ServiceAccounts (sa)  <br>3. Roles  <br>4. RoleBindings  <br>5. ClusterRoles  <br>6. ClusterRoleBindings|
|**Metadata**|1. Labels and Selectors  <br>2. Annotations  <br>3. Finalizers|

## Common Object Parameters

There are many object types in Kubernetes, however, there are common parameters for every object.

Following are the common set of parameters shared by Kubernetes objects

|Parameter|Description|
|---|---|
|**apiVersion**|The Kubernetes API version for the object.  <br>– Alpha  <br>– Beta  <br>– Stable|
|**kind**|The type of object being defined  <br>– Pod  <br>– Deployment  <br>– Service  <br>– Configmap, etc.|
|**metadata**|metadata is used to uniquely identify and describe a Kubernetes object. Here are some of the common key metadata that can be added to an object  <br>– labels  <br>– name  <br>– namespace  <br>– annotations  <br>– finalizers etc|
|**spec**|Under the ‘spec’ section of a Kubernetes object definition, we declare the desired state and characteristics of the object that we want to create.|
