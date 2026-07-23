<img width="1432" height="1080" alt="Screenshot 2026-07-23 220612" src="https://github.com/user-attachments/assets/996cd9f8-7fc7-4636-a778-0fc5d22d19e0" /># Satellite_Telemetry_platform

A backend microservices-based platform for managing satellite information, telemetry data, automated alerts, and maintenance records.

The project is built using **Spring Boot** and follows a microservices architecture where each service handles a specific business responsibility. An **API Gateway** provides a single entry point for external clients, while internal service communication is handled using **RestTemplate**.

---

## 📌 Overview

The platform consists of the following independent services:

* 🛰️ Satellite Service
* 📡 Telemetry Service
* 🚨 Alert Service
* 🔧 Maintenance Service
* 🌐 API Gateway

### Key Features

* CRUD operations
* Request validation using Jakarta Bean Validation
* Centralized exception handling
* DTO-based API design
* Entity-to-DTO mapping
* Structured logging using SLF4J and Lombok `@Slf4j`
* API Gateway routing
* RestTemplate-based inter-service communication
* Automatic alert generation from abnormal telemetry
* Independent H2 databases for each service

---

## 🏗️ System Architecture

```text
                         ┌──────────────────┐
                         │      Client      │
                         │     Postman      │
                         └────────┬─────────┘
                                  │
                                  ▼
                         ┌──────────────────┐
                         │   API Gateway    │
                         │     Port 8080    │
                         └────────┬─────────┘
                                  │
        ┌─────────────────────────┼─────────────────────────┐
        │                         │                         │
        ▼                         ▼                         ▼
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│ Satellite       │      │ Telemetry       │      │ Alert           │
│ Service         │      │ Service         │      │ Service         │
│ Port: 8081      │      │ Port: 8082      │      │ Port: 8083      │
└─────────────────┘      └────────┬────────┘      └─────────────────┘
                                  │
                                  │ RestTemplate
                                  ▼
                         ┌─────────────────┐
                         │ Alert Generation│
                         └─────────────────┘

                         ┌─────────────────┐
                         │ Maintenance     │
                         │ Service         │
                         │ Port: 8084      │
                         └─────────────────┘
```

---

## 🌐 API Gateway

**Port:** `8080`

The API Gateway acts as the single entry point for external clients and routes requests to the appropriate microservice.

| Request Path      | Target Service      | Port |
| ----------------- | ------------------- | ---- |
| `/satellites/**`  | Satellite Service   | 8081 |
| `/telemetry/**`   | Telemetry Service   | 8082 |
| `/alerts/**`      | Alert Service       | 8083 |
| `/maintenance/**` | Maintenance Service | 8084 |

Example:

```text
POST http://localhost:8080/satellites
```

The request is internally routed to the Satellite Service running on port `8081`.

---

## 🛰️ Satellite Service

**Port:** `8081`

Manages satellite information and provides complete CRUD functionality.

### Endpoints

| Method | Endpoint                    |
| ------ | --------------------------- |
| POST   | `/satellites`               |
| GET    | `/satellites`               |
| GET    | `/satellites/{satelliteId}` |
| PUT    | `/satellites/{satelliteId}` |
| DELETE | `/satellites/{satelliteId}` |

Example satellite data:

```json
{
  "name": "INSAT-1",
  "missionName": "Communication Mission",
  "launchDate": "2024-01-15",
  "orbitType": "Geostationary",
  "status": "ACTIVE"
}
```

---

## 📡 Telemetry Service

**Port:** `8082`

Manages telemetry data received from satellites, including:

* Battery level
* Temperature
* Signal strength
* Altitude
* Timestamp

### Endpoints

| Method | Endpoint                             |
| ------ | ------------------------------------ |
| POST   | `/telemetry`                         |
| GET    | `/telemetry`                         |
| GET    | `/telemetry/{telemetryId}`           |
| GET    | `/telemetry/satellite/{satelliteId}` |
| PUT    | `/telemetry/{telemetryId}`           |
| DELETE | `/telemetry/{telemetryId}`           |

The service checks telemetry values and communicates with the Alert Service when abnormal conditions are detected.

---

## 🚨 Alert Service

**Port:** `8083`

Manages alerts generated from abnormal satellite telemetry.

### Alert Conditions

| Condition                | Alert                 |
| ------------------------ | --------------------- |
| Battery below 20%        | LOW BATTERY           |
| Temperature above 45°C   | OVERHEAT              |
| Signal strength below 50 | COMMUNICATION FAILURE |

The service also provides CRUD operations for managing alerts.

---

## 🔧 Maintenance Service

**Port:** `8084`

Manages maintenance records for satellites.

### Endpoints

| Method | Endpoint                               |
| ------ | -------------------------------------- |
| POST   | `/maintenance`                         |
| GET    | `/maintenance`                         |
| GET    | `/maintenance/{maintenanceId}`         |
| GET    | `/maintenance/satellite/{satelliteId}` |
| PUT    | `/maintenance/{maintenanceId}`         |
| DELETE | `/maintenance/{maintenanceId}`         |

---

## 🔄 Inter-Service Communication

The Telemetry Service communicates with the Alert Service using `RestTemplate`.

```text
Client
  ↓
API Gateway
  ↓
Telemetry Service
  ↓
RestTemplate
  ↓
Alert Service
  ↓
Alert Created
```

For example, when the battery level falls below `20%`, the Telemetry Service automatically sends an alert request to the Alert Service.

---

## 🗄️ Database Architecture

Each microservice uses its own independent H2 in-memory database.

| Service             | Database |
| ------------------- | -------- |
| Satellite Service   | H2       |
| Telemetry Service   | H2       |
| Alert Service       | H2       |
| Maintenance Service | H2       |

This maintains service independence and follows the microservices principle of separating data ownership.

---

## 🧱 Project Structure

Each business service follows a layered architecture:

```text
src/main/java/com/example/service
├── controller
├── service
├── repository
├── entity
├── dto
├── mapper
├── exception
└── config
```

The API Gateway contains its routing configuration in:

```text
src/main/resources/application.yml
```

---

## 🛠️ Technologies Used

* Java
* Spring Boot
* Spring Cloud Gateway
* Spring Web
* Spring Data JPA
* Hibernate
* H2 Database
* Lombok
* Jakarta Bean Validation
* RestTemplate
* Maven
* Postman
* IntelliJ IDEA

---

## ▶️ How to Run

Start the services in the following order:

1. Satellite Service — `8081`
2. Telemetry Service — `8082`
3. Alert Service — `8083`
4. Maintenance Service — `8084`
5. API Gateway — `8080`

After starting all services, use the API Gateway as the main entry point:

```text
http://localhost:8080
```

---

## 📝 Logging and Exception Handling

The project uses Lombok's `@Slf4j` for structured application logging.

Logging is used for:

* Service operations
* Telemetry processing
* Alert generation
* Inter-service communication
* Errors and exceptional conditions

Centralized exception handling provides consistent responses for:

* Resource not found
* Invalid request data
* Duplicate records
* Database errors
* Unexpected application errors

---

## 📸 Final Project Demonstration

The following screenshots provide visual evidence of the main project functionality:

* API Gateway routing and Satellite creation
* Telemetry creation through the API Gateway
* Automatic alert generation
* Maintenance record creation
* Application logs showing telemetry processing and service communication


---
## Output
Satellite service:
<img width="1428" height="1077" alt="Screenshot 2026-07-23 220011" src="https://github.com/user-attachments/assets/633e78ee-5f14-46cf-ac59-06127737c6a5" />

Telemetry service:
<img width="1425" height="1092" alt="Screenshot 2026-07-23 221225" src="https://github.com/user-attachments/assets/3392ab41-f59e-4ded-889c-1370b297171e" />

Alert Service:
<img width="1432" height="1080" alt="Screenshot 2026-07-23 220612" src="https://github.com/user-attachments/assets/9a180aeb-036f-4cff-9d2a-f1a01bf4eb15" />

Maintainace Service:
<img width="1100" height="692" alt="Screenshot 2026-07-23 222321" src="https://github.com/user-attachments/assets/561bb6d8-ebf5-44cb-be3d-8d9b305b9283" />


## 👨‍💻 Author

**Santhosh V **

B.Tech Artificial Intelligence and Data Science

---

## ⭐ Conclusion

The Satellite Management Microservices Platform demonstrates the development of a complete backend system using Spring Boot and microservices architecture.

The project showcases independent business services, API Gateway routing, CRUD operations, request validation, centralized exception handling, H2 database integration, structured logging, RestTemplate-based communication, and automatic alert generation based on satellite telemetry data.
