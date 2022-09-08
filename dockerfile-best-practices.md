# Dockerfile Best Practices

### Here are some best practices for writing dockerfiles that are;
    - Maintainable
    - Secure
    - Produce small image size


1. Always use the most current release base upstream image for security reasons. 
    > - Use the latest release of a base image. This release should contain the latest security patches available when the base image is built. When a new release of the base image is available, rebuild the application image to incorporate the base image's latest release, because that release contains the latest fixes.
    > - Conduct vulnerability scanning. Scan a base or application image to confirm that it doesn't contain any known security vulnerabilities. 
    > - [Red Hat Developer Website](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

2. Use a specific Tag or version for your image not “latest”. 
    > Using a specific tag gives your image traceability incase of trouble shooting, you know the exact image used.
    > - For example ```nginx:1.23.1``` INSTEAD of ```nginx:latest```

3. Run Images as ```NON-ROOT``` by defining ```USER``` in your Dockerfile and set the permissions for the files and directories to the ```USER```. 
    > By default Docker images run as Root. This is insecure because if a process went rogue in the container it will also have root access to the host;
    > For Example, add USER as follows in your Dockerfile:
    
    ```sh
        USER 1001
        RUN chown -R 1001:0 /some/directory
        chmod -R g=u /some/directory
    ```

4. Always choose the smallest base images that do not contain the complete or full-blown OS with default system utilities installed. 
    > You can add the tools and utilities needed for your application in the Dockerfile build. This will reduce possible vulnerabilities in your image.
    > - For example ```nginx:1.23.1-alpine``` instead of ```nginx:1.23-suse```

5. Build images using Multi-Stage Dockerfiles to keep image size small. 
    > For example, for a Java application use 1 stage to compile and build the application and then a 2nd stage to copy the compiled artifact(s) and dependencies only and discard unneeded artifacts.
    > - See: https://docs.docker.com/develop/develop-images/multistage-build/ 

6. Use a ```.dockerignore``` file to ignore files you do not need added to the image.

7. Scan your images for known vulnerabilities. 
    > For example, on your local run; 
    ```sh 
        docker scan myappimage:1.0
    ```
    > Automated scanning tools should also be implemented in the CI pipeline and or the enterprise registry.

8. Order your Docker commands especially the ```COPY``` command so that the most volatile are the bottom. 
    > This will speed up the build process by maximizing on build caching. Each Docker build command creates a layer, this layer is cached to speed up the next build; however, If a command encounters a change, all the layers after that will be rebuilt even if there are no changes to those commands.  
 
- Sources:
    - https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
    - https://developers.redhat.com/articles/2021/11/11/best-practices-building-images-pass-red-hat-container-certification#
