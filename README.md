# Kubernetes

## OOMKLilled Vs OutOfMemoryError


| Aspect              | **OOMKilled (cgroup)**   | **OutOfMemoryError (JVM)** |
| ------------------- | ------------------------ | -------------------------- |
| Who enforces        | Linux kernel             | JVM itself                 |
| Trigger             | Container memory > limit | Heap > -Xmx                |
| Shutdown type       | Immediate kill (SIGKILL) | Exception (can log/dump)   |
| Debugging possible? | Hard (only system logs)  | Easy (heap dump/logs)      |
| Container memory    | Fully exhausted          | Might still have space     |
