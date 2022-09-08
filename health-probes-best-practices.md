# Best Practices for Kubernetes Health Check/Probes

If not configured properly, accounting for variance within the application, these can cause issues for your service and impact availability.
 
- Summary. 
1. Startup probe - checks if the container application has started. Stops at the first success. If it fails, the container is killed and subject to pod's ```restartPolicy```. It disables the liveness and readiness probes until it succeeds
2. Readiness probe -  checks if the container application is available/ready to receive requests. If it fails traffic will not be routed by the service to the container. It runs through out the lifetime of the application on every ```periodSeconds```.If it failes, it does not restart your application but will take it off lb rotation.
    - increase the ```failureThreshold``` above the default count of 3 to avoid the probe failing prematurely due to temporary latency issues.
    - If it is checking for external dependencies make sure the ```timeoutSeconds``` is long enough.
3. Liveness probe - checks if the container application is in a healthy state and responsive. If the check fails it will restart the container. It will restart container if it fails.
    - Make sure that the ```initialDelaySeconds``` is accounts for ```readinessProbe``` so that the probe does not start before the container has been marked as ready.
    - Set the ```initialDelaySeconds``` in such a way as to account for variance in startup times. It should be long enough for variable startup times to avoid the container being caught in a restart loop caused by the probe itsel causing a self inflicted DOS.
    - Make sure to account for temporary latency issues to avoid unnecessary failures. If you set probe ```timeoutSeconds``` then make it as long as the client timeout with a higher ```failureThreshold``` count. 
    - Should be a trivial predictable call with consistent response times and does not include checking for external services or dependencies.


- Do not use the same endpoint for both the ```readinessProbe``` and ```livenessProbe```
