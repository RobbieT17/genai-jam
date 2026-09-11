# Compact Disc Catalog REST API

A Spring Boot-based REST API for managing a compact disc catalog with full CRUD operations, comprehensive API documentation via Swagger, and support for both local and containerized deployments.

## Table of Contents

- [Overview](#overview)
- [Technology Stack](#technology-stack)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Application](#running-the-application)
- [API Documentation](#api-documentation)
- [API Endpoints](#api-endpoints)
- [Database Schema](#database-schema)
- [Project Structure](#project-structure)
- [Deployment](#deployment)
- [Monitoring and Logging](#monitoring-and-logging)
- [Development](#development)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

## Overview

This application provides a RESTful API for managing a collection of compact discs with detailed album and track information. It supports full CRUD (Create, Read, Update, Delete) operations and includes a web-based user interface for browsing the catalog. The API is documented using Swagger/OpenAPI standards for easy integration and testing.

**Key Features:**
- Full CRUD operations for albums (Compact Discs)
- Comprehensive track management per album
- RESTful API design with JSON payloads
- Interactive Swagger UI for API exploration
- Web-based catalog browser
- Comprehensive logging with Log4j2
- Multi-environment configuration (local, Docker)
- JPA/Hibernate-based data persistence

## Technology Stack

| Component | Version | Purpose |
|-----------|---------|---------|
| Java | 11 | Runtime Environment |
| Spring Boot | 2.5.3 | Application Framework |
| Spring Data JPA | Latest | Data Persistence & ORM |
| MySQL | 8.0.22 | Relational Database |
| Swagger | 2.9.2 | API Documentation |
| Log4j2 | Latest | Logging Framework |
| Maven | Latest | Build Tool |

## Prerequisites

Before running the application, ensure you have the following installed:

- **Java Development Kit (JDK)** 11 or higher
- **Maven** 3.6.0 or higher
- **MySQL Server** 8.0 or higher (or Docker)
- **MySQL Client** (for running SQL scripts)
- **Git** (for version control)

### Verify Installation

```bash
java -version
mvn -version
mysql --version
```

## Installation

### Step 1: Clone or Download the Repository

```bash
git clone <repository-url>
cd no-readme
```

### Step 2: Set Up the Database

#### Local MySQL Setup

1. Start MySQL Server (if not already running)
   ```bash
   # Windows
   net start MySQL80
   
   # macOS (using Homebrew)
   brew services start mysql
   
   # Linux
   sudo systemctl start mysql
   ```

2. Create the database and tables:
   ```bash
   mysql -u root -p < sql/createTables.sql
   ```
   
   When prompted, enter your MySQL root password.

3. Verify the database was created:
   ```bash
   mysql -u root -p -e "USE conygre; SHOW TABLES;"
   ```

## Configuration

### Local Development Configuration

The default configuration file is located at `src/main/resources/application.properties`:

```properties
# Database Connection
spring.datasource.url=jdbc:mysql://localhost:3306/conygre?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=c0nygre1
spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver

# Server Port (default: 8080, uncomment to change)
# server.port=8081

# Logging
logging.file=myapplication.log
```

**Important:** Update the database credentials to match your MySQL installation:
- `spring.datasource.username`: Your MySQL username (default: root)
- `spring.datasource.password`: Your MySQL password

### Docker Configuration

For Docker deployments, use `application-docker.properties`:

```properties
spring.datasource.url=jdbc:mysql://cddb:3306/conygre
spring.datasource.username=root
spring.datasource.password=secret123
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

The `cddb` hostname refers to the linked MySQL container in Docker Compose.

## Running the Application

### Build the Application

```bash
mvn clean package
```

This command will:
1. Clean previous builds
2. Compile the source code
3. Run unit tests
4. Package the application as a JAR file
5. Create the executable JAR in the `target/` directory

### Run Locally

#### Option 1: Using Maven

```bash
mvn spring-boot:run
```

#### Option 2: Using the JAR File

```bash
java -jar target/CompactDiscRestDataBoot-0.0.1-SNAPSHOT.jar
```

#### Option 3: Specify Configuration Profile

For local development:
```bash
mvn spring-boot:run
```

For Docker environment:
```bash
java -jar target/CompactDiscRestDataBoot-0.0.1-SNAPSHOT.jar --spring.profiles.active=docker
```

### Verify Application Started

Once the application starts, you should see output similar to:
```
Started AppConfig in X.XXX seconds
```

The application will be running at: `http://localhost:8080`

## API Documentation

### Swagger UI

The API is fully documented using Swagger/OpenAPI. Access the interactive documentation at:

**URL:** `http://localhost:8080/swagger-ui.html`

The Swagger UI allows you to:
- View all available endpoints
- See request/response schemas
- Test API endpoints directly from the browser
- Download API specification in JSON format

### API Information

- **Title:** Album REST API with Swagger
- **Description:** Full CRUD API for managing albums and tracks
- **Contact:** Nick Todd (nick.todd@conygre.com)

## API Endpoints

### Base URL
```
http://localhost:8080/api/compactdiscs
```

### Endpoints

#### 1. Get All Compact Discs
```
GET /api/compactdiscs
```
- **Description:** Retrieve all compact discs in the catalog
- **Response Code:** 200 OK
- **Response Body:** Array of CompactDisc objects

**Example Response:**
```json
[
  {
    "id": 9,
    "title": "Is This It",
    "artist": "The Strokes",
    "tracks": 11,
    "price": 13.99
  },
  {
    "id": 10,
    "title": "Just Enough Education to Perform",
    "artist": "Stereophonics",
    "tracks": 11,
    "price": 10.99
  }
]
```

#### 2. Get Compact Disc by ID
```
GET /api/compactdiscs/{id}
```
- **Description:** Retrieve a specific compact disc by its ID
- **Path Parameter:** `id` - The ID of the compact disc
- **Response Code:** 200 OK
- **Response Body:** CompactDisc object

**Example Request:**
```
GET /api/compactdiscs/9
```

**Example Response:**
```json
{
  "id": 9,
  "title": "Is This It",
  "artist": "The Strokes",
  "tracks": 11,
  "price": 13.99
}
```

#### 3. Get Compact Disc by ID with 404 Handling
```
GET /api/compactdiscs/404/{id}
```
- **Description:** Retrieve a compact disc with proper 404 response if not found
- **Path Parameter:** `id` - The ID of the compact disc
- **Response Codes:**
  - 200 OK (found)
  - 404 Not Found (if CD doesn't exist)

#### 4. Create New Compact Disc
```
POST /api/compactdiscs
Content-Type: application/json
```
- **Description:** Add a new compact disc to the catalog
- **Request Body:** CompactDisc JSON object
- **Response Code:** 200 OK / 201 Created

**Example Request:**
```json
{
  "title": "Sweet Caroline",
  "artist": "Neil Diamond",
  "price": 13.99,
  "tracks": 1
}
```

#### 5. Delete Compact Disc by ID
```
DELETE /api/compactdiscs/{id}
```
- **Description:** Delete a compact disc by its ID
- **Path Parameter:** `id` - The ID of the compact disc
- **Response Code:** 204 No Content / 200 OK

**Example Request:**
```
DELETE /api/compactdiscs/20
```

#### 6. Delete Compact Disc by Object
```
DELETE /api/compactdiscs
Content-Type: application/json
```
- **Description:** Delete a compact disc by providing the entire object
- **Request Body:** CompactDisc JSON object
- **Response Code:** 204 No Content / 200 OK

**Example Request:**
```json
{
  "id": 20,
  "title": "Sweet Caroline",
  "artist": "Neil Diamond",
  "price": 13.99,
  "tracks": 1
}
```

### CORS Support

The API has Cross-Origin Resource Sharing (CORS) enabled, allowing requests from all domains. This enables the web-based UI and third-party applications to consume the API.

### API Testing

#### Using cURL

```bash
# Get all CDs
curl http://localhost:8080/api/compactdiscs

# Get CD by ID
curl http://localhost:8080/api/compactdiscs/9

# Create new CD
curl -X POST http://localhost:8080/api/compactdiscs \
  -H "Content-Type: application/json" \
  -d '{"title":"Test Album","artist":"Test Artist","price":9.99,"tracks":10}'

# Delete CD
curl -X DELETE http://localhost:8080/api/compactdiscs/20
```

#### Using REST Client (VS Code Extension)

Use the provided `.rest` files in the `rest/` directory:
- `postcd.rest` - Example POST request
- `deletecd.rest` - Example DELETE request

## Database Schema

### Tables

#### compact_discs
Primary table for storing album information.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | INT | PRIMARY KEY, AUTO_INCREMENT | Unique identifier |
| title | VARCHAR(50) | NOT NULL | Album title |
| artist | VARCHAR(30) | NOT NULL | Artist name |
| tracks | INT | NOT NULL | Number of tracks |
| price | DOUBLE | NOT NULL | Album price |

#### tracks
Table for storing track information linked to albums.

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| id | INT | PRIMARY KEY, AUTO_INCREMENT | Unique identifier |
| cd_id | INT | NOT NULL, FOREIGN KEY | Reference to compact_discs.id |
| title | VARCHAR(50) | NOT NULL | Track title |

### Sample Data

The database is pre-populated with popular albums:
- Is This It - The Strokes
- Just Enough Education to Perform - Stereophonics
- Parachutes - Coldplay
- White Ladder - David Gray
- Greatest Hits - Penelope
- Echo Park - Feeder
- Mezzanine - Massive Attack
- Spice World - Spice Girls

## Project Structure

```
no-readme/
├── pom.xml                                    # Maven configuration
├── readme.md                                  # This file
├── sql/
│   └── createTables.sql                       # Database initialization script
├── rest/
│   ├── postcd.rest                            # Example POST requests
│   └── deletecd.rest                          # Example DELETE requests
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/conygre/spring/boot/
│   │   │       ├── AppConfig.java             # Spring Boot entry point
│   │   │       ├── SwaggerConfig.java         # Swagger configuration
│   │   │       ├── entities/
│   │   │       │   ├── CompactDisc.java       # Album entity
│   │   │       │   └── Track.java             # Track entity
│   │   │       ├── repos/
│   │   │       │   └── CompactDiscRepository.java  # Data repository
│   │   │       ├── rest/
│   │   │       │   └── CompactDiscController.java  # REST controller
│   │   │       └── services/
│   │   │           ├── CompactDiscService.java     # Service interface
│   │   │           └── CompactDiscServiceImpl.java  # Service implementation
│   │   ├── resources/
│   │   │   ├── application.properties        # Local configuration
│   │   │   ├── application-docker.properties # Docker configuration
│   │   │   ├── log4j2.properties             # Logging configuration
│   │   │   ├── static/
│   │   │   │   ├── index.html                # Main landing page
│   │   │   │   ├── listcds.html              # Catalog listing page
│   │   │   │   ├── promisefetch.html         # Example using Fetch API
│   │   │   │   ├── constructorfunctionajax.html # Example using AJAX
│   │   │   │   ├── temp.html                 # Temporary page
│   │   │   │   └── css/
│   │   │   │       └── cd.css                # Stylesheet
│   │   │   └── META-INF/
│   │   │       └── persistence.xml           # JPA configuration
│   │   └── test/                             # Unit tests
│   └── target/                               # Build output directory
└── .gitignore                                # Git ignore rules
```

## Deployment

### Local Deployment

Refer to the [Running the Application](#running-the-application) section for local setup and execution.

### Docker Deployment

#### Docker Setup

1. **Build Docker Image:**
   ```bash
   docker build -t compactdisc-api:latest .
   ```

2. **Run with Docker Compose:**
   ```bash
   docker-compose up -d
   ```

   The `docker-compose.yml` should include:
   - Java application container
   - MySQL database container with name `cddb`
   - Networking configuration to link containers

3. **Verify Containers:**
   ```bash
   docker ps
   docker logs <container-id>
   ```

#### Docker Environment Variables

When running in Docker, ensure the following environment variables are set:
- `SPRING_PROFILES_ACTIVE=docker` - Activates docker profile
- Database connection uses `cddb` hostname instead of `localhost`

### Application Server Deployment

The application can be deployed to any Java application server supporting Spring Boot:
- **Standalone JAR** (recommended) - No additional server needed
- **Apache Tomcat** - Deploy WAR file
- **JBoss/WildFly** - Deploy WAR file
- **Docker/Kubernetes** - Containerized deployment

## Monitoring and Logging

### Log Output

Logs are written to: `myapplication.log`

### Logging Configuration

Log levels are configured in `src/main/resources/log4j2.properties`:

```properties
# Configure logging levels for different packages
# ERROR, WARN, INFO, DEBUG, TRACE
```

### Viewing Logs

```bash
# Real-time log monitoring
tail -f myapplication.log

# View last 100 lines
tail -n 100 myapplication.log

# Search for errors
grep ERROR myapplication.log
```

### Application Metrics

The application includes logging at key points:
- HTTP request entry points in the REST controller
- Database operations
- Service method invocations

**Example Log Entry:**
```
[INFO] managed to call a Get request for findAll
```

### Health Check Endpoint

Once the application is running, verify it's healthy by accessing the API:
```bash
curl http://localhost:8080/api/compactdiscs
```

Expected response: HTTP 200 with array of compact discs.

## Development

### Development Environment Setup

1. **IDE Configuration** (IntelliJ IDEA / Eclipse / VS Code)
   - Import project as Maven project
   - Configure JDK 11 as project SDK
   - Enable annotation processing for JPA/Hibernate

2. **Maven Profiles** (if configured)
   ```bash
   mvn clean install -P <profile-name>
   ```

3. **Running Tests**
   ```bash
   mvn test
   ```

### Code Organization

- **Entities** (`entities/`): JPA entity classes with annotations
- **Repositories** (`repos/`): Spring Data JPA repository interfaces
- **Services** (`services/`): Business logic layer
- **Controllers** (`rest/`): REST endpoint handlers
- **Configuration** (`AppConfig.java`, `SwaggerConfig.java`): Application configuration

### IDE Plugins Recommended

- Spring Boot Extension Pack (Visual Studio Code)
- Lombok (optional, if used)
- REST Client (for testing .rest files)

### Build Management

```bash
# Clean build
mvn clean

# Compile only
mvn compile

# Run tests
mvn test

# Package without running tests
mvn package -DskipTests

# Generate documentation (if configured)
mvn javadoc:javadoc
```

## Contributing

When contributing to this project:

1. Follow Spring Boot and Java naming conventions
2. Maintain consistent code style
3. Add appropriate logging statements
4. Update documentation for API changes
5. Test all endpoints before submitting changes
6. Update the database schema documentation if needed

## License

This project is proprietary software developed by Conygre. For licensing information, contact: nick.todd@conygre.com

## Contact

**Developer:** Nick Todd  
**Email:** nick.todd@conygre.com  
**Organization:** Conygre  
**Website:** http://www.conygre.com

---

**Last Updated:** 2024  
**Version:** 0.0.1-SNAPSHOT