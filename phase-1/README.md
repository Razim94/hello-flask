# Hello World Flask App (Phase 1 of the project)

This is a simple Flask application that returns "Hello, World!" when accessed. It is containerized using Docker and deployed via docker-compose.

## Files Included
- hello.py – Flask application source
- Dockerfile – Instructions for building the Docker image
- docker-compose.yml – For running the container
- requirements.txt – Python dependencies

## How to Build and Run

### Prerequisites
- Docker installed
- (Optional) Docker Compose installed

### Using Docker directly

1. Build the Docker image:
   docker build -t razi94/hello-world-flask:latest .

2. Run the container:
   docker run -p 5000:5000 razi94/hello-world-flask:latest

3. Access the app:
   Open your browser or run:
   curl http://localhost:5000

### Using Docker Compose

1. Start the app:
   docker-compose up

2. Stop the app:
   docker-compose down

### Docker Hub Image

You can also pull and run the prebuilt image:

docker pull razi94/hello-world-flask:latest
docker run -p 5000:5000 razi94/hello-world-flask:latest

Docker Hub URL: https://hub.docker.com/r/razi94/hello-world-flask

## Notes

- You may have to open port 5000 on your host to access the application. 
- Testing dev branch changes
