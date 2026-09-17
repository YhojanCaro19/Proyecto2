# Proyecto 2 — WordPress persistente con Docker Compose

Sitio WordPress con base de datos MySQL, orquestado con Docker Compose, con persistencia de datos mediante volúmenes nombrados.

## Descripción

Dos servicios conectados por una red personalizada:
- **db**: MySQL 8, con healthcheck y datos en el volumen `db_data`.
- **wordpress**: WordPress, espera a que `db` esté saludable antes de arrancar, archivos en el volumen `wp_data`.

## Requisitos

- Docker y Docker Compose instalados y corriendo

## Instrucciones de ejecución

1. Copiar la plantilla de variables de entorno:

\`\`\`bash
cp .env.example .env
\`\`\`

2. Levantar los servicios:

\`\`\`bash
docker compose up -d
\`\`\`

3. Verificar el estado:

\`\`\`bash
docker compose ps
\`\`\`

4. Abrir el sitio en el navegador:

\`\`\`
http://localhost:8000
\`\`\`

## Prueba de persistencia

\`\`\`bash
docker compose down   # elimina contenedores y red, NO los volúmenes
docker compose up -d  # vuelve a levantar todo
\`\`\`

El contenido publicado en WordPress (entradas, usuarios, medios) sigue disponible porque vive en los volúmenes nombrados `db_data` y `wp_data`, no dentro de los contenedores.

## Evidencias

Ver documento PDF de evidencias adjunto (capturas de instalación, entrada de prueba antes/después de `docker compose down`, y `docker volume ls`).

## Preguntas

**¿Qué comando eliminaría también los datos y por qué debe usarse con cuidado?**

`docker compose down -v` elimina además los volúmenes nombrados, borrando permanentemente la base de datos y los archivos subidos. Debe usarse con mucho cuidado porque no hay forma de recuperar esa información después.

**¿Por qué WordPress se conecta al host `db` y no a una dirección IP?**

Docker Compose crea una red personalizada donde cada servicio es accesible por su nombre gracias al DNS interno de Docker. Las IPs de los contenedores pueden cambiar cada vez que se recrean, pero el nombre del servicio (`db`) siempre resuelve al contenedor correcto.

**¿Qué aporta el healthcheck frente a un depends_on simple?**

Un `depends_on` simple solo espera a que el contenedor de la base de datos *arranque*, no a que MySQL esté realmente listo para aceptar conexiones. El `healthcheck` verifica que el servicio responda de verdad, y `condition: service_healthy` hace que WordPress espere ese estado antes de iniciar, evitando errores de conexión al arrancar.
