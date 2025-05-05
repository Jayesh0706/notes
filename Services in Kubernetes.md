#### In Kubernetes, a **Service** is an abstraction that defines a logical set of Pods and a policy to access them. Services ==provide a stable endpoint to applications== running on Pods, even if the underlying Pods are dynamically created or destroyed.





### **Key Concepts**

1. **Purpose of Services**:
    
    - Pods in Kubernetes are ephemeral, meaning their IPs can change if the Pod is restarted. Services provide a consistent way to access Pods.
    - They enable communication between components of an application (e.g., frontend talking to backend).
    - They provide load balancing across multiple Pods.

2. **Service Selector**:
     - The set of Pods targeted by a Service is usually determined by a [selector](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) that you define
    - A Service uses **selectors** to identify the Pods it routes traffic to, based on Pod labels.

3. **ClusterIP and Endpoints**:
    - When you create a Service, Kubernetes assigns a **ClusterIP** to the Service, which acts as a virtual IP inside the cluster.
    - The Service points to a set of **Endpoints**, which are the IPs of the Pods matching the selector.

---

### **Kubernetes Services: Core Functionalities**

#### 1) **Pod Discovery (Service Discovery)**

#### 🔍 Problem:

Pods have dynamic IPs. If a ==Pod dies and is recreated, its IP changes, making it hard to communicate consistently==.

#### ✅ Solution with Services:

- A **Service** ==groups Pods using **label selectors**==.
    
- Kubernetes assigns a **DNS name** and **cluster-internal IP** to the service.
    
- Any Pod can reach the service by DNS (e.g., `my-service.default.svc.cluster.local`)

- #### 🔧 Example:

```
selector:   
    app: my-app
```

> This means: route requests to all Pods with `app=my-app`.



#### 2) **Load Balancing**

#### 🎯 Goal:

Distribute incoming traffic evenly to multiple healthy Pods.

#### ✅ How It Works:

- A Service maintains an internal list of matching Pods (via labels).
    
- It uses **round-robin load balancing** to distribute requests.
    
- Load balancing happens **at the IP:Port level**, managed by **kube-proxy** on each node.
    

#### 💡 Supported by:

- **ClusterIP**
    
- **NodePort**
    
- **LoadBalancer**


#### 3) **Exposing to the Outside World**

#### 🚪 Goal:

Allow users outside the Kubernetes cluster to access services.

#### ✅ Service Types:

| Type             | Access Scope                     | How to Access                               |
| ---------------- | -------------------------------- | ------------------------------------------- |
| **ClusterIP**    | Internal only                    | `http://<service-name>` from inside cluster |
| **NodePort**     | External via Node IP             | `http://<NodeIP>:<NodePort>`                |
| **LoadBalancer** | External via Cloud Load Balancer | Public IP assigned by cloud provider        |
|                  |                                  |                                             |

### **Summary Table**

| Feature         | Handled by                        |
| --------------- | --------------------------------- |
| Pod Discovery   | Label selectors + Service DNS     |
| Load Balancing  | kube-proxy & iptables/IPVS        |
| External Access | NodePort / LoadBalancer / Ingress |

---

### **There are 3 types of services mainly used in k8s :**

#### **a. ClusterIP (Default)**-

- Exposes the Service on a ==cluster-internal IP==. Choosing this value makes the Service only reachable from within the cluster.
- **What it does:**
    - Exposes the Service internally within the Kubernetes cluster.
    - Only accessible from within the cluster (e.g., by other pods).

- **Use cases:**
    - For internal communication between microservices.
    - Backend services like databases or caches that do not need to be exposed to the internet.




#### **b. NodePort -**(Not safe cause we expose the port)
- Exposes the Service on each Node's IP at a ==static port== (the `NodePort`). To make the node port available(It is very risky, ==companies don't use node port== )

- **What it does:**
    - Exposes the Service externally on a static port (between 30000–32767) on each node in the cluster.
    - Accessible using `<NodeIP>:<NodePort>`.

- **Use cases:**
    - For development or debugging purposes where direct node access is acceptable.
    - For exposing services in environments without a LoadBalancer.


#### **C. LoadBalancer-**
- On cloud providers which support external load balancers, setting the `type` field to `LoadBalancer` provisions a load balancer for your Service.(if you have their services like EKS,GKE)

-  cloud-controller-manager component then configures the external load balancer to forward traffic to that assigned node port.

- **What it does:**
    - Exposes the Service externally using a cloud provider's load balancer (e.g., AWS ELB, Google Cloud LB).(for automatic load balancer -EKS )
    - Automatically provisions a load balancer and assigns an external IP.

- **Use cases:**
    - For production environments where external traffic needs to reach the application.
    - Scenarios requiring high availability and load distribution.


---
### **3. Core Concepts of Kubernetes Services**

- **Selectors:** Define the pods the Service routes traffic to, based on labels.
- **Endpoints:** Represents the set of IPs and ports for the pods selected by the Service.
- **TargetPort:** The port on the pod where the Service forwards traffic.
- **ClusterIP:** Internal IP assigned to the Service for communication


---
 
 
##### In Kubernetes, a **Service** provides a stable endpoint (IP address and port) to access a set of pods. A Service routes traffic to the correct pods based on labels and selectors, and the port configuration plays a key role in how traffic is handled within the cluster. ==The **ports** in a Kubernetes Service configuration define the communication between different components.==


### Key Ports in Kubernetes Service

1. **port**
2. **targetPort**
3. **nodePort** (for `NodePort` services)
4. **protocol**
5. **name**

---
### 1. **port**

- **Definition**: This is the port on which the Service is exposed within the Kubernetes cluster.
- **Usage**: The `port` is the port that other services or pods in the cluster use to access the Service.
- **Example**: If you define `port: 80`, any service or pod that communicates with your Service would connect to port 80.
- `ports:
     port: 80  # Exposed port inside the cluster`

---

### 2. **targetPort**

- **Definition**: This is the port on the **pods** that the Service forwards traffic to. This is where your application is listening.(Expose used in docker image)
- **Usage**: The `targetPort` should match the port the container inside the pod is listening on. If this is not specified, it defaults to the same value as the `port`.
- **Example**: If you set `targetPort: 8080`, Kubernetes will route traffic that hits port `80` to port `8080` inside the pods.
- ports:
     port: 80       # Exposed port on the service
     targetPort: 8080  # Port where the pod is actually listening

---

### 3. **nodePort** (only for `NodePort` Service type)

- **Definition**: This is the port on each node in the cluster that exposes the service externally (outside the Kubernetes cluster).(port of your host)

- **Usage**: The `nodePort` is used in `NodePort` type services. It allows you to access the service from outside the Kubernetes cluster by connecting to `nodeIP:nodePort`.

- **Port Range**: By default, the valid range for `nodePort` is from `30000` to `32767`, but you can specify a custom range if needed.

- **Example**: If you set `nodePort: 30080`, traffic hitting port `30080` on any node's external IP will be forwarded to the service.

-  ports:
     port: 80         # Internal service port  (used request by application)
     targetPort: 8080  # Port in the pod forwards traffic to port
     nodePort: 30080   # Port on each node forward's traffic to target port
