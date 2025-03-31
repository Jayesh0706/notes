## **Replication Controller (RC)**
#### A **Replication Controller (RC)** in Kubernetes is a mechanism to ensure that a specified number of pod replicas are running at any given time. It is an older Kubernetes concept that is being ==replaced by **Deployments**==, but it is still supported and used in certain scenarios.


## **ReplicaSet (RS)**

#### A **ReplicaSet (RS)** in Kubernetes ensures a specified number of pod replicas are running at all times. It is a modern ==replacement for the older **ReplicationController**== with added flexibility and advanced features.

### **Why Use Deployments Instead of ReplicaSets Directly?**

While you can use ReplicaSets independently, they are commonly managed by **Deployments**, which add the following benefits:

- **Rolling Updates**: Seamlessly update pods to a new version of the application.
- **Rollbacks**: Easily revert to a previous state if something goes wrong.
- **Declarative Management**: Simplifies pod lifecycle management. 

To check pod is part of ReplicaSet or not - `kubectl describe pod <pod_name>`
check OwnerReferences - to get info of pod

| Feature                    | ReplicationController (RC)                                | ReplicaSet (RS)                                                                              |
| -------------------------- | --------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| **Introduced In**          | Early Kubernetes versions                                 | Kubernetes 1.2 (Replaces RC)                                                                 |
| **Selector Type**          | Only ==**equality-based selectors**== (e.g., `key=value`) | Supports both ==**equality** and **set-based selectors**== (e.g., `key in (value1, value2)`) |
| **Use Case**               | Basic pod replication                                     | Advanced pod replication                                                                     |
| **Rolling Updates**        | Not supported (manual scaling required)                   | Supports rolling updates when used with **Deployments**                                      |
| **Recommendation**         | Deprecated (use ReplicaSet or Deployment instead)         | Preferred over RC, especially in combination with Deployments                                |
| **Backward Compatibility** | Yes (still supported for legacy workloads)                | Fully backward compatible with RC                                                            |
| **Template Support**       | Manages pods using a pod template                         | Same pod template structure as RC                                                            |
| **Scaling**                | Supported (manual scaling)                                | Supported (manual or Deployment-driven scaling)                                              |

**Key Takeaway:**  
While both RC and RS are used to maintain a stable number of pods, ==**ReplicaSet** is more versatile due to its support for advanced selectors and integration with **Deployments**==. RC is considered legacy, and RS is the recommended choice for most scenarios.


### **Key Concepts in Deployments**

1. **Pod**: The smallest deployable unit in Kubernetes, which runs one or more containers.
2. **ReplicaSet**: Ensures the desired number of Pod replicas are running.
3. **Deployment**: Manages ReplicaSets and enables updates, scaling, and rollbacks.


## **Deployments**

### **What is a Kubernetes *Deployment*?**

A **Deployment** in Kubernetes is a controller that ensures your application runs as ==desired by creating and managing **Pods**.== It helps:

- ==Scale up/down== the number of Pods.
- ==Roll out updates== to your application seamlessly.
- ==Roll back to previous versions== if something goes wrong.



### **1. Create a Deployment**

1) **Using a YAML file**:
    `kubectl apply -f deployment.yaml`
    
2) **Using the `kubectl create deploy` command**: (through command)
    `kubectl create deploy <deployment-name> --image=<image-name> --replicas=<number>`
    
    - Example:

    `kubectl create deploy nginx-deployment --image=nginx --replicas=3`
    

---

### **2. Get Deployment Details**

1) **List all deployments**:
  
    `kubectl get deployments`
    
2) **Get details of a specific deployment**:

    `kubectl describe deployment <deployment-name>`
    
3) **Get YAML output of a deployment**:

    `kubectl get deployment <deployment-name> -o yaml`(==Full YAML of the current deployment== and check the image used.)
    

---

### **3. Update a Deployment**

1) **Change the image of a container in a deployment**:(through cmd)
  
    `kubectl set image deployment/<deployment-name> <container-name>=<new-image>`
    
    - Example:
    
    `kubectl set image deployment/nginx-deployment nginx=nginx:1.23`
    
2)  **Apply updated configuration from a YAML file**:

    `kubectl apply -f updated-deployment.yaml`
    
    - ==**Record All Changes**==:

     - Always use `--record` during updates to log the change reason.
         - Example:

    `kubectl apply -f deployment.yaml --record`
    

---

### **4. Scale a Deployment**

1) **Increase or decrease replicas**:

    `kubectl scale deployment <deployment-name> --replicas=<number>`
    
     -  Example:
 
    `kubectl scale deployment nginx-deployment --replicas=5`
    

---

### **5. Monitor Rollouts**

1) **Check the rollout status**:
    
    `kubectl rollout status deployment/<deployment-name>`
    
2) **View rollout history**:

    `kubectl rollout history deployment/<deployment-name>`
    `kubectl rollout history deployment/nginx-deployment --revision=2`(for detailed for any revision)
    
3) **Rollback to a previous revision**:

    `kubectl rollout undo deployment/<deployment-name> --to-revision=<revision-number>`
    
    - Example:
    `kubectl rollout undo deployment/nginx-deployment`
    

---

### **6. Delete a Deployment**

1) **Delete by name**:

    `kubectl delete deployment <deployment-name>`
    
2) **Delete using a YAML file**:
 
    `kubectl delete -f deployment.yaml`
    

---

### **7. Debug a Deployment**

1) **View Deployment events**:

    `kubectl describe deployment <deployment-name>`
    
2) **Inspect the Pods created by the Deployment**:
 
    `kubectl get pods -l app=<label-name>`
    
    - Example:
    
    `kubectl get pods -l app=nginx`
    
3) **Get logs for a specific Pod**:
  
    `kubectl logs <pod-name>`
    

---

### **8. Export Deployment YAML for Editing**

1) Generate YAML for an existing deployment:

    `kubectl get deployment <deployment-name> -o yaml > deployment.yaml`
    
    - You can then edit `deployment.yaml` and reapply it:
    
    `kubectl apply -f deployment.yaml`
    

---

### **9. Pause and Resume Deployments**

1) **Pause a rollout** (stop applying updates):
    
    `kubectl rollout pause deployment/<deployment-name>`
    
2) **Resume a rollout**:

    `kubectl rollout resume deployment/<deployment-name>`
    
==3) To restart rollout if stuck:
    `kubectl rollout restart deployment <deployment-name>`==

---

### **10. Port-Forward to Access Pods**

1) **Forward a local port to a Pod's port**:

    `kubectl port-forward deployment/<deployment-name> <local-port>:<container-port>`
    
   -  Example:

    `kubectl port-forward deployment/nginx-deployment 8080:80`
    

---

### **Common Example Commands**

1) **Create a Deployment**:

    `kubectl create deploy my-deploy --image=nginx --replicas=3`
    
2. **Scale it to 5 replicas**:

    `kubectl scale deployment my-deploy --replicas=5`
    
3. **Update the image to a newer version**:
 
    `kubectl set image deployment/my-deploy nginx=nginx:1.21`
    
4. **Check rollout status**:

    `kubectl rollout status deployment/my-deploy`
    
5. **Rollback to the previous version**:

    `kubectl rollout undo deployment/my-deploy`