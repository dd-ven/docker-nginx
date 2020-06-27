# Web Proxy con Docker, NGINX and Let's Encrypt

Con este repositorio podrá configurar su servidor con múltiples sitios utilizando un solo proxy NGINX para administrar sus conexiones, automatizando su contenedor de aplicaciones (puerto 80 y 443) para renovar automáticamente sus certificados SSL con Let´s Encrypt.

## Prerrequisitos

Para utilizar este archivo de composición (docker-compose.yml) debe tener:
1. docker (https://docs.docker.com/engine/installation/)
2. docker-compose (https://docs.docker.com/compose/install/)


## Instalación

1. Clonar repositorio: ``git clone https://github.com/dd-ven/docker-nginx.git``
2. Acceder al directorio: ``cd docker-nginx``
3. Crear una copia de ``.env.sample`` como ``.env`` y modificar los ajustes necesarios
      1. ``cp .env.sample .env``
      2. ``nano .env``
4. Arrancar el script: ``./start.sh``

## Añadir contenedores al proxy

Después de seguir los pasos anteriores, puede iniciar nuevos contenedores web con el puerto 80 abierto y agregar la opción ``-e VIRTUAL_HOST = your.domain.com`` para que el proxy genere automáticamente la secuencia de comandos inversa en NGINX Proxy para reenviar nuevas conexiones a su contenedor web / aplicación, a partir de:

````
docker run -d -e VIRTUAL_HOST=your.domain.com \
              --network=webproxy \
              --name my_app \
              httpd:alpine
````

Para tener SSL en su web / aplicación, simplemente agregue la opción ``-e LETSENCRYPT_HOST = your.domain.com`` de la siguiente manera:

````
docker run -d -e VIRTUAL_HOST=your.domain.com \
              -e LETSENCRYPT_HOST=your.domain.com \
              -e LETSENCRYPT_EMAIL=your.email@your.domain.com \
              --network=webproxy \
              --name my_app \
              httpd:alpine
````
- en docker compose:

````
version: '3'

services:
  app:
    image:
    environment:
      - VIRTUAL_HOST=your.domain.com
      - LETSENCRYPT_HOST=your.domain.com
      - LETSENCRYPT_EMAIL=your.email@your.domain.com
networks:
    default:
        external:
            name: webproxy
````


No necesita abrir el puerto 443 en su contenedor, la validación del certificado es administrada por el proxy web.
Tenga en cuenta que cuando se ejecuta un nuevo contenedor para generar certificados con LetsEncrypt (``-e LETSENCRYPT_HOST = your.domain.com``), puede tomar unos minutos, dependiendo de las circunstancias múltiples.

## Creditos

Este repositorio es una copia de ``https://github.com/alexkutsan/docker-compose-letsencrypt-nginx-proxy-companion.git`` modificada y simplificada. Se ha modificado ``.env.sample`` para permitir cargas de más de 2 megas.