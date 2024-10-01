# Containerized Full Stack Application

This repository contains a containerized full-stack application with frontend, backend, and a MongoDB database using Docker. The project is divided into three services:

- **Frontend**: A Node.js application.
- **Backend**: Another Node.js application that connects to a MongoDB database.
- **Database (MongoDB)**: MongoDB is used for storing data.

## Docker Setup

### Prerequisites

- Install [Docker](https://docs.docker.com/get-docker/) on your local machine.
- Install [Docker Compose](https://docs.docker.com/compose/install/) if not included in your Docker installation.

## Cloning the Repository

To clone the repository, use the following command:

\`\`\`bash
git clone <repository-url>
cd <repository-folder>
\`\`\`

Replace `<repository-url>` with the actual GitHub URL of this repository.

## Docker Services

### 1. Frontend Dockerfile Explanation

The `frontend` service is based on the following Dockerfile:

\`\`\`Dockerfile
FROM node:14.16.0-alpine3.13

RUN addgroup app && adduser -S -G app app
USER app

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

EXPOSE 3000

CMD ["npm", "start"]
\`\`\`

**Key Commands**:
- **WORKDIR /app**: Sets the working directory inside the container.
- **COPY package*.json ./**: Copies the package.json and lock files to the container.
- **RUN npm install**: Installs dependencies inside the container.
- **EXPOSE 3000**: Exposes port 3000 for the frontend application.
- **CMD ["npm", "start"]**: Starts the application.

### 2. Backend Dockerfile Explanation

The `backend` service uses the following Dockerfile:

\`\`\`Dockerfile
FROM node:14.16.0-alpine3.13

RUN addgroup app && adduser -S -G app app
USER app

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

EXPOSE 3001

CMD ["npm", "start"]
\`\`\`

**Key Commands**:
- **WORKDIR /app**: Sets the working directory for the backend service.
- **COPY package*.json ./**: Copies the necessary package.json files.
- **RUN npm install**: Installs backend dependencies.
- **EXPOSE 3001**: Exposes port 3001 for the backend.
- **CMD ["npm", "start"]**: Starts the backend service.

### 3. Docker Compose

The `docker-compose.yml` file is used to orchestrate the frontend, backend, and database services:

\`\`\`yaml
version: "3.8"

services:
  frontend:
    depends_on: 
      - backend
    build: ./frontend
    ports:
      - 3000:3000

  backend: 
    depends_on: 
      - db
    build: ./backend
    ports: 
      - 3001:3001
    environment: 
      DB_URL: mongodb://db/vidly
    command: ./docker-entrypoint.sh

  db:
    image: mongo:4.0-xenial
    ports:
      - 27017:27017
    volumes:
      - vidly:/data/db

volumes:
  vidly:
\`\`\`

### Explanation of `docker-compose.yml`:

- **depends_on**: Ensures that the backend starts before the frontend and the database starts before the backend.
- **build**: Specifies the build context for frontend and backend.
- **ports**: Maps the container ports to local machine ports for each service.
- **environment**: Provides the MongoDB connection URL to the backend.
- **volumes**: Persists MongoDB data on the local machine using the `vidly` volume.

## Running the Application

To start the entire application using Docker Compose, run the following command from the root directory:

\`\`\`bash
docker-compose up --build
\`\`\`

This command will:
- Build the images for frontend, backend, and MongoDB.
- Start all three services.
- The frontend will be accessible at `http://localhost:3000`.
- The backend will be accessible at `http://localhost:3001`.
- MongoDB will run on port 27017.

### Stopping the Application

To stop the running containers:

\`\`\`bash
docker-compose down
\`\`\`

This will stop and remove the containers, but the data will remain in the `vidly` volume.

## Additional Notes

- Ensure that the `docker-entrypoint.sh` script in the backend is executable before building the Docker image.
- You can modify the ports in the `docker-compose.yml` file if the defaults (3000, 3001, 27017) conflict with other services on your machine.

### Troubleshooting

If you encounter issues, ensure Docker and Docker Compose are installed correctly and that no other services are using the specified ports.

## License

This project is licensed under the MIT License.