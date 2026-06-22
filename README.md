# Backend DevOps

API REST de inventario y tickets para Innovatech Chile, desarrollada con Node.js y Express.

## 🚀 Descripción
Backend que proporciona los servicios API para la gestión de inventario y sistema de tickets. Integrado con base de datos MySQL y preparado para despliegue en AWS.

## 📋 Requisitos
- Node.js v18+
- Docker y Docker Compose
- MySQL/MariaDB (via Docker o local)
- AWS CLI (solo para despliegue en producción)

## 🛠️ Stack Tecnológico
- **Express.js** ^4.21.1
- **MySQL2** ^3.11.3
- **CORS** ^2.8.5
- **Dotenv** ^16.4.5

## 📦 Instalación

### Modo Desarrollo (sin Docker)
```bash
npm install
npm run dev
```

### Con Docker Compose
```bash
docker-compose up --build
```

## 🔨 Comandos Disponibles
```bash
npm run dev    # Inicia servidor con watch
npm start      # Inicia servidor en producción
```

## 🌍 Puerto
- **3001** (API REST)

## 🔐 Variables de Entorno
Crear archivo `.env` con las siguientes variables:
```
PORT=3001
DB_HOST=localhost
DB_PORT=3306
DB_NAME=innovatech
DB_USER=innovatech_user
DB_PASSWORD=innovatech_pass
CORS_ORIGIN=http://localhost:80
```

> Ver `.env.example` para valores de referencia

## 📡 Endpoints Principales
- `GET /health` - Health check del servidor
- `GET /api/dashboard` - Dashboard con estadísticas
- `GET /api/items` - Obtener todos los items del inventario
- `POST /api/items` - Crear nuevo item
- `GET /api/tickets` - Obtener todos los tickets
- `POST /api/tickets` - Crear nuevo ticket

## 📂 Estructura del Proyecto
```
src/
├── app.js      # Configuración de Express
├── db.js       # Configuración de base de datos
└── server.js   # Punto de entrada
```

## 🐳 Docker
La aplicación está containerizada y lista para despliegue. Los servicios se configuran a través de `docker-compose.yml`.

## 🔄 Integración con Base de Datos
El backend se conecta automáticamente a la base de datos MySQL configurada en las variables de entorno.

## 📝 Notas
- Asegúrate de que la base de datos esté ejecutándose antes de iniciar el backend
- Los puertos deben estar disponibles (3001 para API, 3306 para BD)
