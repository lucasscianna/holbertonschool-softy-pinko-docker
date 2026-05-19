# Holberton School Softy Pinko Docker

## Context
Docker is a platform that allows you to containerize your applications, meaning that you can package them into portable, self-contained environments which can run anywhere. This means that you can quickly move your application from one environment to another, such as from your local computer to a server, without worrying about dependencies or configuration issues. Docker achieves this by using containers, which are isolated environments that contain everything an application needs to run, such as libraries, dependencies, and configurations. Docker containers are lightweight and can be started and stopped quickly, making them ideal for modern software development and deployment. With Docker, you can also quickly scale your application by running multiple containers of the same application on different hosts (or the same host, as we will do in this project), and manage them using Docker Compose or other orchestration tools.

Ultimately, what you will create in this project is an infrastructure for an application that utilizes a reverse proxy, a load balancer, two application servers, and one front-end server.

## High-level Design
In this design, there is a single server that acts as the entry point for your application. That server acts as a reverse proxy server, which routes traffic to either the API servers or the front-end static-content server; it also acts as a load balancer to balance traffic between the two API servers. When traffic comes in, the server will determine which service it needs to go to (either the front-end static-content server or the API server) and:

- If the request is to be routed to the front-end static-content server, it will do so and any static content that is returned from the front-end static-content server to the proxy server will then be sent to the client. The client isn’t directly communicating with the front-end static-content server.
- If the request is to be routed to the API server, then it will first go through a load-balancing algorithm to determine which API server to send the request to. We will be using the basic Round Robin load-balancing algorithm for our site. Once the request is routed to an API server and the response is sent back to the proxy server, it will then be sent to the client. The client isn’t directly communicating with either of the API servers.

### What is Round Robin load balancing?
Round Robin load balancing is a method of distributing traffic across multiple servers or nodes in a network. In this approach, the load balancer assigns requests to each server in a rotating manner, meaning that each server takes turns serving the requests. This ensures that each server receives an equal share of the traffic, and can prevent any one server from becoming overwhelmed with requests.

## Project Evolution (Tasks Overview)

Through a series of incremental tasks, we built this infrastructure from the ground up:

### Task 0: Create Your First Docker Image
Started with the basics by creating a simple Dockerfile based on Ubuntu, updating APT packages, and configuring the container to output "Hello, World!".

### Task 1: Back-end API Setup
Transitioned our container to host a back-end service. Installed Python 3, pip3, and Flask, and created a basic `api.py` endpoint returning "Hello, World!" on port 5252.

### Task 2: Front-end Static Server
Restructured the project into `front-end` and `back-end` directories. Set up a separate Docker container running Nginx to serve a cloned static front-end web page on port 9000. 

### Task 3: Connecting Front-end and Back-end
Integrated the two services. Added `flask-cors` to the back-end API to allow Cross-Origin Resource Sharing. Updated the front-end's HTML/JS to make an AJAX request to the API and dynamically display the returned text.

### Task 4: Docker Compose
Simplified the orchestration of multiple containers. Created a `docker-compose.yml` file to build and spin up both the front-end and back-end services simultaneously, ensuring proper startup order with `depends_on`.

### Task 5: Reverse Proxy Server
Introduced an Nginx reverse proxy server as the single entry point. Configured it to route traffic from the root `/` to the front-end service and `/api` to the back-end service. Closed off external access to the individual services so all traffic is securely routed through the proxy on port 80.

### Task 6: Horizontal Scaling
Scaled the infrastructure to handle high traffic spikes. Modified the deployment command to spin up two instances of the API server (`docker-compose up --scale back-end=2`). Docker's internal networking combined with our Nginx proxy effectively load-balances requests across both back-end servers using the Round Robin algorithm.

## Requirements
- Docker Desktop installed on your local computer.
