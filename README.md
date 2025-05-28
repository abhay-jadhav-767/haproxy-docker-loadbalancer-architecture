# haproxy-docker-loadbalancer-architecture
This project sets up a containerized load balancing architecture using HAProxy and multiple Nginx servers, all orchestrated with Docker. It demonstrates how to efficiently distribute HTTP traffic, perform health checks, and scale backend services in a microservices-friendly environment.




### Setup Guide: ###

Step 1: Create a Docker Network (example: newnetwork)
#
    docker network create newnetwork

Step 2: Create Four NGINX Containers
# 
    docker run -dit --name c1 --network newnetwork nginx
    docker run -dit --name c2 --network newnetwork nginx
    docker run -dit --name c3 --network newnetwork nginx
    docker run -dit --name c4 --network newnetwork nginx

Step 3: Modify the Index File in Each Container
Update the default NGINX index file to identify each container. Please refer index.html for content.

Step 4: Create a Dockerfile for HAProxy
Create a file named haproxydockerfile. Please refer Dockerfile for file content:

Step 5: Create the HAProxy Configuration File
Create a file named haproxy.cfg. Please refer "haproxy.cfg" for file content.

Step 6: Build the HAProxy Docker Image
# 
    docker build -f haproxydockerfile -t haproxy-custom .

Step 7: Run the HAProxy Container
# 
    docker run -d --name haproxy-container -p 80:80 --network newnetwork haproxy-custom

Step 8: Test Load Balancing
Open your EC2 instance’s public IP in a browser:
Refresh the page multiple times. You should see different responses from containers C1, C2, C3, and C4, confirming that load balancing is working.




## Additional Notes ##
If you want to use Nginx as a reverse proxy instead of HAProxy, you can simply launch an additional Nginx container as a proxyserver and configure it accordingly.
In the Nginx configuration:
Add a proxy_pass directive inside the location block to forward requests to your backend services.
Define an upstream block inside the http section to list your backend servers.

Example nginx.conf:
#
    http {
    upstream backend {
        server nginx1:80;
        server nginx2:80;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
          }
      }
     }
