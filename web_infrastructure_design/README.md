# Web Infrastructure Design

This project documents the process of designing, scaling, securing, and monitoring the infrastructure for hosting the website www.foobar.com, based on progressive improvements through four design stages.

## Simple Web Stack

The initial design consisted of a single server hosting all core components:

- A web server (Nginx) to serve static content and handle HTTP requests.

- An application server managing the business logic and rendering dynamic content.

- Application files containing the codebase.

- A MySQL database to store persistent data.

- The domain foobar.com was configured with a www A record pointing to IP 8.8.8.8.

This setup works but has several limitations: it presents a Single Point of Failure (SPOF), can't scale to high traffic, and makes maintenance risky since downtime would affect the entire service.

## Distributed Web Infrastructure

To address the limitations of the single-server model, the architecture was expanded into a distributed design with high availability:

- A load balancer (HAProxy) was added to distribute incoming traffic across two backend servers.

Both backend servers host the same components:

- Nginx web server

- Application server

- The application codebase

- A local MySQL database instance

A master-replica database cluster was introduced to ensure read redundancy.

This version improves scalability and reduces SPOFs at the application level. However, it lacked firewalls, HTTPS encryption, and monitoring capabilities.

## Secured and Monitored Infrastructure

To improve security and observability:

- Each of the three main servers (load balancer, backend servers) was protected with its own firewall.

- HTTPS was enabled with an SSL certificate, ensuring encrypted traffic to and from www.foobar.com.

- Monitoring agents were installed on each server to collect system and application metrics, using a monitoring service (e.g., Sumologic).

- The system was configured to monitor key indicators such as server health and QPS (Queries Per Second) on the web servers.

This iteration increases reliability, protects sensitive data in transit, and provides visibility into the system’s performance. However, there were still some architectural risks, such as SSL termination at the load balancer level and having only one write-enabled database node.

## Scaled Infrastructure with Dedicated Components
For further scalability and clean separation of concerns:

- A third server was added.

- A second HAProxy load balancer was configured in cluster mode with the first, enabling load balancer redundancy.

Infrastructure was modularized:

- One server exclusively runs the web server (Nginx)

- Another runs only the application server

- A separate database server hosts the master node

- A new, dedicated replica server was introduced to offload read queries

Each component now resides on its own server, allowing better scaling and maintainability, while also complying with high-availability practices.


