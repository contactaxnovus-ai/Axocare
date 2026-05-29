# Ticket Service

Ticket Service is a production-grade, cloud-native ticket management microservice built using Java 17 and Quarkus, designed for scalable service management, incident handling, customer support operations, and workflow-driven ticket lifecycle management.

Built with modern event-driven architecture principles, the platform leverages PostgreSQL for transactional persistence, Redpanda/Kafka for real-time event streaming, LocalStack S3 compatible object storage for attachments and documents during local development, Flyway for database versioning, and Docker Desktop for simplified local deployment and development workflows.

### Core Capabilities

* Ticket creation, updates, assignment, escalation, and lifecycle management
* Multi-tenant architecture support
* Event-driven ticket processing and workflow orchestration
* SLA management with configurable business rules
* Ownership tracking, audit history, and activity logs
* File attachments and document management
* Role-based access control and permission management
* REST APIs for integration with external systems
* Search and filtering capabilities for operational scale
* Horizontal scalability for enterprise workloads

### Architecture Highlights

* Java 17 + Quarkus high-performance microservice architecture
* PostgreSQL transactional persistence
* Redpanda / Kafka event streaming architecture
* Flyway database migration management
* Docker-native deployment model
* S3-compatible object storage integration
* Cloud-ready and Kubernetes-friendly deployment architecture

### AI Powered Capabilities (Planned / Supported)

The platform is designed to support AI-assisted service management and intelligent ticket operations including:

* AI-powered ticket classification and categorization
* Agent-assist capabilities for suggested responses and resolutions
* Intelligent duplicate ticket detection
* Natural language ticket summarization
* Conversational AI interfaces for ticket creation and updates
* Predictive SLA breach detection and escalation recommendations
* AI-powered knowledge retrieval and contextual recommendations (RAG-based)
* Anomaly detection for unusual ticket patterns and operational issues

### Example Use Cases

* Customer Support Platforms
* Telecom Service Requests
* Incident Management Systems
* Enterprise Service Management
* Internal IT Support Operations
* Workflow-driven Case Management
* AI-powered Customer Service Platforms

Designed for high-volume, enterprise-scale environments where real-time processing, automation, and intelligent operations are critical.


## What is included

- PostgreSQL container on port `5432`
- Redpanda Kafka-compatible broker on port `19092`
- LocalStack S3-compatible API on port `4566`
- Ticket service container on port `8081`
- Internal Docker network between app, database, broker, and object storage
- Automatic table creation through Flyway
- Soft delete support
- Full update and partial update APIs
- Filtered and paginated ticket listing
- Auto-generated alphanumeric SR `ticketId`
- Batch ticket fetch by ids
- Ticket channel, related party, related entity, and note child tables
- Attachment metadata support in ticket APIs
- Attachment file upload, download, and delete through S3-compatible storage
- UTC-aware audit timestamps through database triggers
- Centralized API exception handling
- Ticket status transition guardrails
- Externalized status transition configuration
- Kafka outbox publisher for SR lifecycle events
- Attachment metadata and ticket tag tables
- Ticket metadata configuration and data management APIs
- Swagger/OpenAPI generation
- Health and OpenAPI support

## Start everything

```powershell
docker compose up --build
```

This starts both containers and the app connects to PostgreSQL using the internal host `postgres`.

## Local code changes

The app container mounts your local project directory into `/workspace`. If you change code on your laptop:

- Quarkus dev mode often reloads it automatically
- If needed, restart just the app container:

```powershell
docker compose restart ticket-service
```

## Endpoints

- `POST /api/tickets`
- `POST /api/tickets/by-ids`
- `GET /api/tickets/{id}`
- `PUT /api/tickets/{id}`
- `PATCH /api/tickets/{id}`
- `POST /api/tickets/{ticketId}/attachments/upload`
- `GET /api/tickets/attachments/{attachmentId}/file`
- `DELETE /api/tickets/attachments/{attachmentId}`
- `DELETE /api/tickets/{id}` soft delete
- `GET /api/tickets`
- `POST /ticket-meta`
- `GET /ticket-meta`
- `PUT /ticket-meta/{id}`
- `DELETE /ticket-meta/{id}`
- `POST /ticket-metadata`
- `GET /ticket-metadata`
- `GET /ticket-metadata/{id}`
- `PUT /ticket-metadata/{id}`
- `DELETE /ticket-metadata/{id}`

## Useful URLs

- API base: `http://localhost:8081/api/tickets`
- Metadata config base: `http://localhost:8081/ticket-meta`
- Metadata data base: `http://localhost:8081/ticket-metadata`
- OpenAPI UI: `http://localhost:8081/q/swagger-ui`
- OpenAPI spec: `docs/swagger-openapi.yaml`
- Health: `http://localhost:8081/q/health`
- Postman collection: `postman/ticket-service.postman_collection.json`
- Postman environment: `postman/ticket-service.local.postman_environment.json`
- Functional documentation: `docs/functional-documentation.md`
- Technical documentation: `docs/technical-documentation.md`

## Example list filters

`GET /api/tickets?page=0&size=10&status=OPEN&priority=HIGH&tenantId=44444444-4444-4444-4444-444444444444&assignedUserId=11111111-1111-1111-1111-111111111111&search=printer`

`GET /api/tickets?page=0&size=10&tenantId=44444444-4444-4444-4444-444444444444&category=APPLICATION&subCategory=NOTIFICATIONS`

`GET /api/tickets?page=0&size=10&ticketId=SR-20260503101530-A1B2C3D4`

## Eventing

Ticket lifecycle changes are written to `ticket_event_outbox` and then published to Kafka topic `ticket-events` by the background worker. Event payloads include `ticketId`, channel, related parties, related entities, notes, tags, attachments, and extended attributes. Published event types:

- `TICKET_CREATED`
- `TICKET_UPDATED`
- `TICKET_ASSIGNED`
- `TICKET_STATUS_CHANGED`
- `TICKET_RESOLVED`
