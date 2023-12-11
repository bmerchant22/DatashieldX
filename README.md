# DataShieldX Documentation

## Overview

Welcome to DataShieldX, a comprehensive system designed to provide a secure data handling environment. This documentation will guide you through the deployment process, explain the project structure, and provide insights into the main components and their functionalities.

## Table of Contents

1. [Introduction](#introduction)
2. [Project Structure](#project-structure)
3. [Steps to Deploy](#steps-to-deploy)
    - [Update IP Address](#update-ip-address)
    - [Run the Deployment Script](#run-the-deployment-script)
4. [Main Script Explanation](#main-script-explanation)
    - [Build Static Files](#build-static-files)
    - [Installation of Docker](#installation-of-docker)
    - [Running Docker-Compose](#running-docker-compose)
    - [Nginx Setup](#nginx-setup)
5. [Docker Compose and Dockerfiles](#docker-compose-and-dockerfiles)
    - [Client Service](#client-service)
    - [Student Service](#student-service)
    - [Rocketchat Service](#rocketchat-service)
    - [Mongodb Service](#mongodb-service)
    - [Code-Server Service](#code-server-service)
    - [Network](#network)

## Introduction

DataShieldX is a secure data handling environment that comprises both frontend and backend components. It offers a robust solution for managing and processing data securely. The system is designed to be deployed on a server, providing a secure and scalable platform for various data-related tasks.

## Project Structure

The project is organized into two main directories:

1. **DataShieldX-frontend**: Contains the frontend components, including the client and student interfaces.
2. **DataShieldX-backend**: Includes the backend components, consisting of a main server and student-entry points, as well as the deployment script (`main.sh`).

## Steps to Deploy

### Update IP Address

Before deploying DataShieldX, ensure you update the IP address in the following `.env` files:

- **DataShieldX-frontend/.env**
- **DataShieldX-frontend/client/.env**
- **DataShieldX-frontend/student/.env**

If deploying locally, the default IP address is set to localhost. Replace it with the actual IP address of your server.

### Run the Deployment Script

1. Navigate to the `DataShieldX-backend` directory:

   ```bash
   cd DataShieldX-backend
   ```

2. Execute the deployment script:

   ```bash
   ./main.sh
   ```

## Main Script Explanation

### Build Static Files

The script first builds static files for the client and student interfaces, copying them to the backend directories. The backend then serves these static files.

```bash
npm install
npm run build
```

### Installation of Docker

Checks if Docker and Docker Compose are installed. If not, it installs Docker and Docker Compose.

### Running Docker-Compose

Executes the Docker Compose command in the backend directory, starting the Docker containers. This includes services for the client, student, Rocket.Chat, MongoDB, and Code Server.

```bash
docker-compose up -d
```

### Nginx Setup

Checks if Nginx is installed. If not, it installs Nginx, removes the default configuration file, and sets up custom configurations for proxying to the main server and a chat service.

## Docker Compose and Dockerfiles

### Client Service

- Defines a service named client for the main server.
- Builds the service using the Dockerfile in the `./main-server` directory.
- Exposes port 8001 on the host.
- Connects to the custom network named mynetwork.
- Runs in privileged mode.
- Mounts local directories and Docker socket to the container.

### Student Service

- Defines a service named student for the student entry point.
- Builds the service using the Dockerfile in the `./student-entry` directory.
- Exposes port 8000 on the host.
- Connects to the custom network named mynetwork.
- Mounts a local directory to the container.

### Rocketchat Service

- Defines a service named rocketchat for the Rocket.Chat application.
- Uses the official Rocket.Chat image from the specified registry.
- Restarts the service always.
- Defines Traefik labels for HTTPS configuration.
- Sets environment variables for MongoDB connection and Rocket.Chat configuration.
- Depends on the mongodb service.
- Exposes and maps ports as specified.

### Mongodb Service

- Defines a service named mongodb using the Bitnami MongoDB image.
- This is required for running rocket chat.
- Restarts the service always.
- Uses the named volume mongodb_data for persisting MongoDB data.
- Sets environment variables for MongoDB configuration.

### Code-Server Service

- Defines a service named code-server using the Code Server image.

### Network

- Defines a custom network named mynetwork using the bridge driver.
- All Docker containers in the application run on this network for communication.
- The network is named here as mynetwork, but it will be named as datashieldx-backend-network, and this name is being used in DataShieldX-backend/main-server/store/store.go on line 77, can be changed there if you change network.

After implementing all this, finally:

1. **Student server**: running on port 8000
1. **Client server**: running on port 8001
1. **Rocketchat**: running on port 3000
1. **Workspace server of team 'i'**: running on port 8080+i

This documentation provides an overview of the DataShieldX project, guiding you through deployment and explaining the main components and their functionalities. 

**Student Credentials**

For now, for student server, we have 3 students with teir teams with credentials of team id, username and password:

1. 0, user0, pass0
2. 1, user1, pass1
3. 2, user2, pass2

**Deployment** 
- We have hosted this on our digital ocean server
- The ip address of the servr is: 165.22.213.213

**NOTE** \
Some important configurations which could cause errors :
- We have used mongodb in our project currently, if you want to use your own mongodb instance, you can replace your url in the .env file for rocketchat in the backend directory and also, in the backend/pkg/handlers/mongo_handlers.go
- The network is named here as mynetwork, but it will be named as datashieldx-backend-network, and this name is being used in DataShieldX-backend/main-server/store/store.go on line 77, can be changed there if you change network.
- The directories which student is allowed to use our configured in handlers.go, in DataShieldX-backend/pkg/web directory, you can change it