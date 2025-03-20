# Ingress -
- Make your HTTP (or HTTPS) network service available using a protocol-aware configuration mechanism, that understands web concepts like URIs, hostnames, paths, and more.
- The Ingress concept lets you ==map traffic to different backends based on rules you define via the Kubernetes API.==

An ingress is an ==alternative to creating a dedicated load balancer in front of Kubernetes services==, or manually exposing services within a node. It lets you flexibly configure routing rules, greatly simplifying your production environment.

---
### Key Components of Ingress

1. **Ingress Resource**: A YAML/JSON configuration that specifies rules for routing traffic to the appropriate services.
2. **Ingress Controller**: A pod that implements the Ingress rules and manages the actual HTTP/HTTPS traffic. Examples include NGINX Ingress Controller, Traefik, or HAProxy.

---
### Why Use Ingress?
- Before Ingress we have been using services but for services we have to buy separate static IP for each service that is being very costly compared traditional LB.
- Also there are lot of features were available on traditional load balancing rather than only round-robin in LoadBalancer service.
- Consolidates multiple service routes under one IP address.
- Supports advanced HTTP routing features like path-based routing, host-based routing, SSL termination, etc.
- Replaces or complements other access methods like NodePort and LoadBalancer.

---
## What is Ingress?[](https://kubernetes.io/docs/concepts/services-networking/ingress/#what-is-ingress)

[Ingress](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.32/#ingress-v1-networking-k8s-io) exposes HTTP and HTTPS routes from outside the cluster to [services](https://kubernetes.io/docs/concepts/services-networking/service/) within the cluster. Traffic routing is controlled by rules defined on the Ingress resource.

Here is a simple example where an Ingress sends all its traffic to one Service:

![[Pasted image 20241221191321.png]]


## Prerequisites[](https://kubernetes.io/docs/concepts/services-networking/ingress/#prerequisites)

You must have an [Ingress controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) to satisfy an Ingress. Only creating an Ingress resource has no effect.(Ingress controller forwards request to ingress)

You may need to deploy an Ingress controller such as [ingress-nginx](https://kubernetes.github.io/ingress-nginx/deploy/). You can choose from a number of [Ingress controllers](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/).

Ideally, all Ingress controllers should fit the reference specification. In reality, the various Ingress controllers operate slightly differently.


## Types of Ingress

### 1) Single Service Ingress

- A single service ingress exposes only one service to external users.
- To enable a single service ingress, you must define a default backend—if the ingress object’s host or path does not match information in the HTTP message, traffic is forwarded to this default backend.
- The default backend does not have any routing rules.
![[Pasted image 20241224205443.png]]

### 2) Simple Fanout Ingress
- A simple fan-out ingress allows you to expose multiple services using a single IP address
- This makes it easier to route traffic to destinations based on the type of request.
- This type of ingress simplifies traffic routing while reducing the total number of load balancers in the cluster.

![[Pasted image 20241224205724.png]]
### 3) Name-based Virtual Hosting
- Name-based virtual hosting makes it possible to route HTTP traffic to multiple hostnames with the same IP address.
- This ingress type usually directs traffic to a specific host before evaluating routing rules.
- Example:
    - Requests to `app1.example.com` are routed to `Service app1`.
    - Requests to `app2.example.com` are routed to `Service app2`.
![[Pasted image 20241224205903.png]]


---
---
## Kubernetes Ingress Controller
#### In order for an [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) to work in your cluster, there must be an _ingress controller_ running.

==An ingress controller is a dedicated load balancer for Kubernetes clusters.==

#### Ingress controller implements a Kubernetes Ingress and works as a load balancer and reverse proxy entity. It abstracts traffic routing by directing traffic coming towards a Kubernetes platform to the pods inside and load balancing it.

#### The controller’s routing directions come from the Ingress resource configurations.

- Kubernetes cluster can have multiple associated Ingress controllers, and each one works similarly to a deployment controller.
- It is event-driven and gets triggered when, for example, a user creates, updates or deletes an Ingress.
- Once triggered, an Ingress controller tracks a cluster’s Ingress resources and reads the Ingress specifications. Then, it makes the configuration (in YAML or JSON) intelligible for the reverse proxy so it can provide the cluster with the resources it requires.


The main functions of a Kubernetes ingress controller are to receive and load-balance traffic from outside Kubernetes to pods running in a Kubernetes cluster, and manage egress traffic from services within the cluster to services outside the cluster. ==They monitor pods running on Kubernetes and automatically update load balancing rules when pods are added or removed from a service.==



## Kubernetes Ingress Controller Benefits and Limitations

- Technically, an ingress is not a service, it is a ==layer 7 (application layer) router== that is typically exposed through a load balancer service. It is cheaper than using a cloud load balancer for each service as it relies on an ingress controller which is hosted together with the application.
- It ensures each service has only one IP that can be accessed from the internet, and ==traffic intended for this IP is routed to the correct service by the ingress controller.==



**Benefits of ingress controllers include:**

- **Provides secure access** to services over HTTP or HTTPS paths, instead of using direct connections.
- **Creates access routes** to multiple services, with full control over routing of services and conditions for external access.
- **Creates a single path for ingress traffic**, which can be modified based on conditions defined by the operator, instead of opening many connections to access a Kubernetes application.
- **Simplifies management** of complex ingress processes, with the ability to easily manage access to multiple services in one system. This can have a significant impact on performance and management costs of large systems.
- All ingress controllers support a set of annotations that enable specific software-based features. For example, in the Traefik ingress controller, users can use annotations to add middleware to ingress, even if not supported by the Ingress specification.


**Limitations of ingress controllers:**

- The Ingress controller ==only handles layer 7 traffic==, while an ingress routes HTTP and HTTPS traffic. This means it is ==not possible to route TCP and UDP traffic==.
- Ingress is used in a single namespace. This means that ==anything within a Kubernetes namespace can only reference services within the same namespace==. To solve this problem, Kubernetes has introduced a gateway API specification, which enables cross-namespace communication. This specification is still in alpha status. A Kubernetes-native API Gateway can be used today for cross-namespace communications.



### **How to choose an ingress controller**

There are several ingress controllers available in Kubernetes, each has advantages and preferred use cases. It can be challenging to select the ingress controller suitable for your needs.
If you’re managing a small production environment and don’t plan to scale it, you probably need only the standard features such as load balancing, flow control, and flow splitting. Be careful not to select a controller that has more functionality than you need, because this can negatively impact performance and create unneeded management complexity.

However, if your application runs in a distributed, multi-cloud environment, you may need an enterprise-grade, high-performance networking solution.

---

https://www.solo.io/topics/kubernetes-api-gateway/kubernetes-ingress

# Kubernetes Gateway API

## Current State of Ingress in Kubernetes

- After you deploy a Kubernetes application, you typically need to expose it to end users. This is usually done using an Ingress Controller. The Ingress API object defines the routing and mapping of external traffic to the Kubernetes service. It also provides load balancing, SSL termination, and name-based virtual hosting. Currently, the native Ingress API is very limited in scope.

- While this basic set of ingress controls was enough to get started, the need for more sophisticated controls over incoming traffic led to the development of additional tools to add more control over ingress.


## What is the Kubernetes Gateway API?

- The Kubernetes Gateway API is the first step toward adding additional control over ingress patterns directly in Kubernetes.
- It represents a new standard in defining how to configure and manage how traffic is defined and routed in Kubernetes.
- This extensible API is a collection of resources that models a network of services in Kubernetes and provides  a standard way to describe how inbound traffic routes can be defined.
- The Gateway API consists of three new resources to define.

     1) GatewayClass: This exists at a cluster level to describe the set of common configurations and behaviors for a set of Gateways.
     2) Gateway: A gateway defines how traffic can connect to the services that exist in the cluster.
     3) Routes: These describe how to map incoming requests to the services. These can be defined as HTTPRoute, TLSRoute, TCPRoute/UDPRoute or GRPCRoute.

![[Pasted image 20241226114019.png]]
- GatewayClass must be defined in order to have any Gateways in a cluster. Each Gateway defines ==how incoming traffic will connect through a Route to the service it is destined to connect with.==


Here's a **comparison table** highlighting the differences between Kubernetes **Ingress** and **Gateway API**:

| Feature                                | **Ingress**                                                                  | **Gateway API**                                                              |
| -------------------------------------- | ---------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| **Introduction**                       | Native Kubernetes object for managing HTTP/HTTPS routing.                    | Modern, extensible API designed to replace Ingress over time.                |
| **Traffic Routing**                    | Basic routing based on hostnames and paths.                                  | Advanced routing with support for headers, query params, etc.                |
| **Protocol Support**                   | Primarily HTTP/HTTPS.(Only layer 7)                                          | Supports HTTP, HTTPS, TCP, UDP, and more.                                    |
| **Multi-tenancy**                      | Limited, requires custom implementations.                                    | Built-in support with `GatewayClass` and delegation.                         |
| **Extensibility**                      | Limited to annotations for customizations.                                   | Modular design with support for custom route types.                          |
| **Multi-listener Support**             | Not supported.                                                               | Supports multiple listeners (e.g., HTTP, HTTPS, TCP) on different ports.     |
| **Separation of Concerns**             | Cluster admins and developers manage a single object.                        | Separation between infrastructure (`Gateway`) and app routing (`HTTPRoute`). |
| **Traffic Splitting**                  | Limited (requires custom annotations or controllers).                        | Native support for traffic splitting (e.g., ==canary deployments).==         |
| **TLS Configuration**                  | Basic support with limited flexibility.                                      | Granular TLS configuration, including per-route TLS settings.                |
| **Controller Support**                 | Widely supported by most ingress controllers.                                | ==Requires Gateway-compatible controllers (e.g., Istio, NGINX).==            |
| **Scalability**                        | Suitable for basic use cases in small/medium clusters.                       | Designed for ==large-scale and complex routing scenarios.==                  |
| **Standardization**                    | Relies on annotations, leading to inconsistent behavior between controllers. | Consistent behavior with standardized CRDs.                                  |
| **Custom Resource Definitions (CRDs)** | No CRDs, uses `Ingress` resource.                                            | CRDs like `GatewayClass`, `Gateway`, `HTTPRoute`, etc.                       |
| **Maturity**                           | Stable and widely used.                                                      | Newer, under active development, and rapidly gaining adoption.               |



### **When to Use Each**

- **Ingress:**
    
    - Use for simple HTTP/HTTPS routing in smaller or less complex clusters.
    - Ideal for straightforward use cases with limited advanced routing needs.

- **Gateway API:**
    
    - Use for advanced routing, multi-tenancy, scalability, and extensibility.
    - Ideal for large, multi-team environments or when you need protocol-agnostic routing.