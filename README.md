# SIGIP-Design-Service

Diseños: versiones del arte y aprobación del cliente.

## Funcionalidad

- `GET|POST|PUT|DELETE /api/v1/disenos` (escritura ADMIN; el cliente ve los de sus pedidos).
- Cada nueva versión reemplaza a la anterior; no se sube otra si ya hay una aprobada.
- `POST /api/v1/disenos/{id}/decision` → el cliente APRUEBA u OBSERVA (con comentario) la versión vigente.
- Valida el pedido en sales-service con Feign.

## Pruebas

- `DisenoRepositoryTest`, `DisenoFlujoIntegrationTest`

## Ejecución

Puerto `8096`. Base local `sigip_design` (MySQL); en la nube, esquema propio en PostgreSQL.

Variables: `DB_USER`, `DB_PASSWORD`, `JWT_SECRET` (o `DB_URL` completa).

## Repositorio

Este repositorio es uno de los 12 de SIGIP (un repositorio por microservicio). Es un proyecto Maven
independiente; su única dependencia propia es la librería **SIGIP-Common** (`pe.edu.cibertec.sigip:sigip-common`),
que vive en su propio repositorio y se instala en el Maven local una sola vez.

## Compilar y ejecutar

Requisitos: Java 25 (Maven no hace falta, cada repo trae `mvnw`).

```powershell
# 1. Una sola vez (y cada vez que cambie SIGIP-Common): instalar la librería compartida
cd ..\SIGIP-Common
.\mvnw.cmd install -DskipTests

# 2. Este servicio
cd ..\SIGIP-Design-Service
.\mvnw.cmd clean verify          # compila y ejecuta las pruebas
.\mvnw.cmd spring-boot:run       # arranca en el puerto 8096
```

En Linux/macOS usa `./mvnw` en lugar de `.\mvnw.cmd`.

## Docker

La imagen compila también SIGIP-Common, así que necesita su carpeta como contexto adicional:

```powershell
docker build --build-context common=..\SIGIP-Common -t sigip/design-service .
```

Para levantar todo SIGIP junto (los 12 repos, MySQL y RabbitMQ) usa el `docker-compose.yml` de la carpeta
`SIGIP` que contiene a los 12 repositorios.
