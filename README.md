## Instrucciones 
Se tiene los archivos de ejemplo para mostrar una aplicación docker con python. 

**Empaquetar aplicación**

docker build -t app-flask .

**Ejecutar contenedor**
docker run -it -p 5000:5000 app-flask

**Run docker container using docker-compose**
docker-compose up -d --build


## Multi-stage builds 

```
# Primera imagen para compilar 
FROM python:3.8.4-slim-buster as compile-image
# Se define una variable opcional
RUN python3 -m venv /opt/venv
# Se sobreescribe la variable path para que tenga prioridad los comandos del ambiente
ENV PATH="/opt/venv/bin:$PATH"
# Se copia unicamente el archivo de dependencias 
COPY requirements.txt /requirements.txt
# Se instalan las dependencias.
RUN pip install -r requirements.txt
# Listo, inicia el segundo contenedor 
FROM python:3.8.4-alpine3.12 AS build-image
# Se copia la carpeta venv que contiene todas las dependencias en el segundo contenedor
COPY --from=compile-image /opt/venv /opt/venv
# Se copia la aplicación
COPY . usr/src/app
# Se establece por defecto el directorio 
WORKDIR /usr/src/app
# Se agrega el directorio a las variables de ambiente.
ENV PATH="/opt/venv/bin:$PATH"
# Arranca la aplicación
ENTRYPOINT python3 main.py
```

## Size of container
```
$ docker images
REPOSITORY                     TAG                   IMAGE ID       CREATED         SIZE
project_webflask               latest                961da471e720   2 minutes ago   ```**95.1MB**```
<none>                         <none>                6e3ba55bd728   2 minutes ago   184MB
```