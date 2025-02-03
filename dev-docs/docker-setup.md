---
icon: fill-drip
---

# Docker Setup

## Docker Configuration Documentation

This document explains the Docker setup for the Study Abroad App, including the differences between development and production configurations.

### Overview

The application uses a microservices architecture with four main services:

1. Backend (Django REST API)
2. Frontend (React application)
3. Database (MySQL)
4. Nginx (Reverse proxy)

### Development Setup ([docker-compose.yml](cci:7://file:///Users/sanjeevchauhan/github/studyabroad/docker-compose.yml:0:0-0:0))

The development configuration prioritizes developer experience with features like hot-reloading and exposed ports for debugging.

#### Services

**Backend Service**

* Built from `./mishmash/Dockerfile`
* Features:
  * Hot-reload enabled for development
  * Django development server
  * Exposed port 8000
  * Debug mode enabled
  * Direct volume mounting for code changes
* Environment variables configured for development

**Frontend Service**

* Built from `./mishmash/frontend/Dockerfile`
* Features:
  * Hot-reload enabled
  * React development server
  * Exposed port 3000
  * Direct volume mounting for code changes
  * Node modules preserved in container
* Environment configured for development with local API URL

**Database Service**

* MySQL 8.0
* Persistent data storage
* Native password authentication
* Root password configured for development

**Nginx used just to route frontend to backend**

### Production Setup ([docker-compose.prod.yml](cci:7://file:///Users/sanjeevchauhan/github/studyabroad/docker-compose.prod.yml:0:0-0:0))



#### Services

**Frontend Service**

* Built from `./mishmash/frontend/Dockerfile.prod`
* Features:
  * Production build optimization
  * Static file serving through Nginx
  * No exposed ports directly
  * No development dependencies
* Environment configured for production

**Backend Service**

* Built from `./mishmash/Dockerfile.prod`
* Features:
  * Gunicorn WSGI server
  * 4 workers and 4 threads
  * Debug mode disabled
  * Production-grade performance
  * No direct port exposure

**Nginx Service**&#x20;

* Alpine-based Nginx
* Features:
  * SSL/TLS support
  * Static file serving
  * Reverse proxy for backend
  * Ports 80 and 443 exposed
  * Let's Encrypt certificate integration - certbot is setup locally and stores certs
  * Access to static, media, and frontend build files
  * Logging configuration

**Database Service**

* Similar to development but with:
  * Health checks enabled
  * More secure configuration
  * Persistent volume mounting

### Volume Management

Both configurations use Docker volumes for persistent data:

* `mysql_data`: Database files
* `static_volume`: Django static files
* `media_volume`: User-uploaded media
* `frontend_build`: Production frontend assets (prod only)
* `nginx_logs`: Nginx logs (prod only)

### Key Differences

1. **Environment**
   * Development: Debug enabled, exposed ports, hot-reloading
   * Production: Debug disabled, optimized builds, secure configurations
2. **Server Configuration**
   * Development: Django development server, React development server
   * Production: Gunicorn, Nginx, optimized React build
3. **Security**
   * Development: Open ports, less strict configurations
   * Production: Restricted access, SSL/TLS, proper reverse proxy
4. **Performance**
   * Development: Focused on developer experience
   * Production: Optimized for end-user experience

### Usage

#### Development - local dev

```bash
docker compose up watch
```

#### Development - server (dev)

```bash
docker compose -f "docker-compose.dev.yml" up --build
```
