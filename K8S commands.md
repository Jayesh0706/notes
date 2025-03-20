1) `kubectl create deploy j-deploy --image nginx --replicas 3 --port 80 -o yaml`
    - Use this command when you want to create the Deployment **and** simultaneously see the YAML representation of it.
    
2) `kubectl create deploy j-deploy --image nginx --replicas 3 --port 80 --dry-run=client -o yaml`
     - Use this command when you want to **generate a YAML file template** for a Deployment or validate that your command will work, **without actually creating it in the cluster**.

3) 