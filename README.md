# 🛒 E-Commerce Microservices (Spring Boot + PostgreSQL)

A fully backend-only **E-Commerce Microservice System** built using
**Spring Boot**, **Spring Cloud**, **PostgreSQL**, and **Kafka**
(optional but recommended).\
This system demonstrates intermediate-level microservice architecture,
inter-service communication, distributed design, and clean REST APIs ---
**without any frontend**.

## 📌 Architecture Overview

This project consists of the following microservices:

  -----------------------------------------------------------------------
  Service                     Description
  --------------------------- -------------------------------------------
  **API-Gateway**             Entry point for all clients, handles
                              routing & authentication.

  **Discovery Server          Service registry so microservices can
  (Eureka)**                  discover each other.

  **Product Service**         Manages products, categories, inventory
                              info. Uses PostgreSQL.

  **Inventory Service**       Checks and updates stock. Uses PostgreSQL.

  **Order Service**           Creates orders, coordinates with Product &
                              Inventory services. Uses PostgreSQL.

  **Payment Service**         Mock payment processing.

  **Notification Service**    Sends order confirmation notifications
                              (async / Kafka).
  -----------------------------------------------------------------------

Communication Types: - **Sync:** REST calls between Product → Inventory
→ Order\
- **Async:** Order → Notification using Kafka events

## 🧰 Tech Stack

### Backend

-   Java 17+
-   Spring Boot 3.x
-   Spring Cloud (Eureka, Gateway, OpenFeign)
-   Spring Security (JWT)
-   Spring Data JPA
-   MapStruct
-   Lombok

### Database

-   PostgreSQL (one DB per service)

### Messaging

-   Kafka (optional, for Notification events)

## 📁 Project Structure

    ecommerce-microservices/
    ├── api-gateway/
    ├── discovery-server/
    ├── product-service/
    ├── inventory-service/
    ├── order-service/
    ├── payment-service/
    └── notification-service/

## 🗄️ Database Setup (PostgreSQL)

Create databases:

    CREATE DATABASE product_db;
    CREATE DATABASE inventory_db;
    CREATE DATABASE order_db;
    CREATE DATABASE payment_db;
    CREATE DATABASE notification_db;

Each service updates its `application.yml`:

    spring:
      datasource:
        url: jdbc:postgresql://localhost:5432/product_db
        username: postgres
        password: yourpassword

## 🚀 How to Run (Without Docker)

1.  Start Discovery Server\
2.  Start API Gateway\
3.  Start Product, Inventory, Order, Payment services\
4.  Start Notification Service (if using Kafka)

## ✨ Features

### Product Service

-   CRUD products\
-   Categories\
-   Stock check

### Inventory Service

-   Check available stock\
-   Reduce stock atomically

### Order Service

-   Create orders\
-   Validate stock\
-   Kafka event publishing

### Payment Service

-   Mock payments

### Notification Service

-   Kafka listener

## 🛌 Sample API Endpoints

### Product

    GET /api/products
    GET /api/products/{id}
    POST /api/products

### Inventory

    GET /api/inventory/{productId}
    POST /api/inventory/reduce

### Order

    POST /api/orders
    GET /api/orders/{id}

## 🧪 Testing

Run:

    ./mvnw test

## 🛠️ Build

    ./mvnw clean package

## 📜 License

MIT
