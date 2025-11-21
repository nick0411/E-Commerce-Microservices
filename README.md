# 📦 E-Commerce Microservices (Spring Boot + PostgreSQL + Kafka + Eureka)

**Backend-only, No Frontend**

This project is an **intermediate-level microservices system**
demonstrating real-world backend architecture using **Spring Boot**,
**PostgreSQL**, **Kafka**, **Spring Cloud**, and **JWT authentication**.

Each microservice is fully isolated with its own database, communicates
through REST + Kafka events, and registers with a Eureka discovery
server.

------------------------------------------------------------------------

## 🧱 Architecture Overview

                        ┌───────────────────┐
                        │   API Gateway     │
                        └─────────┬─────────┘
                                  │
                         Service Discovery
                        ┌─────────▼─────────┐
                        │   Eureka Server   │
                        └─────────┬─────────┘
                                  │
                 ┌────────────────┼──────────────────┐
                 │                │                  │
       ┌─────────▼───────┐┌──────▼─────────┐┌───────▼────────┐
       │ Auth Service    ││ Product Service ││ Inventory       │
       │ (JWT, Users)    ││ (Catalog CRUD)  ││ Service         │
       └─────────────────┘└─────────────────┘│ (Stock Mgmt)   │
                                             └─────────┬──────┘
                                                       │ Kafka Events
       ┌─────────────────────┐                         │
       │ Order Service       │<────────────────────────┘
       │ (Saga Workflow)     │──────────────┐
       └─────────────────────┘              │
                                            │ Kafka
                         ┌──────────────────▼───────────┐
                         │     Payment Service           │
                         └──────────────────────────────┘

------------------------------------------------------------------------

## 📚 Services Included

 | Service               | Port | Description                    |
| --------------------- | ---- | ------------------------------ |
| **Eureka Server**     | 8761 | Service registry               |
| **API Gateway**       | 8080 | Routing, filters, JWT relay    |
| **Auth Service**      | 8101 | Login, registration, JWT       |
| **Product Service**   | 8102 | Product catalog                |
| **Inventory Service** | 8103 | Stock management, reservations |
| **Order Service**     | 8104 | Orders + Saga coordination     |
| **Payment Service**   | 8105 | Mock payment processor         |

------------------------------------------------------------------------

## 🛢 Databases

Each microservice uses its **own PostgreSQL database**:

  | Service   | Database Name |
| --------- | ------------- |
| Auth      | auth_db       |
| Product   | product_db    |
| Inventory | inventory_db  |
| Order     | order_db      |
| Payment   | payment_db    |

### Flyway migrations

Located in:

    src/main/resources/db/migration/

Example migration (Product service):

``` sql
CREATE TABLE products (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    sku VARCHAR(64) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    price_cents BIGINT NOT NULL,
    currency VARCHAR(8) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);
```

------------------------------------------------------------------------

# 📨 Kafka Topics (Event-Driven Communication)

  -----------------------------------------------------------------------
  Topic                            Produced by   Consumed by
  -------------------------------- ------------- ------------------------
  `orders.created`                 Order Service Inventory, Payment

  `inventory.reserved`             Inventory     Payment, Order

  `inventory.reservation_failed`   Inventory     Order

  `payment.completed`              Payment       Order

  `orders.confirmed`               Order         Notification (optional)

  `orders.cancelled`               Order         Notification
  -----------------------------------------------------------------------

Event example:

``` json
{
  "orderId": "ae34b5c0-19b2-4fad-b0e6-3c9c28f71c5b",
  "userId": "9f134",
  "totalCents": 12999,
  "items": [
    { "sku": "SKU-123", "qty": 1 }
  ],
  "createdAt": "2025-11-21T10:00:00Z"
}
```

------------------------------------------------------------------------

# ⚙️ How to Run the Project (NO DOCKER)

## 1. **Prerequisites**

Install:

-   Java 17+
-   Apache Kafka
-   Zookeeper
-   PostgreSQL
-   Maven or Gradle

------------------------------------------------------------------------

## 2. **Start PostgreSQL and Create Databases**

Run:

``` sql
CREATE DATABASE auth_db;
CREATE DATABASE product_db;
CREATE DATABASE inventory_db;
CREATE DATABASE order_db;
CREATE DATABASE payment_db;
```

------------------------------------------------------------------------

## 3. **Start Kafka and Zookeeper**

Example:

    zookeeper-server-start.sh zookeeper.properties
    kafka-server-start.sh server.properties

------------------------------------------------------------------------

## 4. **Start Services in Order**

### 1️⃣ Start Eureka Server

    cd eureka-server
    ./mvnw spring-boot:run

### 2️⃣ Start API Gateway

    cd gateway
    ./mvnw spring-boot:run

### 3️⃣ Start each microservice

    cd auth-service && ./mvnw spring-boot:run
    cd product-service && ./mvnw spring-boot:run
    cd inventory-service && ./mvnw spring-boot:run
    cd order-service && ./mvnw spring-boot:run
    cd payment-service && ./mvnw spring-boot:run

------------------------------------------------------------------------

# 🔑 Authentication (JWT)

## Register

    POST /auth/register
    {
      "email": "test@example.com",
      "password": "123456"
    }

## Login

    POST /auth/login

Response:

``` json
{
  "accessToken": "xxx",
  "refreshToken": "yyy"
}
```

------------------------------------------------------------------------

# 🛒 Order Placement Saga Flow

### Step 1: Client creates order

    POST /api/orders

### Step 2: Order Service

-   Saves order as **PENDING**
-   Emits **orders.created**

### Step 3: Inventory Service

-   Reserves stock
-   Emits `inventory.reserved` OR `inventory.reservation_failed`

### Step 4: Payment Service

-   Processes mock payment
-   Emits `payment.completed`

### Step 5: Order Service

-   Confirms or cancels order accordingly

------------------------------------------------------------------------

# 📁 Project Structure (Monorepo)

    ecommerce/
     ├── eureka-server/
     ├── gateway/
     ├── auth-service/
     ├── product-service/
     ├── inventory-service/
     ├── order-service/
     ├── payment-service/
     └── shared/
          ├── dto/
          ├── events/
          ├── kafka/
          └── security/

------------------------------------------------------------------------

# 🧪 Testing

    ./mvnw test

------------------------------------------------------------------------

# 🚀 Future Enhancements

-   Notification service\
-   Outbox pattern\
-   Redis caching\
-   Elasticsearch search\
-   Zipkin/Jaeger tracing
