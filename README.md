# Microservices Skill Test — Docker Containerization

## 1. Project Overview

This project demonstrates how to containerize Node.js microservices using Docker and configure communication between services using Docker Compose.

The repository contains four microservices:

1. User Service
2. Product Service
3. Order Service
4. Gateway Service

Each service has its own Dockerfile, which defines how its Docker image is built.

**Current deployment scope:** The Docker Compose configuration deploys three services: User Service, Product Service, and Gateway Service. The Order Service Dockerfile is documented separately but is not included in the current Compose deployment.



## 2. Service Details

| Service | Application port | Purpose |
|---|---:|---|
| User Service | 3000 | Returns user information |
| Product Service | 3001 | Returns product information |
| Order Service | 3002 | Provides order-related functionality |
| Gateway Service | 3003 | Routes requests to backend services |

The ports above are the application ports. The current Compose file publishes ports 3000, 3001, and 3003 on the host.

## 3. Clone the repo and Creating the Dockerfiles

Each Dockerfile uses Node.js as its base image, sets a working directory, installs dependencies, copies application files, exposes the appropriate port, and defines the startup command.

```
git clone https://github.com/codeWithTanuj-Taz/Microservices-Task

```

<img width="625" height="62" alt="Screenshot1" src="https://github.com/user-attachments/assets/e24b95c3-0903-4c3e-9689-9722655d37a8" />


### 3.1 User Service Dockerfile

**Location:** `Microservices/user-service/Dockerfile`

Create a file named `Dockerfile` inside the `user-service` directory.

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install --omit=dev

COPY app.js ./

EXPOSE 3000

CMD ["node", "app.js"]
```

**Explanation:**

- `FROM node:18-alpine` selects a lightweight Node.js 18 base image.
- `WORKDIR /app` sets the working directory inside the container.
- `COPY package*.json ./` copies the package manifest.
- `RUN npm install --omit=dev` installs production dependencies.
- `COPY app.js ./` copies the User Service application code.
- `EXPOSE 3000` documents the port used by the application.
- `CMD ["node", "app.js"]` starts the application when the container runs.

<img width="620" height="339" alt="Screenshot7" src="https://github.com/user-attachments/assets/de2cd51d-3348-4ab6-8c0d-4b4c2ce2f849" />

  

### 3.2 Product Service Dockerfile

**Location:** `Microservices/product-service/Dockerfile`

Create a file named `Dockerfile` inside the `product-service` directory.

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install --omit=dev

COPY app.js ./

EXPOSE 3001

CMD ["node", "app.js"]
```

<img width="579" height="371" alt="Screenshot8" src="https://github.com/user-attachments/assets/98437f37-bf9d-4238-b81a-0c717d0c2e20" />

**Note:** This Dockerfile documents how to package the Order Service. The current three-service Compose configuration does not start this service.

### 3.3 Gateway Service Dockerfile

**Location:** `Microservices/gateway-service/Dockerfile`

Create a file named `Dockerfile` inside the `gateway-service` directory.

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install --omit=dev

COPY app.js ./

EXPOSE 3003

CMD ["node", "app.js"]
```

**Explanation:**

- `FROM node:18-alpine` selects the Node.js base image.
- `WORKDIR /app` establishes the working directory.
- `COPY package*.json ./` copies the package manifest.
- `RUN npm install --omit=dev` installs production dependencies, including Axios.
- `COPY app.js ./` copies the Gateway application.
- `EXPOSE 3003` documents the Gateway port.
- `CMD ["node", "app.js"]` starts the Gateway.

<img width="835" height="382" alt="Screenshot9" src="https://github.com/user-attachments/assets/3fad4389-04c3-402c-9863-aa3498868e3c" />


The Gateway forwards requests to backend services using their Docker Compose service names.

For example:

- User Service: `http://user-service:3000/users`
- Product Service: `http://product-service:3001/products`

These names are resolvable by containers attached to the shared Compose network.


## 4. Docker Compose Configuration

**File location:** `docker-compose.yml` in the project root.

Docker Compose is used to define, build, connect, and run the three required services together.

The following is the configuration used for the current deployment:

```yaml
services:
  user-service:
    build: ./Microservices/user-service
    ports:
      - "3000:3000"
    networks:
      - microservices-network

  product-service:
    build: ./Microservices/product-service
    ports:
      - "3001:3001"
    networks:
      - microservices-network

  gateway-service:
    build: ./Microservices/gateway-service
    ports:
      - "3003:3003"
    depends_on:
      - user-service
      - product-service
    networks:
      - microservices-network

networks:
  microservices-network:
    driver: bridge
```

### Explanation of the Compose configuration

**`services:`**

Defines the containers managed by Docker Compose.

**`build:`**

Specifies the directory containing each service's Dockerfile and application files.

**`ports:`**

Maps a host port to the corresponding container port:

- `3000:3000` — User Service
- `3001:3001` — Product Service
- `3003:3003` — Gateway Service

**`depends_on:`**

Specifies that the User and Product services should be started before the Gateway. It does not guarantee that they are fully ready to receive requests.

**`networks:`**

Attaches each service to the same network.

**`driver: bridge`**

Creates a bridge network for communication between containers on the same Docker host.

<img width="809" height="574" alt="Screenshot10" src="https://github.com/user-attachments/assets/a06dcdc7-f8fc-44e2-bec8-d3ab56d5efc5" />



## 5. Prerequisites

Before running the application, install:

- Docker Desktop
- Docker Compose, included with current Docker Desktop installations
- PowerShell or another terminal

Start Docker Desktop and wait until the Docker engine is running.

Verify Docker installation:

```powershell
docker --version
docker compose version
```

<img width="646" height="80" alt="Screenshot17" src="https://github.com/user-attachments/assets/1c9ec91d-95bd-4e23-a89b-d82bef70610a" />


## 6. Setup and Execution Instructions

Run all Compose commands from the project root directory, where `docker-compose.yml` is located.

### Step 1: Navigate to the project

```powershell
cd "D:\Devopps Project\Microservices-Task"
```

### Step 2: Validate the Compose configuration

```powershell
docker compose config
```

<img width="547" height="374" alt="Screenshot11" src="https://github.com/user-attachments/assets/fd5627f8-90ce-42cd-82d1-efdf985f9a62" />


Docker Compose should display the resolved configuration without validation errors.

### Step 3: Build and start the services

```powershell
docker compose up --build -d
```
<img width="642" height="272" alt="Screenshot18" src="https://github.com/user-attachments/assets/60fe73e2-b6c8-40ec-b60d-fea4bb3449c6" />



This command builds the images and starts the three configured services in detached mode.

### Step 4: Verify running containers

```powershell
docker compose ps
```

<img width="721" height="152" alt="Screenshot13" src="https://github.com/user-attachments/assets/f8c84d4f-7973-4b23-b4f1-cb6de1abad2e" />


The expected services are:

- `user-service`
- `product-service`
- `gateway-service`

Their status should show that the containers are running.

### Step 5: View service logs

```powershell
docker compose logs --tail=50
```
<img width="659" height="76" alt="Screenshot19" src="https://github.com/user-attachments/assets/c7b62d76-d8fb-4bec-aab6-a04d3dc400eb" />


To view logs for an individual service:

```powershell
docker compose logs user-service
docker compose logs product-service
docker compose logs gateway-service
```


## 7. Local Testing and Validation

Use the following PowerShell commands to verify that each deployed service is accessible through `localhost`.

### 7.1 User Service

Health check:

```powershell
Invoke-RestMethod http://localhost:3000/health
```

Expected response:

```text
status
------
User Service is healthy
```

Retrieve users:

```powershell
Invoke-RestMethod http://localhost:3000/users
```

Expected sample users:

- John Doe
- Jane Smith

<img width="662" height="213" alt="Screenshot14" src="https://github.com/user-attachments/assets/644fe57f-4cac-40d0-9d51-fce583a41cbd" />


### 7.2 Product Service

Health check:

```powershell
Invoke-RestMethod http://localhost:3001/health
```

Expected response:

```text
status
------
Product Service is healthy
```

Retrieve products:

```powershell
Invoke-RestMethod http://localhost:3001/products
```

Expected sample products:

- Laptop — 999
- Phone — 699

<img width="659" height="219" alt="Screenshot15" src="https://github.com/user-attachments/assets/34f9503e-8005-453f-9b7a-686c52f8ff04" />


### 7.3 Gateway Service

Health check:

```powershell
Invoke-RestMethod http://localhost:3003/health
```

Expected response:

```text
status
------
Gateway Service is healthy
```

Retrieve users through the Gateway:

```powershell
Invoke-RestMethod http://localhost:3003/api/users
```

Retrieve products through the Gateway:

```powershell
Invoke-RestMethod http://localhost:3003/api/products
```

<img width="614" height="318" alt="Screenshot16" src="https://github.com/user-attachments/assets/0d7d2326-10bf-4784-b964-244ca6960d1b" />

These requests should return the same sample user and product data as the corresponding backend services. Successful responses confirm that the Gateway can communicate with the User and Product services over the Docker network.


## 8. Docker Network Verification

List Docker networks:

```powershell
docker network ls
```

Inspect the network created by the Compose project:

```powershell
docker network inspect microservices-task_microservices-network
```
<img width="535" height="116" alt="Screenshot20" src="https://github.com/user-attachments/assets/b52349a5-c596-4af9-b659-81c47ca56bef" />


<img width="687" height="139" alt="Screenshot21" src="https://github.com/user-attachments/assets/a683d19e-f32b-4597-84b8-f5eabf8871b7" />


The generated network name can vary depending on the Compose project name. If the example name does not exist, use the network name displayed by `docker network ls`.

The User, Product, and Gateway containers should be attached to the same Compose network.

## 12. Screenshots and Evidence

Create a folder named `screenshots` in the project root:

```text
Microservices-Task/
├── docker-compose.yml
├── README.md
├── screenshots/
│   ├── docker-compose-ps.png
│   ├── user-service-test.png
│   ├── product-service-test.png
│   └── gateway-service-test.png
└── Microservices/
    ├── user-service/
    ├── product-service/
    ├── order-service/
    └── gateway-service/
```

Save your actual screenshots using these filenames, or update the Markdown paths to match your chosen filenames.

### Docker Containers Running

![Docker Compose containers running](screenshots/docker-compose-ps.png)

*Evidence that the three configured services are running.*

### User Service Testing

![User Service test results](screenshots/user-service-test.png)

*Evidence of the User Service health check and user-list endpoint.*

### Product Service Testing

![Product Service test results](screenshots/product-service-test.png)

*Evidence of the Product Service health check and product-list endpoint.*

### Gateway Service Testing

![Gateway Service test results](screenshots/gateway-service-test.png)

*Evidence of the Gateway health check and successful user and product requests.*

**Note:** These image references will display only after the corresponding screenshot files are saved in the `screenshots` directory. Use screenshots captured from your actual environment.

## 13. Conclusion

This project demonstrates the fundamentals of containerizing Node.js microservices with Docker and orchestrating multiple containers with Docker Compose.

The implementation includes:

- Four Dockerfiles documenting the container build process for the User, Product, Order, and Gateway services.
- A Docker Compose configuration for the three required deployed services.
- Port mapping for local access.
- A shared bridge network for inter-service communication.
- Local health checks and API endpoint testing.
- Troubleshooting instructions and screenshot evidence.

The User Service, Product Service, and Gateway Service were successfully started and tested locally. The Order Service Dockerfile is documented separately and is not included in the current three-service Compose deployment.
