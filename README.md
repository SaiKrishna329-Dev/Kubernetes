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

