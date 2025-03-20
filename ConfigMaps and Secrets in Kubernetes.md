
## **ConfigMaps**
#### A ConfigMap is an API object used to store non-confidential data in key-value pairs. [Pods](https://kubernetes.io/docs/concepts/workloads/pods/) can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume(recommended).

#Note -  The data stored in a ConfigMap cannot exceed 1 MiB. If you need to store settings that are larger than this limit, you may want to consider mounting a volume or use a separate database or file service.


### **Using ConfigMaps in Pods**

You can use ConfigMaps in Pods in two ways:

1. **As Environment Variables**
```
env:  
 - name: ENV_KEY1  
   valueFrom:    
    configMapKeyRef:       
      name: app-config       
      key: key1
```
    
2. **As a Volume**
```
volumeMounts: 
  - name: config-volume 
    mountPath: /app/config 
volumes: 
  - name: config-volume 
    configMap: 
      name: app-config
```

#NOTE - Configmaps consumed in ==volume== are updated ==automatically==, but if ConfigMap is consumed as ==ENV VARIABLE== they require ==Pod Restart==

---
## **Secrets**
