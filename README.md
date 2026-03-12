# Patient Management System

A comprehensive microservices-based healthcare management system built with Spring Boot, featuring patient records, billing, analytics, authentication, and API gateway.

## 🏗️ Architecture

```
┌─────────────┐
│   Clients   │
└──────┬──────┘
       │
┌──────▼──────────────────┐
│   API Gateway (4004)    │
└──────┬──────────────────┘
       │
       ├────────────┬─────────────┬──────────────┬─────────────┐
       │            │             │              │             │
┌──────▼──────┐ ┌──▼────────┐ ┌──▼─────────┐ ┌─▼──────────┐ │
│  Patient    │ │  Billing  │ │ Analytics  │ │   Auth     │ │
│  Service    │ │  Service  │ │  Service   │ │  Service   │ │
│  (4000)     │ │  (8081)   │ │  (8082)    │ │  (8083)    │ │
└──────┬──────┘ └──────┬────┘ └──────┬─────┘ └─────┬──────┘ │
       │               │              │             │        │
       │               │              │             │        │
   ┌───▼───┐      ┌────▼─────┐   ┌───▼────┐   ┌────▼────┐  │
   │PostgreSQL│     │  gRPC   │   │ Kafka  │   │PostgreSQL│  │
   │ (5432) │     │ (9001)  │   │ (9092) │   │ (5432)  │  │
   └────────┘      └──────────┘   └────────┘   └─────────┘  │
                                                             │
                                      Swagger UI ────────────┘
```

## 🚀 Services

### 1. **API Gateway** (Port 4004)
- Central entry point for all client requests
- Routes requests to appropriate microservices
- Built with Spring Cloud Gateway

### 2. **Patient Service** (Port 4000)
- Manages patient records (CRUD operations)
- Stores patient demographics and medical information
- REST API + gRPC server (Port 9090)
- PostgreSQL database

### 3. **Billing Service** (Port 8081)
- Handles billing account creation
- Integrates with Patient Service via gRPC
- Creates billing accounts when patients are registered
- gRPC server (Port 9001)

### 4. **Analytics Service** (Port 8082)
- Processes real-time patient events via Kafka
- Provides analytics and reporting
- Kafka consumer for patient events

### 5. **Auth Service** (Port 8083)
- User authentication and authorization
- JWT token generation and validation
- Secure password hashing with BCrypt
- PostgreSQL database for user management

## 📋 Prerequisites

- **Docker** and **Docker Compose**
- **Java 21** (for local development)
- **Maven 3.9+** (for local development)
- **Git**

## 🛠️ Technology Stack

### Backend
- **Spring Boot 3.4.1** - Application framework
- **Spring Cloud Gateway** - API Gateway
- **Spring Data JPA** - Database access
- **Spring Security** - Authentication & Authorization
- **Spring Kafka** - Event streaming

### Communication
- **gRPC** - Inter-service communication
- **Apache Kafka 3.9.0** - Event streaming platform
- **REST** - Client-facing APIs

### Database
- **PostgreSQL 16** - Primary database
- **H2** - In-memory database (development/testing)

### Security
- **JWT (JSON Web Tokens)** - Token-based authentication
- **BCrypt** - Password hashing

### Documentation
- **SpringDoc OpenAPI 3** - API documentation (Swagger)

### Build & Deployment
- **Maven** - Dependency management
- **Docker** - Containerization
- **Docker Compose** - Multi-container orchestration

## 📦 Quick Start

### 1. Clone the Repository

```bash
git clone 
cd patient-management
```

### 2. Start All Services

```bash
docker-compose up -d
```

This will start:
- PostgreSQL database
- Kafka message broker
- All microservices
- API Gateway

### 3. Verify Services are Running

```bash
docker-compose ps
```

Expected output:
```
NAME                STATUS              PORTS
postgres            Up                  0.0.0.0:5432->5432/tcp
kafka               Up                  0.0.0.0:9092->9092/tcp
patient-service     Up                  0.0.0.0:4000->4000/tcp, 0.0.0.0:9090->9090/tcp
billing-service     Up                  0.0.0.0:8081->8081/tcp, 0.0.0.0:9001->9001/tcp
analytics-service   Up                  0.0.0.0:8082->8082/tcp
auth-service        Up                  0.0.0.0:8083->8083/tcp
api-gateway         Up                  0.0.0.0:4004->4004/tcp
```

### 4. Access Services

- **API Gateway**: http://localhost:4004
- **Patient Service**: http://localhost:4000
- **Patient Service Swagger**: http://localhost:4000/swagger-ui.html
- **Billing Service**: http://localhost:8081
- **Analytics Service**: http://localhost:8082
- **Auth Service**: http://localhost:8083

## 🔑 API Documentation

### Authentication Endpoints

#### Register User
```bash
POST http://localhost:4004/api/auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123",
  "name": "John Doe",
  "role": "PATIENT"
}
```

#### Login
```bash
POST http://localhost:4004/api/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123"
}

Response:
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiresIn": 36000000
}
```

### Patient Endpoints (via Gateway)

#### Create Patient
```bash
POST http://localhost:4004/api/patients
Content-Type: application/json
Authorization: Bearer 

{
  "name": "John Doe",
  "email": "john.doe@example.com",
  "address": "123 Main St, City, State",
  "dateOfBirth": "1990-01-15",
  "registeredDate": "2026-02-15"
}
```

#### Get All Patients
```bash
GET http://localhost:4004/api/patients
Authorization: Bearer 
```

#### Get Patient by ID
```bash
GET http://localhost:4004/api/patients/{id}
Authorization: Bearer 
```

#### Update Patient
```bash
PUT http://localhost:4004/api/patients/{id}
Content-Type: application/json
Authorization: Bearer 

{
  "name": "John Updated",
  "email": "john.updated@example.com",
  "address": "456 New St",
  "dateOfBirth": "1990-01-15",
  "registeredDate": "2026-02-15"
}
```

#### Delete Patient
```bash
DELETE http://localhost:4004/api/patients/{id}
Authorization: Bearer 
```

## 🔧 Configuration

### Environment Variables

Create a `.env` file in the root directory:

```env
# Database
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=patientdb

# JWT
JWT_SECRET=my-super-secret-jwt-key-that-is-at-least-256-bits-long
JWT_EXPIRATION=36000000

# Kafka
KAFKA_BOOTSTRAP_SERVERS=kafka:9092

# Service Ports
API_GATEWAY_PORT=4004
PATIENT_SERVICE_PORT=4000
BILLING_SERVICE_PORT=8081
ANALYTICS_SERVICE_PORT=8082
AUTH_SERVICE_PORT=8083
```

### Application Properties

Each service has its own `application.yml`:

**Patient Service** (`patient-service/src/main/resources/application.yml`):
```yaml
server:
  port: 4000

spring:
  application:
    name: patient-service
  datasource:
    url: jdbc:postgresql://postgres:5432/patientdb
    username: postgres
    password: postgres
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true

grpc:
  server:
    port: 9090
```

## 🧪 Testing

### Using cURL

```bash
# Register a user
curl -X POST http://localhost:4004/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"Test123","name":"Test User","role":"PATIENT"}'

# Login
TOKEN=$(curl -X POST http://localhost:4004/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"Test123"}' \
  | jq -r '.token')

# Create a patient
curl -X POST http://localhost:4004/api/patients \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"name":"Jane Doe","email":"jane@example.com","address":"789 Oak St","dateOfBirth":"1985-05-20","registeredDate":"2026-02-15"}'

# Get all patients
curl -X GET http://localhost:4004/api/patients \
  -H "Authorization: Bearer $TOKEN"
```

### Using Postman

1. Import the Postman collection (if available)
2. Set the `baseUrl` variable to `http://localhost:4004`
3. Run the authentication request first to get a token
4. Use the token for subsequent requests

## 🐳 Docker Commands

### Build and Start
```bash
docker-compose up --build -d
```

### Stop All Services
```bash
docker-compose down
```

### View Logs
```bash
# All services
docker-compose logs -f

# Specific service
docker logs patient-service -f
```

### Rebuild Single Service
```bash
docker-compose up --build -d patient-service
```

### Remove All Containers and Volumes
```bash
docker-compose down -v
```

## 📊 Database Management

### Access PostgreSQL
```bash
docker exec -it postgres psql -U postgres
```

### Create Databases
```sql
-- Patient database
CREATE DATABASE patientdb;

-- Auth database
CREATE DATABASE authdb;

-- List databases
\l

-- Connect to database
\c patientdb

-- List tables
\dt

-- Exit
\q
```

## 🔍 Monitoring and Debugging

### Check Service Health
```bash
# Patient Service
curl http://localhost:4000/actuator/health

# Through Gateway
curl http://localhost:4004/api/patients/actuator/health
```

### View Container Stats
```bash
docker stats
```

### Access Container Shell
```bash
docker exec -it patient-service sh
```

## 🏃 Local Development

### Run Service Locally (Without Docker)

1. **Start PostgreSQL and Kafka**:
```bash
docker-compose up -d postgres kafka
```

2. **Update application.yml** (change `postgres` to `localhost`):
```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/patientdb
```

3. **Run the service**:
```bash
cd patient-service
mvn spring-boot:run
```

## 🚨 Troubleshooting

### Service Won't Start

**Problem**: Service fails to start
```bash
# Check logs
docker logs 

# Common issues:
# 1. Port already in use
# 2. Database connection failed
# 3. Missing environment variables
```

**Solution**:
```bash
# Stop conflicting services
docker-compose down

# Rebuild
docker-compose up --build -d
```

### Database Connection Failed

**Problem**: `Cannot connect to database`

**Solution**:
```bash
# Check if PostgreSQL is running
docker ps | grep postgres

# Restart database
docker-compose restart postgres

# Check network
docker network inspect patient-management
```

### Kafka Connection Issues

**Problem**: `Failed to obtain JDBC Connection` or Kafka errors

**Solution**:
```bash
# Restart Kafka
docker-compose restart kafka

# Check Kafka logs
docker logs kafka

# Verify Kafka is healthy
docker exec -it kafka kafka-topics.sh --bootstrap-server localhost:9092 --list
```

## 📝 Development Guidelines

### Code Structure
```
service-name/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/pm/servicename/
│   │   │       ├── controller/
│   │   │       ├── service/
│   │   │       ├── repository/
│   │   │       ├── model/
│   │   │       ├── dto/
│   │   │       ├── mapper/
│   │   │       ├── config/
│   │   │       └── util/
│   │   └── resources/
│   │       ├── application.yml
│   │       └── proto/ (for gRPC services)
│   └── test/
├── Dockerfile
└── pom.xml
```

### Best Practices

1. **Always use DTOs** for API requests/responses
2. **Implement proper error handling** with @ControllerAdvice
3. **Use mappers** to convert between entities and DTOs
4. **Write unit tests** for services and integration tests for controllers
5. **Document APIs** with Swagger annotations
6. **Use environment variables** for configuration
7. **Implement logging** with SLF4J
8. **Validate input** with Bean Validation annotations

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request


## 🎯 Future Enhancements

- [ ] Add Redis caching layer
- [ ] Implement circuit breakers with Resilience4j
- [ ] Add service discovery with Eureka
- [ ] Implement distributed tracing with Zipkin
- [ ] Add Kubernetes deployment manifests
- [ ] Implement rate limiting
- [ ] Add comprehensive integration tests
- [ ] Implement audit logging
- [ ] Add health check dashboards
- [ ] Implement database migrations with Flyway/Liquibase

## 📚 Additional Resources

- [Spring Boot Documentation](https://spring.io/projects/spring-boot)
- [Spring Cloud Gateway](https://spring.io/projects/spring-cloud-gateway)
- [gRPC Documentation](https://grpc.io/docs/)
- [Apache Kafka](https://kafka.apache.org/documentation/)
- [Docker Documentation](https://docs.docker.com/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
