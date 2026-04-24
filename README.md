# proyectoAwsDespliegue

Proyecto de despliegue de microservicios en la nube usando Docker Compose. Implementa una arquitectura multi-servicio con tres servicios backend/frontend independientes, una base de datos PostgreSQL y una interfaz de administración, todos orquestados mediante contenedores Docker.

---

## Arquitectura

```
┌────────────────────────────────────────────────────────────┐
│                   Red Docker Compose                       │
│                                                            │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐   │
│  │   Django    │  │   FastAPI   │  │  Nginx (Angular) │   │
│  │  :8000      │  │  :8001      │  │  :4200           │   │
│  └──────┬──────┘  └──────┬──────┘  └─────────────────┘   │
│         │                │                                 │
│         └────────┬────────┘                                │
│                  │                                         │
│         ┌────────▼────────┐                                │
│         │   PostgreSQL 16 │                                │
│         │   :5432         │                                │
│         └─────────────────┘                                │
│                                                            │
│         ┌─────────────────┐                                │
│         │    pgAdmin 4    │                                │
│         │    :5050        │                                │
│         └─────────────────┘                                │
└────────────────────────────────────────────────────────────┘
```

---

## Servicios

| Servicio        | Tecnología       | Puerto externo | Descripción                         |
|-----------------|------------------|----------------|-------------------------------------|
| `servicio1`     | Django + Gunicorn| `8000`         | Backend Python con framework Django |
| `servicio2`     | FastAPI + Uvicorn| `8001`         | API REST asíncrona en Python        |
| `servicio3`     | Nginx + HTML     | `4200`         | Frontend estático (Angular ready)   |
| `postgres`      | PostgreSQL 16    | `5432`         | Base de datos relacional            |
| `pgadmin`       | pgAdmin 4        | `5050`         | Interfaz web de administración BD   |

---

## Requisitos previos

- [Docker](https://docs.docker.com/get-docker/) >= 20.x
- [Docker Compose](https://docs.docker.com/compose/install/) >= 2.x

---

## Instalación y uso

### 1. Clonar el repositorio

```bash
git clone <url-del-repositorio>
cd ucmcloud
```

### 2. Levantar todos los servicios

```bash
docker-compose up -d
```

### 3. Verificar que los contenedores están corriendo

```bash
docker-compose ps
```

### 4. Acceder a los servicios

| Servicio           | URL                                   |
|--------------------|---------------------------------------|
| Django             | http://localhost:8000                 |
| FastAPI            | http://localhost:8001                 |
| FastAPI Docs       | http://localhost:8001/docs            |
| Frontend (Angular) | http://localhost:4200                 |
| pgAdmin            | http://localhost:5050                 |

### 5. Detener los servicios

```bash
docker-compose down
```

Para detener y eliminar los volúmenes (borra los datos de la base de datos):

```bash
docker-compose down -v
```

---

## Configuración

### Base de datos PostgreSQL

| Variable          | Valor       |
|-------------------|-------------|
| `POSTGRES_USER`   | `postgres`  |
| `POSTGRES_PASSWORD` | `postgres` |
| `POSTGRES_DB`     | `mydatabase`|

### pgAdmin

| Variable                    | Valor                    |
|-----------------------------|--------------------------|
| `PGADMIN_DEFAULT_EMAIL`     | `pgadmin4@pgadmin.org`   |
| `PGADMIN_DEFAULT_PASSWORD`  | `admin1234`              |

Para conectar pgAdmin a la base de datos:
1. Ingresar a http://localhost:5050 con las credenciales anteriores.
2. Crear un nuevo servidor con host `postgres`, puerto `5432`, usuario `postgres` y contraseña `postgres`.

---

## Estructura del proyecto

```
ucmcloud/
├── docker-compose.yml       # Orquestación de todos los servicios
├── README.md
├── servicio1/               # Servicio Django
│   ├── Dockerfile
│   └── requirements.txt
├── servicio2/               # Servicio FastAPI
│   ├── Dockerfile
│   ├── main.py
│   └── requirements.txt
└── servicio3/               # Servicio Frontend (Nginx)
    ├── Dockerfile
    └── index.html
```

---

## Detalle de cada servicio

### Servicio 1 — Django

- **Base:** Debian 12 con Python 3 y entorno virtual
- **Servidor:** Gunicorn (producción)
- **Inicio:** Genera el proyecto Django automáticamente y aplica migraciones al construir la imagen
- **Puerto:** `8000`

### Servicio 2 — FastAPI

- **Base:** Debian 12 con Python 3 y entorno virtual
- **Servidor:** Uvicorn (ASGI)
- **Endpoint disponible:**

  ```
  GET /  →  {"message": "¡Hola, Mundo! FastApi"}
  ```

- **Documentación automática:** http://localhost:8001/docs (Swagger UI)
- **Puerto:** `8001`

### Servicio 3 — Frontend

- **Base:** Nginx Alpine (imagen mínima)
- **Contenido:** Página HTML estática lista para reemplazar por una aplicación Angular compilada
- **Puerto:** `4200`

---

## Comandos útiles

```bash
# Ver logs de un servicio específico
docker-compose logs -f django
docker-compose logs -f fastapi

# Reconstruir imágenes después de cambios
docker-compose up -d --build

# Acceder a la terminal de un contenedor
docker exec -it servicio_django bash
docker exec -it servicio_fastapi bash

# Ver uso de recursos
docker stats
```

---

## Stack tecnológico

- **Python 3** — Django 5.x · FastAPI · Gunicorn · Uvicorn
- **JavaScript** — HTML/Nginx (base para Angular)
- **Base de datos** — PostgreSQL 16
- **Infraestructura** — Docker · Docker Compose
- **SO base** — Debian 12 · Nginx Alpine
