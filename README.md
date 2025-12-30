# Milyfe - Main Repository

This is the main repository that orchestrates the Milyfe microservices architecture using Git submodules.

## Structure

This repository contains:
- `backend/` - Backend microservice (Git submodule)
- `frontend/` - Frontend microservice (Git submodule)
- `docker-compose.yml` - Main orchestration file that runs all services together

## Getting Started

### Setting Up Remote Repositories

Before cloning, you'll need to set up three separate remote repositories:

1. **Backend repository**: Create a remote repo (e.g., `milyfe-backend`)
2. **Frontend repository**: Create a remote repo (e.g., `milyfe-frontend`)
3. **Main repository**: Create a remote repo (e.g., `milyfe`)

Then update the submodule URLs in `.gitmodules` to point to your remote repositories:

```bash
# Update backend submodule URL
git config submodule.backend.url <your-backend-repo-url>
git submodule sync

# Update frontend submodule URL
git config submodule.frontend.url <your-frontend-repo-url>
git submodule sync
```

### Initial Setup

1. Clone this repository with submodules:
   ```bash
   git clone --recurse-submodules <repository-url>
   ```

   Or if you've already cloned:
   ```bash
   git submodule update --init --recursive
   ```

2. Start all services:
   ```bash
   docker-compose up -d
   ```

### Working with Submodules

#### Update submodules to latest
```bash
git submodule update --remote
```

#### Update a specific submodule
```bash
git submodule update --remote backend
git submodule update --remote frontend
```

#### Make changes in a submodule
1. Navigate to the submodule directory
2. Make your changes
3. Commit and push in the submodule repository
4. Return to main repository and commit the submodule reference update

## Individual Microservices

Each microservice can be run independently:

### Backend
```bash
cd backend
docker-compose up -d
```

### Frontend
```bash
cd frontend
docker-compose up -d
```

## Services

When running the main `docker-compose.yml`:
- **PostgreSQL**: Port 5432
- **Backend API**: Via Nginx on port 8080
- **Frontend**: Port 3000
- **Queue Worker**: Background service

