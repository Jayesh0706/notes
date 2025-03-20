## **Probes** are used to monitor the health of containers ==within a pod==. These probes help Kubernetes determine if a container is functioning correctly and whether it should be restarted or removed from the load balancer.

---
### **There are three types of probes:**
##### **1)Readiness**
##### **2)Liveness**
##### **3)Startup**

---
#### **1)Readiness -**

1) **Purpose**: Indicates if the application inside the container is ready to handle traffic.
2) **When to Use**:
    - When your application takes time to initialize or load resources before it can serve requests.
    - Ensures the container is not added to the service's endpoint until it is ready.
3) If the readiness probe returns a failed state, Kubernetes removes the pod from all matching service endpoints
4) Readiness probes run on the container during its whole lifecycle.
5) **Example Use Case**: A web server that needs to load large configuration files before handling user requests.


#### **2)Liveness -**
1) **Purpose**: Checks if the application inside the container is still alive and functioning. If the liveness probe fails, Kubernetes restarts the container.
2) liveness probes could catch a deadlock when an application is running but unable to make progress.
3) **When to Use**:
    - When your application might hang, crash, or become unresponsive but the container itself doesn’t exit.
    - Ensures the container is restarted if the application stops responding.
4) **Example Use Case**: A Java application with a memory leak that causes it to freeze after running for a while.
5) Liveness probes do not wait for readiness probes to succeed. If you want to wait before executing a liveness probe, you can either define `initialDelaySeconds` or use a [startup probe](https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/#startup-probe).

#### **3)Startup**
1) **Purpose**: Checks if the application inside the container has started successfully. It is primarily used for containers that take a long time to initialize.
2) **When to Use**:
    - This can be used to adopt liveness checks on slow starting containers, avoiding them getting killed by the kubelet before they are up and running.
    - When your application takes an exceptionally long time to start and might cause the liveness probe to fail during initialization.
    - Prevents unnecessary restarts during the startup phase.
3) This can be used to adopt liveness checks on slow starting containers, avoiding them getting killed by the kubelet before they are up and running.
4) **Example Use Case**: A legacy application with a lengthy initialization process due to database schema migrations.

---
