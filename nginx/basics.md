# Nginx #
## Introduction to Nginx ##
- Nginx is a robust and efficient web server that is widely used in modern web infrastructure.
- Beyond serving static web pages, Nginx is versatile and can be configured to perform a variety of roles, including load balancing, HTTP caching, and acting as a reverse proxy. These capabilities make it a cornerstone in many complex web architectures.
- Unlike traditional web servers that use a multi-threaded architecture, Nginx employs a non-threaded, event-driven architecture. This design allows Nginx to handle many more connections simultaneously, making it highly suitable for high-traffic websites.
**Explanation**
Sure! Let's break it down:

### Traditional Web Servers (Multi-threaded architecture):
Imagine you're at a restaurant, and the waiter is like the web server. If the restaurant is busy with many customers, the waiter needs to take care of each customer one by one. The waiter might have several helpers (threads), but each helper can only handle one customer at a time. So, if there are a lot of customers, some people might have to wait for help, and things can slow down.

This is how **traditional web servers** work: They handle each request (like a customer’s order) one at a time with separate workers (threads), and if there are too many customers, things can get slower because the threads are busy.

### Nginx (Non-threaded, event-driven architecture):
Now imagine a different restaurant with a more efficient system. Instead of waiting for the waiter to serve each customer individually, there’s a smart system where the waiter can keep track of all the customers' orders at once, without having to take a break in between. The waiter doesn't need to wait for each task to finish before moving to the next one. The waiter can handle many orders simultaneously by simply switching between them, without being tied up with just one customer at a time.

This is how **Nginx** works. Instead of using multiple threads that can each only handle one request at a time, Nginx uses an "event-driven" approach. It can handle many requests **at once**, without needing multiple threads for each one. It’s more efficient because it doesn't waste time waiting around and can serve many customers (or handle many website visitors) at the same time.

### Why is this important?
- **High Traffic**: For websites with a lot of visitors, like popular news websites or online stores, this "event-driven" system helps the server handle many visitors without slowing down.
- **Efficiency**: Nginx uses fewer resources (like memory and processing power), allowing it to manage more visitors with less effort, making it faster and more scalable than traditional servers.

In short, Nginx is like a super-efficient waiter who can manage hundreds of orders at the same time, while traditional web servers might need multiple waiters working in parallel to do the same job.

## Forward Proxy vs. Reverse Proxy
To understand Nginx’s role as a reverse proxy, it’s helpful to first distinguish between a forward proxy and a reverse proxy.

**Forward Proxy:** 
- In a traditional HTTP connection, a client sends a request directly to a server. However, with a forward proxy (such as a VPN), the client sends the request to the proxy, which then forwards it to the server. 
- The server is unaware of the original client; it only interacts with the proxy. This setup is useful for privacy and bypassing geo-restrictions.

**Reverse Proxy:** 
- In contrast, a reverse proxy sits between the client and multiple servers. The client sends a request to the reverse proxy, which then decides which server should handle the request. 
- The client remains unaware of which specific server processes the request. Nginx is a popular choice for a reverse proxy because of its efficiency and flexibility.

**For example:**
/admin requests could be routed to Server 1.
/settings requests could be routed to Server 2.

- Nginx can handle these types of routing efficiently, ensuring that the correct server processes each request based on predefined rules.

### Advantages of Nginx
Nginx provides several key advantages that make it a preferred choice for many web projects:

- **High Concurrency:** Capable of handling 10,000+ concurrent requests.
- **HTTP Caching:** Can cache HTTP requests, reducing server load and improving response times.
- **Reverse Proxy:** Acts as a reverse proxy, routing client requests to the appropriate server.
- **Load Balancing:** Distributes incoming requests across multiple servers to balance the load.
- **API Gateway:** Can act as an API gateway, managing and routing API requests.
- **Static File Serving:** Efficiently serves and caches static files like images and videos.
- **SSL Termination:** Handles SSL certificates, providing secure connections to users.

## How to install Nginx

```
sudo apt update
sudo apt install nginx
service nginx start
service nginx status
```
## Nginx Configuration basics

The main configuration file for Nginx is nginx.conf. 
- It's typically located in /etc/nginx/nginx.conf. Let's break down a simple Nginx configuration file:

```
worker_processes auto;

events {
    worker_connections 1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }
    }
}
```

Let’s understand each part of this file:

- **worker_processes auto;**

This directive tells Nginx how many worker processes to start. Worker processes are responsible for handling requests. Setting it to auto allows Nginx to automatically determine the optimal number based on your CPU cores.

- **events { worker_connections 1024; }**

The events block sets the connection handling settings.

**worker_connections 1024;** means that each worker process can handle 1024 simultaneous connections. If you have 4 worker processes, you can handle 4096 connections concurrently.

- **http { ... }**

The http block is where you define settings for handling HTTP requests. This is where most of your web server's configuration will reside.
include mime.types;

This directive tells Nginx to include a file called mime.types, which maps file extensions to MIME types. MIME types tell the browser how to handle different types of files (e.g., .html as text/html, .jpg as image/jpeg).

- **default_type application/octet-stream;**

If Nginx can’t determine the MIME type of a file, it will use application/octet-stream as a fallback, which generally means "download this file."
sendfile on;

This directive enables the sendfile system call, which allows Nginx to serve files more efficiently by bypassing the need to copy data between buffers.

- **keepalive_timeout 65;**

This setting determines how long a connection should stay open (in seconds) when it’s idle, before Nginx closes it. The default is 65 seconds.
Now let’s talk about the server block which is most important part of the file:

```
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
server { ... }
```

The server block defines the configuration for a virtual server, i.e., a server that will handle requests for a particular domain or IP address.
- **listen 80;**
This tells Nginx to listen for HTTP requests on port 80. Port 80 is the default port for HTTP.

- **server_name localhost;**
The server_name directive specifies the domain name that this server block will respond to. localhost is typically used for local testing, but in a real-world scenario, you'd replace it with your domain name (e.g., example.com).

- **location / { ... }**
The location block is used to define how to respond to different request URIs. The / means that this block will handle requests to the root URL (e.g., http://localhost/).

- **root /usr/share/nginx/html;**
The root directive specifies the directory where Nginx should look for files to serve. Here, it's set to /usr/share/nginx/html, which is the default directory where Nginx stores HTML files.

- **index index.html index.htm;**
The index directive lists the files Nginx should look for if a directory is requested. For example, if a user navigates to http://localhost/, Nginx will try to serve index.html or index.htm from the root directory.


## Configuring Nginx
Once Nginx is installed, a directory is created at /etc/nginx/. The most important file in this directory is nginx.conf, which contains the main configuration for Nginx.

First, let’s back up the original nginx.conf:

```
mv /etc/nginx/nginx.conf /etc/nginx/nginx-backup.conf
```

Now, you can create a new nginx.conf file using a text editor like vim:

```
vim /etc/nginx/nginx.conf
```

In this file, you’ll define your Nginx configuration. Here’s a simple example:

```
events {
}

http {
    server {
        listen 80;
        server_name _;  # This means it will respond to any server name
        location / {
            return 200 "Hello from Nginx Conf File";
        }
    }
}
```

After editing the configuration, you need to reload Nginx to apply the changes:

```nginx -s reload```

## Reverse Proxy with Nginx
A reverse proxy sits between a client and a backend server, forwarding client requests to the backend and sending the backend’s response back to the client. Here’s how you configure Nginx as a reverse proxy:

```
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend_server;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

- **proxy_pass http://backend_server;:** This tells Nginx to forward requests to the backend server.
- **proxy_set_header Host $host;:** Ensures the correct Host header is passed to the backend.
- **proxy_set_header X-Real-IP $remote_addr;:** Passes the client’s real IP address to the backend.
- **proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;:** Adds the client’s IP to the X-Forwarded-For header, which is used by the backend to identify the original client IP.

## Load Balancing with Nginx
Nginx can distribute traffic among multiple backend servers to balance the load.

```
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
}

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend;
    }
}
```

- **upstream backend { ... }:** Defines a group of backend servers.
- **server backend1.example.com;:** Adds a server to the backend group.
- **proxy_pass http://backend;:** Forwards requests to one of the servers in the backend group.


## Securing Nginx with SSL/TLS
SSL/TLS encrypts the data between your server and clients. Here’s how to configure SSL in Nginx:

```
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /etc/nginx/ssl/example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/example.com.key;

    location / {
        root /var/www/html;
        index index.html;
    }
}

server {
    listen 80;
    server_name example.com;
    return 301 https://$host$request_uri;
}
```

- **listen 443 ssl;:** Tells Nginx to listen on port 443 (the default port for HTTPS) with SSL enabled.
- **ssl_certificate and ssl_certificate_key:** Specifies the paths to your SSL certificate and private key.
- **return 301 https://$host$request_uri;:** Redirects all HTTP requests to HTTPS.



