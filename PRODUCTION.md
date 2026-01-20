# Production Deployment Guide

This guide explains how to deploy Milyfe to production using Docker Compose.

The production setup follows the same structure as development:
- `docker-compose.prod.yml` (root) - includes backend and frontend compose files
- `backend/docker-compose.prod.yml` - backend services (postgres, backend, nginx, queue)
- `frontend/docker-compose.prod.yml` - frontend service

## Prerequisites

- Docker and Docker Compose installed
- Environment variables configured (see below)

## Environment Variables

Environment files should be configured in the **backend** and **frontend** directories. Each microservice manages its own environment variables.

### Setup Instructions

1. **Copy the example files to `.env` files:**
   ```bash
   # Backend
   cp backend/.env.example backend/.env
   
   # Frontend
   cp frontend/.env.example frontend/.env
   ```

2. **Update the values in each `.env` file** with your production configuration.

**Important:** 
- Generate `APP_KEY` for Laravel using: `php artisan key:generate` (run in backend directory)
- Update all placeholder values with your actual production values
- Never commit actual `.env` files to version control
- Each docker-compose.prod.yml file reads from its respective directory's `.env` file

## Building and Starting Services

1. **Build the production images:**
   ```bash
   docker compose -f docker-compose.prod.yml build
   ```

2. **Start all services:**
   ```bash
   docker compose -f docker-compose.prod.yml up -d
   ```

3. **Run database migrations:**
   ```bash
   docker compose -f docker-compose.prod.yml exec backend php artisan migrate --force
   ```

4. **Check service status:**
   ```bash
   docker compose -f docker-compose.prod.yml ps
   ```

## Service Management

### View Logs
```bash
# All services
docker compose -f docker-compose.prod.yml logs -f

# Specific service
docker compose -f docker-compose.prod.yml logs -f backend
docker compose -f docker-compose.prod.yml logs -f frontend_nginx
```

### Stop Services
```bash
docker compose -f docker-compose.prod.yml down
```

### Stop and Remove Volumes (⚠️ This will delete data)
```bash
docker compose -f docker-compose.prod.yml down -v
```

### Restart a Service
```bash
docker compose -f docker-compose.prod.yml restart backend
```

## Production Optimizations

The production setup includes:

- **Laravel Optimizations:**
  - Config caching
  - Route caching
  - View caching
  - OPcache enabled
  - Optimized autoloader

- **Frontend Optimizations:**
  - Production build (minified, optimized)
  - Nginx serving static files
  - Gzip compression
  - Cache headers for static assets

- **Security:**
  - No XDEBUG in production
  - Security headers
  - Hidden server tokens
  - No source code volumes (code baked into images)

## Services

- **postgres**: PostgreSQL database
- **backend**: Laravel PHP-FPM application
- **backend_nginx**: Nginx reverse proxy for API
- **queue**: Laravel queue worker
- **frontend_nginx**: Nginx serving React frontend

## Health Checks

All services include health checks. Check service health:

```bash
docker compose -f docker-compose.prod.yml ps
```

## Troubleshooting

### Database Connection Issues
Ensure the database is healthy before the backend starts:
```bash
docker compose -f docker-compose.prod.yml logs postgres
```

### Clear Laravel Cache
```bash
docker compose -f docker-compose.prod.yml exec backend php artisan cache:clear
docker compose -f docker-compose.prod.yml exec backend php artisan config:clear
docker compose -f docker-compose.prod.yml exec backend php artisan route:clear
docker compose -f docker-compose.prod.yml exec backend php artisan view:clear
```

### Rebuild After Code Changes
```bash
docker compose -f docker-compose.prod.yml build --no-cache
docker compose -f docker-compose.prod.yml up -d
```

## Notes

- The production setup uses named volumes for persistent data (database, storage, cache)
- Source code is baked into Docker images (no volume mounts)
- Ports are configurable via environment variables
- All services restart automatically unless stopped manually

