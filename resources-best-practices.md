# CPU and Memory allocation best practices

## CPU
- CPU is a compressible resource meaning it can be redistributed.
1. Always use accurate CPU Requests
2. Do Not Use CPU Limits
    > This means that your application will always get additional CPU if needed, however it will not use up CPU needed by others as long as they have CPU requests which guarantees that they will be allocated what they requested for. CPU Limits waste allocatable CPU. 

## Memory
- Because Memory is non compressible and cannot be reclaimed after its been allocated unless the Pod is killed
1. Use Memory Limits
2. Use Memory Requests
3. Set limit == request
    > Setting limits higher than requestes allows overutilization and eventually leads to Out Of Memory Kill when a pod is trying to access unavailable memory


- Sources
    - https://github.com/kubernetes/design-proposals-archive/blob/8da1442ea29adccea40693357d04727127e045ed/node/resource-qos.md#compressible-resource-guaranteess
    - https://twitter.com/thockin/status/1134193838841401345?s=20&t=IhFrC3Fxuo0NIjEL7RacLQ
    - https://home.robusta.dev/blog/stop-using-cpu-limits/
    - https://home.robusta.dev/blog/kubernetes-memory-limit/
