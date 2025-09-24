# Kubernetes

## OOMKLilled Vs OutOfMemoryError


| Aspect              | **OOMKilled (cgroup)**   | **OutOfMemoryError (JVM)** |
| ------------------- | ------------------------ | -------------------------- |
| Who enforces        | Linux kernel             | JVM itself                 |
| Trigger             | Container memory > limit | Heap > -Xmx                |
| Shutdown type       | Immediate kill (SIGKILL) | Exception (can log/dump)   |
| Debugging possible? | Hard (only system logs)  | Easy (heap dump/logs)      |
| Container memory    | Fully exhausted          | Might still have space     |

### QoS(Quality of Service) :
**Ans:** QoS is decided based on the resources limits and requests.QoS types are **Guarenteed, Brustable, BestEffort**.
| Pod Type   | Requests                               | Limits | QoS Class      | Behavior                                            |
| ---------- | -------------------------------------- | ------ | -------------- | --------------------------------------------------- |
| Guaranteed | All requests = limits                  | Yes    | **Guaranteed** | Strongest guarantee, last to be evicted             |
| Burstable  | Some requests set, not equal to limits | Yes    | **Burstable**  | Can use more than request, middle eviction priority |
| BestEffort | None                                   | None   | **BestEffort** | No guarantee, first to be evicted                   |

### Taints and Tolerations:
- Taints are working on keeping away mechanism like not scheduling the pods on tainted nodes.
    - Effects:
              
               1. NoSchedule - prevents future scheduling, leaves existing/current running Pods

               2. PreferNoSchedule - prevents future scheduling, if necessary then allows for scheduling

               3. No Execute - prevents future scheduling AND evicts current Pods without a matching toleration. 

    ``` kubectl taint nodes node_name key=value:effect ```  --> From CLI temporarily, will get removed after node restarts.

- To taint nodes permanently, we can add the taint condition in **Kubelet Configuration/ Cloud Provider(EKS,AKS)**
  
  Kubelet config:
  ```
    --register-with-taints=key=value:effect

   --register-with-taints=role=database:NoSchedule
  ```  

 Taint via Node Group UI / CLI:
 ```
 aws eks create-nodegroup \
  --cluster-name my-cluster \
  --nodegroup-name database-nodes \
  --node-role arn:aws:iam::123456789012:role/EKSNodeRole \
  --subnets subnet-abc subnet-def \
  --instance-types t3.medium \
  --taints key=role,value=database,effect=NO_SCHEDULE
```
 Taint via Launch Template:
 ```
 --kubelet-extra-args "--register-with-taints=role=database:NoSchedule"
``` 

- Tolerations are exception to assign pods to the matched tainted nodes.
- This Pod is okay to be scheduled on nodes with this taint.

 Added in Pod spec:
 ```
 toleration:
 - key: "role"
   operator: "equal"
   value: "databadse"
   effect: "NoSchedule"
   tolerationSeconds: 300
 ```  
**Why taints and Tolerations:**
- Dedicated nodes → e.g., database workloads only on DB nodes.
- Isolate special hardware → e.g., GPU nodes only for ML Pods.
- Node maintenance → taint a node so no new Pods get scheduled.
- Evict Pods → using NoExecute for failures or draining. 
