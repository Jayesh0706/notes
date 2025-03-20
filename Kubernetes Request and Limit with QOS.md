

**requests** and **limits** are used to manage resource allocation for containers in a pod. They ensure fair sharing of cluster resources and prevent resource contention between applications.(To avoid problem of noisy neighbour)

==remember that the limit can never be lower than the request. If you try this, Kubernetes will throw an error and won’t let you run the container.==
### **1. Resource Requests**

- **Definition:**
    - A _request_ is the ==minimum amount of a resource (CPU or memory)== that a container is guaranteed.
    - Kubernetes uses the request value ==to schedule a pod on a node with sufficient resources.==

- **Key Points:**
    - ==A pod will not be scheduled if no node can meet the requested resources.==
    - Requests define **guaranteed resources** for a container.

```resources:
  requests:
    memory: "512Mi"
    cpu: "500m"
```

-  This example guarantees:
        - **512MiB of memory**(1 mebibyte=1.048 Megabyte)
        - **0.5 CPU cores** (500 milliCPU)
        ```1000m (milicores) = 1 core = 1 vCPU = 1 AWS vCPU = 1 GCP Core.
        100m (milicores) = 0.1 core = 0.1 vCPU = 0.1 AWS vCPU = 0.1 GCP Core.```




### **2. Resource Limits**

- **Definition:**
    - A _limit_ is the ==maximum amount of a resource (CPU or memory)== a container is allowed to consume.
    - Containers exceeding the limit may be throttled (for CPU) or killed (for memory).

- **Key Points:**    
    - **CPU Limits:** The container is throttled if it tries to exceed the CPU limit.
    - **Memory Limits:** The container is terminated if it exceeds the memory limit.


### **Scheduling and Eviction**

- **Scheduling:**
    - Kubernetes schedules pods based on requests, ensuring the node has enough available resources.

- **Eviction:**
    - If the container exceeds its memory limit, it will be terminated.
    - CPU exceeding its limit will lead to throttling but not eviction.

---
----
# QOS (Quality Of Service) in kubernetes

Kubernetes provides a Quality of Service (QoS) mechanism to classify pods into three categories: BestEffort, Burstable, and Guaranteed. This classification determines how pods are scheduled, allocated resources, and prioritized for eviction ==when a node runs out of resources.==

## QoS Classes

1. **BestEffort**: Pods with no resource requests or limits. They are not guaranteed any specific resources and are the ==first to be evicted== when a node runs out of resources.
     - **Behavior:**
         - BestEffort pods get the lowest priority.
         - They are the first to be evicted under resource pressure.

2. **Burstable**: ==Pods with resource requests but no limits==. They can consume additional resources when available, but may experience performance degradation during periods of high demand.
     - At least one container in the pod has memory or CPU **requests set**, ==but requests and limits are **not equal**.==
     - **Behavior:**
         - The pod can consume more resources (up to the specified limits) if available.
         - It can be throttled or evicted under resource pressure.
     
3. **Guaranteed**: ==Pods with both resource requests and limits==. They are allocated the requested resources and are not evicted until they exceed their limits. 
     - All containers in the pod have CPU and memory requests **equal** to their limits.
     - **Use Case:** Use this for critical workloads that need guaranteed access to resources and cannot tolerate throttling or eviction.


    
### **Key Differences Between QoS Classes**

|**QoS Class**|**Priority**|**Eviction Risk**|**Resource Allocation**|
|---|---|---|---|
|**Guaranteed**|High|Lowest|Requests = Limits for all containers|
|**Burstable**|Medium|Medium|Some requests set, limits may or may not be set|
|**BestEffort**|Low|Highest|No requests or limits defined|












