# ExcaliDash Docker Setup

This Docker setup containerizes the ExcaliDash application with a multi-container architecture.

## Architecture

- **Frontend**: React/Vite app served by Nginx
- **Backend**: Express.js API with Socket.IO
- **Database**: SQLite (persisted in Docker volume)

## Single Port Exposure

The application exposes only **port 6767** externally, which serves the frontend. The frontend's Nginx acts as a reverse proxy to route:

- `/api/*` requests to the backend container
- `/socket.io/*` WebSocket connections to the backend container

All inter-container communication happens on an internal Docker network.

## Quick Start

### Option 1: Use Pre-built Images from Docker Hub (Recommended)

Pull and run the latest multi-platform images:

```bash
docker compose -f docker-compose.prod.yml up -d
```

### Option 2: Build Locally

Build and run all services locally:

```bash
docker compose up -d --build
```

### Access the application:

[http://localhost:6767](http://localhost:6767)

## Publishing to Docker Hub

### Build and Push Multi-Platform Images

The `publish-docker.sh` script builds images for multiple platforms (amd64, arm64) and pushes them to Docker Hub:

```bash
# Build and push with 'latest' tag
./publish-docker.sh

# Build and push with a specific version
./publish-docker.sh v1.0.0
```

## Database

The SQLite database is stored in a Docker volume named `backend-data` which persists data across container restarts. Database migrations run automatically when the backend container starts.

## Environment Variables

Default configuration works out of the box. To customize:

Create `.env` files in `backend/` or `frontend/` directories:

**Backend `.env`:**

```
PORT=8000
NODE_ENV=production
```

**Frontend `.env`:**

```
VITE_API_URL=/api
```

## Port Mapping

- **6767** (external) → **80** (frontend nginx) → Routes to backend on internal network
- **8000** (internal only) - Backend API server

Only port 6767 is accessible from outside the Docker network.
