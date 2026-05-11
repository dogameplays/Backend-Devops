# Backend DevOps

API de inventario y tickets para Innovatech Chile.

## Puerto
- 3001 (API)

## Requisitos
- Docker y Docker Compose
- AWS CLI (solo para despliegue)

## Variables de entorno
Ver .env.example para valores de referencia.

- PORT
- DB_HOST
- DB_PORT
- DB_NAME
- DB_USER
- DB_PASSWORD
- CORS_ORIGIN

## Endpoints principales
- GET /health
- GET /api/dashboard
- GET /api/items
- POST /api/items
- GET /api/tickets
- POST /api/tickets
