# **Docker Notes**

## **Virtualization vs Containerization**


### 1)Bare metal-
physical hardware server or machine that ==runs directly without any virtualization or additional software layers like a hypervisor==. It's the "raw" hardware of a computer, such as the CPU, memory, storage, and network interface, that an operating system or application can directly interact with.

 Management was simple, and issues were easier to treat because admins could focus their attention on that one specific server. The costs, however, were very high. Not only did ==you need more and more servers as your business grew==, you also needed to have enough space to host them.

## 2)Virtualization-

#doneby 
- Hypervisor-A **hypervisor** is a software layer or firmware that ==allows multiple operating systems (OS) to run simultaneously on a single physical machine==
- The hypervisor ==acts as an intermediary between virtual machines and the underlying hardware==, allocating host resources such as memory, CPU, and storage.


#### **Virtualization**

1) **Definition**: Virtualization is a technology that allows you to run multiple virtual machines (VMs) on a single physical server.
#imp -provides an abstraction of the ====physical hardware==

3) **Components**:
- **Hypervisor**: Software layer that manages multiple VMs.  
            Examples: VMware, Microsoft Hyper-V, Xen, KVM.
- **Guest OS**: ==Each VM runs its own operating system== (e.g., Windows, Linux).
- **Host OS**: The operating system on the physical machine (optional; some hypervisors run directly on hardware).

3) **Disadvantage**:
 - VMs ==require a full guest OS== for each instance, leading to ==higher resource usage.==
 - More overhead since each VM duplicates the OS kernel and libraries.
 - ==Slower boot times== and performance compared to containerization.
 - Applications are not portable


#### **Containerization**
#doneby 
A **Container Engine** is a software tool that manages the lifecycle of containers—packages that include an application and its dependencies—on a host system. It provides the runtime environment for containers and facilitates container creation, deployment, running, and monitoring.
#eg- docker, containerd, podman etc.

1) Containerization packages applications and their dependencies into ==lightweight, portable containers== that run on a ==shared OS kernel.==(containerization runs a single OS instance)
2) In simple term containerization is advance version of virtualization which uses less resource , portable and light weight
3) Code and all dependencies are packaged together so that an application can run across platforms such as desktop, data center, and cloud. ==Each container runs as an isolated process while sharing the same OS kernel==

- #imp - **Containers** provide abstraction at the ==application layer.== 

#### **Key Features**:

1. **Lightweight**: Containers share the host OS kernel, reducing resource usage compared to VMs.
2. **Faster Deployment**: Containers start almost instantly because they don’t require a full OS boot.
3. **Portability**: Containers are portable across different environments as long as the host supports the container runtime (e.g., Docker, Kubernetes).

#imp
 In a nutshell, container base images are typically smaller compared to VM images because they are designed to be minimalist and ==only contain the necessary components for running a specific application or service.== VMs, on the other hand, contain an entire operating system, ==including all its libraries, utilities, and system files, resulting in a much larger size.==
 
 https://github.com/iam-veeramalla/Docker-Zero-to-Hero/blob/main/README.md#files-and-folders-in-containers-base-images
 
 ### Files and Folders in ==**containers base images**== are downloaded with image and it is used for ==logical isolation== of containers
 ### Files and Folders that containers use from ==**host operating system**== - are used by containers from host 
 Due to this less files are downloaded and image base size is smaller and also we can have logical separation containers.

![[Pasted image 20241115164231.png]]

### **Docker vs Virtual Machines**

| **Feature**        | **Docker (Containers)**              | **Virtual Machines**        |
| ------------------ | ------------------------------------ | --------------------------- |
| **Weight**         | Lightweight                          | Heavy (full OS for each VM) |
| **Boot Time**      | Seconds                              | Minutes                     |
| **Resource Usage** | Shares host kernel, minimal overhead | Dedicated resources per VM  |
| **Isolation**      | Process-level isolation              | Full OS-level isolation     |
| **Portability**    | High (image-based portability)       | Limited                     |

--- 

## **What is Docker?**

- Docker is a platform for developing, shipping, and ==running applications in lightweight, isolated environments called **containers**.== Containers bundle an application and all its dependencies, making it easy to run consistently across different environments.
## **Why use Docker?**

- Docker simplifies software development by allowing you to package your code and dependencies together. It ensures that applications run the same way on any machine, regardless of setup.

---
## **Before Docker what problems were there and what solutions docker offers?**
### 1. "It Works on My Machine" Problem(Developer vs Testing)

- **Issue:** Applications often behaved differently on different environments (e.g., development, testing, and production). This was due to differences in OS, libraries, configurations, or dependencies across these environments.
- **Solution with Docker:** Docker containers encapsulate the app and all its dependencies in container, ensuring consistent behavior across any environment where Docker runs.


### 2. Complex Dependency Management

- **Issue:** Managing dependencies was complicated. Installing, updating, or removing dependencies could lead to "dependency hell," where dependencies conflict or break each other.
- **Solution with Docker:** Docker images package all dependencies with the application, creating isolated environments that prevent dependency conflicts.


### 3. Inefficient Resource Usage with VMs

- **Issue:** Before Docker, many organizations relied on virtual machines (VMs) for isolation.==VMs are heavier because they include an entire OS, which consumes more resources (CPU, memory).==
- **Solution with Docker:** Docker containers are lightweight, sharing the host OS kernel while maintaining isolation. This allows you to run more applications on the same hardware with less overhead.

### 4. Slow Deployment and Scaling

- **Issue:** Deploying applications and setting up new environments took time, slowing down development, testing, and scaling.
- **Solution with Docker:** Docker containers start up quickly, allowing for fast deployment and horizontal scaling. Teams can deploy, test, and scale applications rapidly.


### 5. Application Portability

- **Issue:** Moving applications from one environment to another (e.g., from on-premises to the cloud) was difficult due to dependency and configuration mismatches.
- **Solution with Docker:** Containers are portable across any environment that supports Docker, making it easy to move applications between environments.

---



## **Key Components of Docker for beginner**

#### 1. *Docker Registry*

- A repository for storing and distributing Docker images. The **Docker Registry** stores and serves **Docker Images**.
- Docker pulls images from Docker Registry(Docker Hub) if the image is not available locally.  
- Example: Docker Hub (public), Amazon Elastic Container Registry (private).

#### 2. *Docker Engine

- Core software that runs and manages containers.
- The **Docker Engine** is the core ==software that runs on the host machine and is responsible for managing **Docker Containers**==
- Includes:
    - **Docker Daemon**: Runs on the host and manages containers/images.
    - **Docker CLI**: Command-line interface for interacting with Docker.

#### 3. *Docker Images*

- A read-only template used to create containers.
- ==**Docker Images** are the blueprints or templates used to create Docker containers.== These images can be pulled from a registry and pushed back to it.
- Built using a **Dockerfile**, which contains instructions for assembling the image.
- Example: An image might include Ubuntu, Python, and the app code.

#imp -There are two important principles of images:

1. ==Images are **immutable**==. Once an image is created, it can't be modified. ==You can only make a new image or add changes on top of it.==
    
2. ==Container images are **composed of layers** ==. Each layer represents a set of file system changes that add, remove, or modify files.
#### 4. *Docker Containers*

- - A container is a lightweight, standalone, and executable package that ==contains everything needed to run a piece of software:== the application code, runtime, libraries, environment variables, and configuration files.
- Example: Running a Python app with all its dependencies in a container.

##### **Containers are:**

1) **Self-contained** - Each container has everything it needs to function with no reliance on any pre-installed dependencies on the host machine.
2) **Isolated** - Since containers are run in isolation, ==they have minimal influence on the host and other containers, increasing the security of your applications.==
3) **Independent** - Each container is independently managed. Deleting one container won't affect any others.
4) **Portable** - Containers can run anywhere! The ==container that runs on your development machine will work the same way in a data center or anywhere in the cloud!==

---
## **Commands**
