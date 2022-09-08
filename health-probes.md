# Best Practices for Openshift Health Check/Probes

If not configured well accounting for variance within the application, they can cause issues for your service and impact availability.
 
- Summary. Kubelet uses;
1. Startup probe - to check if the container application has started. Stops when its succeeds once. If it fails, the container is killed and subject to pod's ```restartPolicy```. It disables the liveness and readiness probes until it succeeds
2. Readiness probe -  to check if the container application is available to receive traffic. If it fails traffic will not be routed by the service to the container. It runs through out the lifetime of the application on every ```periodSeconds````  .
3. Liveness probe - to check if the container application is in a healthy state and responsive. If the check fails it will restart the container.


- Readiness probe - Check the availability of your application to accept requests. Does not restart your application but will take it off lb rotation.
    - If it is checking for external dependencies give it enough time to check. Ensure probe time out is longer than the slowest or max response time of the dependency.
    - increase the ```failureThreshold``` above the default count of 3 to avoid the probe failing prematurely due to temporary latency issues.
- Liveness probe - Check if container application has become unresponsive and will restart container if it fails.
    - Make sure to account for a healthy ```initialDelaySeconds```. If for some reason the  is not long enough due to a startup issue, the container may never start due to constant restarts by the probe itself.
    - Make sure to account for temporary latency issues for example larger responses. It may be a good idea to have the probe timeout as long as the client timeout with a higher ```failureThreshold``` count. 
    - Should generally not be checking for dependencies and should be cheap and have consistent response times.

https://blog.colinbreck.com/kubernetes-liveness-and-readiness-probes-how-to-avoid-shooting-yourself-in-the-foot/

https://developers.redhat.com/blog/2020/11/10/you-probably-need-liveness-and-readiness-probes#what_if_i_don_t_specify_a_liveness_probe_