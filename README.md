# Backend-Devops

<<<<<<< Updated upstream
API Node.js + Express + MySQL para Innovatech Inventory System

## Setup Local

```bash
npm install
npm start            # Iniciar servidor puerto 3001
```

## Configurar .env

```
PORT=3001
DB_HOST=10.0.3.238
DB_PORT=3306
DB_NAME=innovatech_ops
DB_USER=innovatech_user
DB_PASSWORD=innovatech_password
CORS_ORIGIN=http://10.0.1.10
```

## Docker

```bash
docker build -t innovatech-backend .
docker run -p 3001:3001 --env-file .env innovatech-backend
```

## Deploy

Push a rama `deploy` dispara GitHub Actions que:
1. Compila imagen Node.js
2. Push a ECR
3. Despliega en EC2 via SSM

## Endpoints

- `GET/POST /api/products` - Gestión de productos
- `GET/POST /api/tickets` - Gestión de tickets

## Estructura

- `src/` - Código Express (app.js, db.js, server.js)
- `Dockerfile` - Build Node.js Alpine
- `.github/workflows/backend-deploy.yml` - CI/CD
# Compilar imagen
docker build -t innovatech-backend:latest .

# Ejecutar contenedor
docker run -p 3001:3001 \
  -e DB_HOST=host.docker.internal \
  -e DB_PORT=3306 \
  -e DB_NAME=innovatech_ops \
  -e DB_USER=innovatech_user \
  -e DB_PASSWORD=innovatech_password \
  -e CORS_ORIGIN=http://localhost:3000 \
  innovatech-backend:latest
```

### Docker Compose Local

```bash
# Iniciar todos los servicios (backend + base de datos)
docker-compose up

# Ejecutar en segundo plano
docker-compose up -d

# Ver logs
docker-compose logs -f backend

# Detener servicios
docker-compose down
```

## Estrategia de Compilación Docker

Dockerfile Node.js optimizado:
- Usa imagen Node 18 Alpine (huella pequeña)
- Instala solo dependencias de producción (npm ci --omit=dev)
- Copia código fuente
- Expone puerto 3001
- Ejecuta comando npm start

Tamaño de imagen final: aproximadamente 350MB

## Endpoints API

### Productos

```
GET /api/products             - Listar todos los productos
POST /api/products            - Crear producto
GET /api/products/:id         - Obtener detalles del producto
PUT /api/products/:id         - Actualizar producto
DELETE /api/products/:id      - Eliminar producto
```

### Tickets

```
GET /api/tickets              - Listar todos los tickets
POST /api/tickets             - Crear ticket
GET /api/tickets/:id          - Obtener detalles del ticket
PUT /api/tickets/:id          - Actualizar ticket
DELETE /api/tickets/:id       - Eliminar ticket
```

## Conexión a Base de Datos

El backend se conecta a MySQL al iniciar:

```javascript
// src/db.js
const connection = mysql.createConnection({
  host: process.env.DB_HOST,
  port: process.env.DB_PORT,
  database: process.env.DB_NAME,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD
});
```

La agrupación de conexiones puede configurarse para uso en producción.

## Configuración CORS

CORS está habilitado para el origen del Frontend especificado en variables de entorno:

```
CORS_ORIGIN=http://10.0.1.10
```

Solo se permiten solicitudes de este origen para realizar llamadas API de origen cruzado.

## Deployment

Automatizado vía GitHub Actions en push a rama `deploy`.

### Flujo de Deployment

1. GitHub Actions obtiene el código
2. Compila imagen Docker desde Dockerfile
3. Hace push a repositorio AWS ECR
4. Envía comando SSM a Backend EC2
5. EC2 descarga imagen más reciente e inicia contenedor
6. Contenedor vincula al puerto 3001

### Deployment Manual

```bash
# Push a rama deploy
git checkout deploy
git commit --allow-empty -m "Desplegar backend"
git push origin deploy

# Monitorear en GitHub Actions
# Ver logs: docker logs innovatech_backend
```

## Gestión de Dependencias

Package.json incluye:
- express: Framework web
- mysql: Driver de base de datos
- cors: Compartir recursos entre orígenes
- dotenv: Carga de variables de entorno

Nota: package-lock.json es requerido para compilaciones Docker usando npm ci.

## Solución de Problemas

### Backend no puede conectar a base de datos

Verificar:
- Contenedor de base de datos está corriendo: docker ps
- Logs de MySQL: docker logs innovatech_db
- Credenciales en .env coinciden con configuración DB-Devops
- Conectividad de red entre Backend y Database
- Security groups permiten puerto 3306 de Backend a Database

### Puerto ya está en uso

Si puerto 3001 está ocupado:
- Encontrar proceso: lsof -i :3001
- Matar proceso: kill -9 <PID>
- O cambiar PORT en .env

### Errores CORS en Frontend

- Verificar variable CORS_ORIGIN coincide con IP del Frontend
- Verificar Frontend está haciendo solicitudes a URL correcta del Backend
- Revisar consola del navegador para detalles específicos del error

### Deployment SSM falla

- Verificar que labrole tiene política AmazonSSMManagedInstanceCore
- Verificar SSM Agent está corriendo en instancia Backend
- Revisar logs de GitHub Actions para detalles del error
- Verificar que Backend Instance ID se resuelve correctamente

## Optimización de Rendimiento

- Agrupación de conexiones para consultas de base de datos
- Compresión gzip para respuestas
- Diseño eficiente de consultas SQL
- Indexación apropiada en tablas de base de datos
- Optimizaciones específicas del entorno

## Consideraciones de Seguridad

- Variables de entorno para datos sensibles
- Validación de entrada en endpoints API
- Prevención de inyección SQL vía consultas parametrizadas
- CORS restringido solo al origen del Frontend
- Acceso SSH no requerido (usa Systems Manager)
- Credenciales AWS gestionadas vía roles IAM

## Monitoreo

Verificar estado de la aplicación vía SSM:

```bash
# Ver procesos en ejecución
docker ps

# Ver logs
docker logs innovatech_backend

# Verificar vinculación de puerto
docker port innovatech_backend

# Verificar conectividad a base de datos
docker logs innovatech_backend | grep -i connection
```

## Flujo de Trabajo de Desarrollo

1. Realizar cambios al código fuente
2. Probar localmente con docker-compose
3. Hacer commit y push a rama main
4. Mezclar con rama deploy
5. GitHub Actions dispara deployment
6. Monitorear aplicación en EC2


=======
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
>>>>>>> Stashed changes
