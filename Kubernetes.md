For Best kubernetes Architecture - https://devopscube.com/kubernetes-architecture-explained/ 


**Kubernetes** is an open source container orchestration engine for automating deployment, scaling, and management of containerized applications. The open source project is hosted by the Cloud Native Computing Foundation ([CNCF](https://www.cncf.io/about)).

 Kubernetes provides you with a framework to run ==distributed systems resiliently==. It takes care of scaling and failover for your application, provides deployment patterns, and more


## What is a Kubernetes Pod?

Kubernetes is a container orchestration system for deploying, scaling, and managing containerized applications and it has its own way of running containers. **We call it a pod**. A pod is the **==smallest deployable unit in Kubernetes==** that represents a single instance of an application.
pod can contain more than one container. You can think of **pods as a box that can hold one or more containers** together.

 #Note:
You need to install a [container runtime](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)(Docker, containerd,  etc) into each node in the cluster so that Pods can run there.


The shared context of a Pod is a set of ==Linux namespaces, cgroups, and potentially other facets of isolation== - the same things that isolate a [container](https://kubernetes.io/docs/concepts/containers/). Within a Pod's context, the individual applications may have further sub-isolations applied.


Pod provides a higher level of abstraction that allows you to manage multiple containers as a single unit. Here instead of each container getting an IP address, the ==**pod gets a single unique IP address**== and containers running inside the pod ==use localhost== to connect to each other on **different ports**.

![[Pasted image 20241213203857.png]]
It means containers inside the Kubernetes pod share the following

1. **Network namespace** – All containers inside a pod communicate via localhost.
2. **IPC namespace**: All containers use a shared inter-process communication namespace.
3. **UTS namespace**: All containers share the same hostname.



### Workload resources for managing pods[](https://kubernetes.io/docs/concepts/workloads/pods/#workload-resources-for-managing-pods)

Usually you don't need to create Pods directly, even singleton Pods. Instead, create them using workload resources such as [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) or [Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/). If your Pods need to track state, consider the [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) resource.

1)Deployment -  
- Deployment manages a set of Pods to run an application workload, usually one that doesn't maintain state.
- You describe a _desired state_ in a Deployment, and the Deployment [Controller](https://kubernetes.io/docs/concepts/architecture/controller/) changes the actual state to the desired state at a controlled rate.

2 )Job- 
- Jobs represent one-off tasks that run to completion and then stop.
- When a specified number of successful completions is reached, the task (ie, Job) is complete. Deleting a Job will clean up the Pods it created. Suspending a Job will delete its active Pods until the Job is resumed again.
- A simple case is to create one Job object in order to reliably run one Pod to completion. The Job object will start a new Pod if the first Pod fails or is deleted

3)Statefulset -
- A StatefulSet runs a group of Pods, and maintains a sticky identity for each of those Pods. This is useful for managing applications that need persistent storage or a stable, unique network identity.
- Manages the deployment and scaling of a set of [Pods](https://kubernetes.io/docs/concepts/workloads/pods/), _and provides guarantees about the ordering and uniqueness_ of these Pods.



In a nutshell, here is what you should know about a pod:

1. Pods are the smallest deployable units in Kubernetes.
2. Pods are ephemeral in nature; they can be created, deleted, and updated.
3. A pod can have more than one container; there is no limit to how many containers you can run inside a pod.
4. Each pod gets a unique IP address.
5. Pods communicate with each other using the IP address.
6. Containers inside a pod connect using localhost on different ports.
7. Containers running inside a pod should have different port numbers to avoid port clashes.
8. You can set CPU and memory resources for each container running inside the pod.
9. Containers inside a pod share the same volume mount.
10. All the containers inside a pod are scheduled on the same node; It cannot span multiple nodes.
11. If there is more than one container, during the pod startup all the main containers start in parallel. Whereas the init containers inside the pod run in sequence.


---


## **Pod YAML (Object Definition)**

Let’s understand this pod YAML. Once you understand the basic YAML it will be easier for you to work with pods and associated objects like [deployment](https://devopscube.com/kubernetes-deployment-tutorial/), [daemonset](https://devopscube.com/kubernetes-daemonset/), statefulset, etc.


| Parameter      | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **apiVersion** | The **`apiVersion`** tells Kubernetes which version of the API it should use to handle a specific resource.                                                                               - Format of the `apiVersion` is `api_group/version`.                                      - use - https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-apiversion-definition-guide.html                                                                                                                                                                                                                                                                        |
| **kind**       | The `kind` field defines the type of resource being created or modified.It tells Kubernetes what kind of object (e.g., Pod, Deployment, Service, etc.) the YAML file is describing.                                                                                - `kind` works alongside `apiVersion` to define what the resource is and how Kubernetes will manage it.<br>- Kubernetes has a variety of `kind` values, each tailored to specific use cases                                                                                                                   Common kinds include **Deployment** , **Service**, **Pod**, **ConfigMap** and **Ingress** |
| **metadata**   | metadata is used to uniquely identify and describe the pod  <br>– labels (set of key-value pairs to represent the pod). This is similar to tagging in cloud environments. Every object must be labeled with standard labels. It helps in grouping the objects.  <br>– name (name of the pod)  <br>– namespace (namespace for the pod)  <br>– annotations (additional data in key-value format)                                                                                                                                                                                                                                                                             |
| **spec**       | Under the ‘spec’ section we declare the desired state of the pod. Those are the specifications of the containers we want to run inside the pod.                 It contains detailed settings that tell Kubernetes how the resource should behave, such as how many Pods to run, what container image to use, or how to expose a Service.                                                                                    - **Resource-Specific**: The content of `spec` depends on the resource type (e.g., Pod, Deployment, Service, etc.).                                                                                                                           |
| **containers** | Under containers, we declare the desired state of the containers inside the pod. The container image, exposed port, etc.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |


## **Static Pods** 
#### Static Pods are a special type of Pod in Kubernetes that are ==**directly managed by the kubelet**== on a specific node, rather than by the Kubernetes API server

##### Static Pods are ==useful for running essential system-level pods or debugging tools== directly on the node without relying on the control plane.

#imp staticPodPath: cd /etc/kubernetes/manifests

kubeletConfigPath: /var/lib/kubelet/cofig.yaml (We can get static pod path in this file)



## **Daemon Set**
#### A **DaemonSet** in Kubernetes ensures that a copy of a specific Pod runs on **all (or a subset of)** nodes in a cluster. It is typically used to deploy node-level services that perform specific tasks or provide system-level functionality.

- New Nodes that join the cluster will automatically start running Pods that are part of a DaemonSet
- DaemonSets are often used to run long-lived background services such as Node ==monitoring systems and log collection agents==. To ensure complete coverage, it’s important that these apps run a Pod on every Node in your cluster.
-  You can optionally customize a DaemonSet’s configuration so that ==only a subset of your Nodes schedule a Pod==.

### **Use Cases for DaemonSet**

1) **Node Monitoring**:
    - Deploy monitoring agents like **Prometheus Node Exporter** or **Datadog agents** on each node to collect metrics.

2) **Log Collection**:
    - Run logging agents like **Fluentd**, **Filebeat**, or **Logstash** on every node to forward logs to a centralized system.

3) **Networking Components**:
    - Deploy network plugins (e.g., **CNI plugins** like ==Calico, Flannel, Weave==) to manage networking on each node.(CNI=Container Networking Interface)

4) **Security and Compliance**:
    - Run security agents like **Falco** or **Antivirus scanners** on each node to monitor for suspicious activity.

5) **Custom Node-Specific Tasks**:
    - Perform tasks like hardware configuration or disk cleanup on each node.



## **Init-Containers**
#### init containers: specialized containers that run before app containers in a [Pod](https://kubernetes.io/docs/concepts/workloads/pods/). Init containers can contain utilities or setup scripts not present in an app image.

 Init Containers are designed to perform setup or initialization tasks, such as pulling configuration files, setting up dependencies, or waiting for services to be ready.

#### **Key Characteristics of Init Containers**

1. **Runs Before the Main Container**:
    - Init Containers always run before the application containers start.
    - They run **sequentially**, one after another.
    
2. **Ephemeral**:
    - Once an Init Container completes its task, it is terminated and does not persist.

3. **Independent from the Main Container**:
     - Init Containers can use different images, tools, and configurations compared to the main application container.

4. **Restart Policy**:
    - If a Pod's init container fails, the kubelet ==repeatedly restarts== that init container until it succeeds. However, if the Pod has a `restartPolicy` of Never, and an init container fails during startup of that Pod, Kubernetes treats the overall Pod as failed.

5. **Preconditions**:   
    - You can use Init Containers to enforce specific preconditions (e.g., waiting for a service dependency to become available).



## **Sidecar Containers**

#### Sidecar containers are the ==secondary containers== that run along with the main application container within the same [Pod](https://kubernetes.io/docs/concepts/workloads/pods/). 
#### These containers are ==used to enhance or to extend the functionality of the primary _app container_== by providing additional services, or functionality such as logging, monitoring, security, or data synchronization, without directly altering the primary application code.


- The sidecar container and the main container ==share the same Pod's resources==, such as network namespace and storage volumes, enabling seamless collaboration.


### **Key Features of a Sidecar Container**

1. **Shared Environment**:
    - Runs in the same Pod as the main container and shares the network, storage, and other resources of the Pod.

2. **Independent Execution**:
       - The sidecar container runs concurrently with the main application container but ==performs a distinct, supportive role==.

3. **Persistent Lifecycle**:
        - Sidecars typically run for the **entire lifecycle of the Pod**.

4. **Decoupling Auxiliary Tasks(secondary tasks)**:
        - By moving supporting tasks (like logging or monitoring) to the sidecar, the main container can focus solely on the application logic.

### **Common Use Cases for Sidecar Containers**

#### 1. Logging and Monitoring
- Collect logs or metrics generated by the main container and ==forward them to a central system== like Elasticsearch, Prometheus, or CloudWatch.
- **Example**:
    Use **Fluentd** (image) as a sidecar to collect and forward application logs.

#### 2. Configuration Management

- Fetch configuration or secrets from external systems and share them with the main container.
- **Example**:
   A sidecar pulls configuration files from a remote server.

#### 3. Caching
- A sidecar container can act as a caching layer to improve performance by storing frequently accessed data locally.
- **Example**:
     A **Redis** ( Image) sidecar caching frequently used data.

#### 4. **Security Tasks**
- Handle token refresh, encryption, or decryption of data for the main container.

- **Example**:- A sidecar retrieves and refreshes security tokens from an identity provider.

![[Pasted image 20241214211654.png]]


### **Differences Between Init Containers and Sidecar Containers**

|**Feature**|**Init Container**|**Sidecar Container**|
|---|---|---|
|**Purpose**|Prepares the environment before the main container starts (setup tasks).|Provides auxiliary or supporting functionality alongside the main container.|
|**Execution**|Runs **sequentially before** the main container starts.|Runs **concurrently** with the main container.|
|**Lifecycle**|Runs once (or restarts until successful) and exits.|Runs for the **entire lifecycle** of the Pod.|
|**Restart Behavior**|Exits permanently once the task is completed successfully. Restarts on failure if required.|Runs continuously and restarts automatically if it fails during the Pod's lifecycle.|
|**Use Cases**|One-time setup tasks, such as dependency installation, configuration setup, or database migrations.|Ongoing tasks, such as logging, monitoring, proxying, or token refresh.|
|**Impact on Pod Start**|Blocks the main container from starting until it completes successfully.|Does not block the main container; runs alongside it.|
|**Resource Usage**|Temporary use of resources (memory, CPU) during the setup phase.|Consumes resources throughout the Pod's lifecycle.|
#### **Differences Between Init Container (With Always Restart) and Sidecar Container**
1) **If the Pod Needs Runtime Auxiliary Support**
    - **Init Container**: Cannot provide runtime support because ==it stops after the main container starts.==
    - **Sidecar Container**: ==Runs throughout the Pod’s lifecycle== and performs tasks like logging, monitoring, or proxying.
    - **Example**:
        Logging with Fluentd: A **sidecar container** would continuously forward logs from the app, whereas an Init Container can only pre-process logs before app startup.

2) **If the Environment Requires Repeated Preparation**
    - **Init Container**: With `restartPolicy: Always`, it will repeatedly try to prepare the environment until successful. ==Afterward, it terminates and plays no role==.
    - **Sidecar Container**: Will not terminate and will keep running to monitor and adjust the environment dynamically.
    - **Example**:
          A **database migration** Init Container can retry repeatedly until it successfully updates schemas before the app starts. A sidecar container is not suited for this task.




### **Adapter Container**

##### An **adapter container** is a type of **sidecar container** that primarily acts as a **translator or intermediary** between the main application container and external systems. It is often used in scenarios where the application needs to communicate with external systems in a format that it does not natively support.
==The adapter container takes care of transforming the output into what is acceptable at the cluster level==
#### **Key Features of Adapter Containers**

1. **Protocol Translation**:
    - Translates requests or responses between different protocols (e.g., converting gRPC to REST or vice versa).
    - Example: An app outputs logs in a custom format, and the adapter converts them into JSON or a specific logging format required by the centralized logging system.

2. **Data Transformation**:
    - Modifies or enriches the data sent or received by the application.
    - Example: Aggregates metrics or reformats API responses before passing them on.

3. **Continuous Operation**:
     - Runs alongside the main application container as a sidecar for the entire Pod's lifecycle.


#### **Common Use Cases**

1. **Log Parsing and Formatting**:
    - The adapter parses raw logs from the main container and ==reformats them before sending them to an external logging system.==
    - Example: Nginx outputs logs in a plain text format, and the adapter converts them to JSON.

2. **Metrics Translation**:
    - Converts application metrics into a format compatible with a monitoring system.
    - Example: Prometheus adapter collects and formats metrics for Prometheus scraping.

3. **Protocol Conversion**:
       - Translates one protocol into another to allow communication with systems that use incompatible formats.
    - Example: A gRPC client app communicates with a REST-based API using an adapter container.


![[Pasted image 20241214214217.png]]


### **Ambassador Container**
#### The ambassador design pattern is used to ==connect containers with the outside world==. In this design pattern, the helper container can send network requests on behalf of the main application. It is nothing but a proxy that allows other containers to connect to a port on the localhost.


### **Key Features of Ambassador Containers**

1. **Proxy for External Communication**:
    - Acts as a middleman, ==forwarding requests and responses between the main container and external services==.

2. **Decoupling**:
       - The main application does not need to manage the complexity of connecting to external systems (e.g., handling authentication, retries, or failover).

3. **Service Discovery**:
    - Handles dynamic service discovery to ensure the main container can seamlessly communicate with external systems, even if their IP addresses or ports change.

4. **Network Abstraction**:
    - The Ambassador container abstracts networking tasks such as load balancing, SSL/TLS termination, or API gateway functions.

### **Common Use Cases**

1. **Database Proxy**:
    - If the application connects to a database, the Ambassador container can handle connection pooling, caching, or routing to different database instances.

2. **API Gateway**:
    - Acts as an API gateway to external APIs, ==managing authentication (e.g., token injection) or protocol translation (e.g., gRPC to REST).==

3. **Load Balancing**:
       - Balances traffic ==across multiple external endpoints== without requiring the application to manage it.

4. **Encryption/Decryption**:
     - Handles SSL/TLS termination or encryption of requests to external systems.

5. **Rate Limiting or Failover**:
    - Manages rate limiting for outbound requests or failover logic if the primary endpoint is unavailable.



### **Comparison of Ambassador, Sidecar, and Adapter Containers**

|**Aspect**|**Ambassador Container**|**Adapter Container**|**Generic Sidecar**|
|---|---|---|---|
|**Role**|Proxy between app and external services.|Translates or adapts data or protocols.|Provides auxiliary services like logging or monitoring.|
|**Example Use Case**|API proxy, database proxy.|Protocol translation (e.g., gRPC to REST).|Log forwarding, metrics collection.|
|**Focus**|External communication.|Data or protocol adaptation.|General runtime support.|
