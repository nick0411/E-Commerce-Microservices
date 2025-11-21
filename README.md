# 🛒 E-Commerce Microservices (Spring Boot + PostgreSQL)

A fully backend-only **E-Commerce Microservice System** built using **Spring Boot**, **Spring Cloud**, **PostgreSQL**, and **Kafka** (optional but recommended).  
This system demonstrates intermediate-level microservice architecture, inter-service communication, distributed design, and clean REST APIs — **without any frontend**.

---

## 📌 **Architecture Overview**

This project consists of the following microservices:

| Service | Description |
|--------|-------------|
| **API-Gateway** | Entry point for all clients, handles routing & authentication. |
| **Discovery Server (Eureka)** | Service registry so microservices can discover each other. |
| **Product Service** | Manages products, categories, inventory info. Uses PostgreSQL. |
| **Inventory Service** | Checks and updates stock. Uses PostgreSQL. |
| **Order Service** | Creates orders, coordinates with Product & Inventory services. Uses PostgreSQL. |
| **Payment Service** | Mock payment processing. |
| **Notification Service** | Sends order confirmation notifications (async / Kafka). |

Communication Types:
- **Sync:** REST calls between Product → Inventory → Order  
- **Async:** Order → Notification using Kafka events

---

## 🧰 **Tech Stack**

### **Backend**
- Java 17+
- Spring Boot 3.x
- Spring Cloud (Eureka, Gateway, OpenFeign)
- Spring Security (JWT)
- Spring Data JPA
- MapStruct
- Lombok

### **Database**
- PostgreSQL (one DB per service)

### **Messaging**
- Kafka (optional, for Notification events)

---

## 📁 Project Structure

