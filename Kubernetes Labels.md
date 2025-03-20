### Kubernetes **labels** are ==key-value pairs== attached to Kubernetes objects (e.g., pods, services, deployments) to organize and select resources. They are used to group resources logically and for querying specific subsets.


### **1. Add a Label to a Resource**

To add a label to a Kubernetes resource:
`kubectl label <resource-type> <resource-name> <key>=<value>`

**Example:**  
Add the label `env=production` to a pod named `my-pod`.

`kubectl label pod my-pod env=production`



### **2. View Labels of a Resource**

To view labels attached to a specific resource:
`kubectl get <resource-type> <resource-name> --show-labels`

**Example:**  
Get labels for all pods in the namespace:
`kubectl get pods --show-labels`



### **3. Update or Change an Existing Label**

To update a label (overwrite the existing value):

`kubectl label <resource-type> <resource-name> <key>=<new-value> --overwrite`

**Example:**  
Change the `env` label for the pod `my-pod` to `staging`.

`kubectl label pod my-pod env=staging --overwrite`


### **4. Filter Resources by Labels**

Use the `-l` or `--selector` flag to filter resources by label.

**Example 1:**  
Get all pods with the label `env=production`:

`kubectl get pods -l env=production`

**Example 2:**  
Get all resources (pods, services, deployments, etc.) with a specific label:

`kubectl get all -l app=frontend`


### **5. Delete Resources by Labels**

To delete all resources with a specific label:

`kubectl delete <resource-type> -l <key>=<value>`

**Example:**  
Delete all pods labeled as `env=production`:

`kubectl delete pods -l env=production`/
`kubectl delete pods -l env=production`