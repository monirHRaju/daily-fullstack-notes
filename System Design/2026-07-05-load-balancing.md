# Load Balancing in System Design

## Overview
Load balancing is a critical technique in distributed systems that distributes incoming network traffic across multiple servers to ensure no single server becomes overwhelmed, thereby improving responsiveness and availability. It acts as a reverse proxy, routing client requests to appropriate backend servers based on various algorithms.

## Why It Matters
In modern web applications, handling high traffic volumes while maintaining low latency and high availability is essential. Load balancing helps achieve:
- **Scalability**: Enables horizontal scaling by adding more servers to the pool.
- **High Availability**: If one server fails, the load balancer redirects traffic to healthy servers.
- **Performance**: Optimizes resource utilization and reduces response times.
- **Fault Tolerance**: Detects unhealthy servers and removes them from rotation.

## Real-world Usage
- **Web Applications**: Distributing HTTP/HTTPS traffic across web servers (e.g., using NGINX, HAProxy, or cloud load balancers like AWS ELB).
- **API Gateways**: Managing traffic to microservices, ensuring each service instance handles a fair share of requests.
- **Databases**: Distributing read queries across read replicas in database clusters.
- **Gaming Services**: Handling millions of concurrent players by distributing game server connections.

## Code Example
Below is a simple NGINX configuration demonstrating round-robin load balancing for a web application:

```nginx
http {
    upstream backend {
        server web1.example.com;
        server web2.example.com;
        server web3.example.com;
    }

    server {
        listen 80;
        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

This configuration defines an upstream group `backend` with three servers. NGINX will distribute incoming requests to these servers in a round-robin manner.

## Common Mistakes
1. **Ignoring Health Checks**: Without health checks, traffic may be sent to downed servers, causing errors.
2. **Misusing Sticky Sessions**: Overusing IP-based persistence can lead to uneven load distribution; consider using application-level session stores (e.g., Redis) instead.
3. **Single Point of Failure**: Using a single load balancer without redundancy creates a bottleneck; deploy multiple load balancers with failover (e.g., using VRRP or cloud solutions).
4. **Wrong Algorithm Selection**: Choosing an algorithm that doesn't match the workload (e.g., round-robin for servers with vastly different capacities) can degrade performance.
5. **Neglecting SSL Termination**: Not offloading SSL/TLS decryption to the load balancer increases backend server load unnecessarily.

## Interview Question
**Question**: Explain the difference between Layer 4 and Layer 7 load balancing. When would you choose each?

**Answer**: 
- **Layer 4 (Transport Layer)**: Operates at the TCP/UDP level, making routing decisions based on IP addresses and port numbers. It's faster and suitable for simple traffic distribution where content inspection isn't needed (e.g., gaming, VoIP).
- **Layer 7 (Application Layer)**: Operates at the HTTP level, allowing routing based on request headers, URLs, cookies, etc. It enables advanced features like content-based routing, SSL termination, and web application firewall (WAF) integration. Choose Layer 7 when you need to inspect or modify HTTP traffic (e.g., routing API requests to different microservices based on path).

## Key Takeaways
- Load balancing is essential for scalable, highly available systems.
- Select the appropriate algorithm (round-robin, least connections, IP hash) based on traffic patterns and server capabilities.
- Always implement health checks to automatically remove unhealthy servers from the pool.
- Consider Layer 7 load balancing for HTTP-based applications when advanced routing or SSL termination is required.
- Deploy load balancers in pairs or use managed services to avoid a single point of failure.