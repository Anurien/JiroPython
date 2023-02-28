# Jiro Python

En este proyecto levantaremos un contenedor de docker con una imagen de Odoo en un entorno python.
Para empezar tenemos que crear un archivo [docker-compose.yml](https://github.com/GorillaGrip/odoo/commit/8d6f09ff7e187558d47d27662984c514928c0552)
Ejemplo:
```
version: '3.1'
services:
  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_PASSWORD=odoo
      - POSTGRES_USER=odoo
    ports:
      - "5432:5432"
```
donde:  
`image`: establece la imagen a partir de la cual se generará el servicio  
`ports`:Ports nos dice los puertos que expondremos al exterior y a cual 
puerto de nuestra máquina se vincularán, siempre en el formato de HOST:CONTENEDOR.  
`environment`: La configuración environment nos permite establecer una lista
de variables de entorno que estarán disponibles en nuestro servicio.

Hasta aquí tendriamos solamente la base de datos en postgres levantada, como la información de la base de datos desaparece cada vez que el contenedor se cierra, hay que crear un volumen para almacenar esa información:
```
    volumes:
      - dam22_postgres:/var/lib/postgresql/data
```
El nombre de nuestro volumen es 'dam22_postgres' y la ruta es donde Postgresql guarda sus datos (por defecto es siempre la misma en todos los equipos), así toda la información de postgresql se guardará en el volumen.
El siguiente paso es descargar la imagen de Odoo:
``` 
  odoo:
   image: odoo:14.0
   container_name: dam22_odoo
   ports:
     - "8069:8069"
   depends_on:
     - db
```
Damos nombre al contenedor y especificamos los puertos, el puerto por defecto de Odoo es el ```8069```.
Para mas información esta es [la guía](https://hub.docker.com/_/odoo) utilizada para realizar este proyecto.

`depends_on`: depends_on nos permite que el inicio de la ejecución de un servicio dependa de otras. En palabras más sencillas, le dice a docker-compose que deseamos arrancar el servicio web solo si ya se han cargado todos los demás servicios.
