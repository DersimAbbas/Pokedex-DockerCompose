# Pokedex Docker Compose

## Overview

A combined solution with two parts:

- **Frontend**  
  - React (Create React App) displaying a list of Pokémon.  
  - Served in production via nginx, with `/api/` requests proxied to the backend.

- **Backend**  
  - Azure Functions (.NET 8 isolated) providing CRUD endpoints to a MongoDB-like Cosmos DB.  
  - RoutePrefix = `api`, with CORS configured to allow the frontend.

---

## Tech Choices

- **React (frontend)**  
  - Rapid setup via Create React App.  
  - Bootstrap for basic styling.  
  - In production, built into static files and served by nginx.

- **Azure Functions (.NET 8, isolated)**  
  - Serverless architecture easily containerized.  
  - MongoDB driver connecting to Cosmos DB API.  
  - CORS configured via `host.json` or `CORS_ALLOWED_ORIGINS` in Docker.

- **Containerization**  
  - Frontend: multi-stage Dockerfile (Node → build → nginx).  
  - Backend: Dockerfile based on `mcr.microsoft.com/azure-functions/dotnet-isolated:4-dotnet-isolated`.  
  - Docker Compose creates an internal network (`app-network`): frontend listens on port 80, backend is exposed internally on 8081.

---
**Note:**

- You must provide your own MongoDB connection inside the compose.yml file or dockerfile. (either a local MongoDB instance or MongoDB Atlas).
- In the frontend, type a Pokémon name into the form field and submit. The Azure Function will first fetch that Pokémon’s stats from the public PokéAPI, then save it to your database. React will automatically update the list with the new Pokémon, including its stats.

## Quickstart (local)

1. **Clone and start**  
   ```bash
   git clone https://github.com/DersimAbbas/Pokedex-DockerCompose.git
   cd Pokedex-DockerCompose
   docker-compose up --build
