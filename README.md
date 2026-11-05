# CST8915 Lab 2 — Algonquin Pet Store Across Four Azure VMs

**Student name:** BASHIRU TAJUDEEN BASHIRU  
**Student ID:** 041289261  
**Course:** CST8915 Full-stack Cloud-native Development  
**Semester:** Fall 2026

## Demo Video

[Watch my Lab 2 demonstration](https://youtu.be/hrTcSYCwXq4)

## Service Repositories

- [Order Service](https://github.com/tajudeenbash/order-service-lab2)
- [Product Service](https://github.com/tajudeenbash/product-service-lab2)
- [Store Front](https://github.com/tajudeenbash/store-front-lab2)

## Deployment

Each component runs on its own Azure virtual machine.

| Component | VM name | Public IP | Application port |
|---|---|---|---|
| Store Front | lab2-storefront-vm | 74.152.32.190 | 8080 |
| Order Service | lab2-order-vm | 20.88.42.198 | 3000 |
| Product Service | lab2-product-vm | 74.152.48.1 | 3030 |
| RabbitMQ | lab2-rabbitmq-vm | 20.80.53.74 | 5672 |

The browser loads products from the Product Service and sends orders to
the Order Service. The Order Service publishes orders to RabbitMQ on
its dedicated VM.

During testing, placing an order through the Store Front increased
the durable `order_queue` message count from 1 to 2.

## Reflections

### 1. Configuration and Backing Services

I applied the supplied Lab 2 replacements so the Order Service reads
its listening port and RabbitMQ connection string from environment
variables, while the Product Service reads its port from an environment
variable. I configured the Order Service through a local `.env` file
and started the Product Service with `PORT=3030`. RabbitMQ runs on a
separate VM and is accessed through a configured URL using an application
account. This allows the broker address or credentials to change through
configuration without changing the application code.

### 2. Why use environment variables?

Environment variables allow the same application code to run with
different settings on different machines. In this lab, I could configure
ports and the RabbitMQ endpoint for the Azure deployment without
hard-coding them into the source. Keeping credentials in deployment
configuration also helps keep them out of Git repositories. Environment
variables still require careful handling, so I restricted access to the
backend `.env` file and kept credentials out of the README.

### 3. Why use separate repositories?

Separate repositories give each microservice its own source history,
dependencies, and release process. A change to the Product Service can
be tested and deployed independently of the Order Service or Store
Front. This supports independent maintenance and deployment. It also
makes it easier to manage changes needed to scale one service, although
running additional instances still requires deployment and networking
configuration.

## Setup Challenges

I resolved SSH private-key permission errors on Windows and configured
Azure inbound rules for the required ports. I also corrected the RabbitMQ
connection-string format and credentials, then verified delivery by
checking the queue count. VM-size availability required checking the
subscription's permitted regions and deploying the Product Service and
Store Front in Belgium Central.
