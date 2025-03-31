### **Node Affinity**

Node affinity is a ==way to constrain which nodes your pod is eligible to be scheduled based on labels on nodes==.

---

**Types:**

1. **RequiredDuringSchedulingIgnoredDuringExecution**:
    
    - The pod ==**must**== be scheduled on ==nodes matching the affinity rules.==    
    - If ==no suitable nodes== exist, the pod ==won't be scheduled==.
        
2. **PreferredDuringSchedulingIgnoredDuringExecution**:
    
    - The pod **prefers** to be scheduled on nodes matching the affinity rules but ==can be scheduled on any available node if none match==.
---
