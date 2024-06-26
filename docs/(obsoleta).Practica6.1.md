---
title: 'Práctica 6.1 - Dockerización del despliegue de una aplicación con Node.js'
---

# Práctica 6.1 - Dockerización del despliegue de una aplicación Node.js

## Introducción

En este caso vamos a Dockerizar la aplicación que ya desplegamos en la [práctica 3.2](Practica3.2-NodeJS-Express.md).

### ¿Por qué *dockerizar*?

[Si uno trata de informarse](https://www.campusmvp.es/recursos/post/los-beneficios-de-utilizar-docker-y-contenedores-a-la-hora-de-programar.aspx), encontrará [múltiples y variadas razones](https://developerexperience.io/practices/dockerizing) para dockerizar nuestras aplicaciones y servicios.

Por citar sólo algunas:

**1. Configuración rápida del entorno en local para el equipo de desarrollo:** si todos los servicios están implementados con contenedores, es muy rápida la configuración de dicho entorno.

**2. Evita el clásico "en mi máquina funciona":** gran parte de los problemas de desarrollo provienen de la propia configuración que los integrantes del equipo de desarrollo tienen de su entorno. Con los servicios en contenedores, esto queda solucionado en gran medida.

**3. Despliegues más rápidos**

**4. Mejor control de versiones:** como ya sabéis, se puede etiquetar (tags), lo que ayuda en el CONTROL DE VERSIONES.

**5. Rollbacks más fáciles:** puesto que se tienen las cosas mas controladas por la versión, es más fácil revertir el código. A veces, simplemente apuntando a su versión de trabajo anterior.

**6. Fácil configuración de múltiples entornos:** como hacen la mayoría de los equipos de desarrollo, se establece un entorno local, de integración, de puesta en escena (preprod) y de producción. Esto se hace más fácil cuando los servicios están en contenedores y, la mayoría de las veces, con sólo un cambio de VARIABLES DE ENTORNO.

**7. Apoyo de la comunidad:** existe una fuerte comunidad de ingenieros de software que continuamente contribuyen con grandes imágenes que pueden ser reutilizadas para desarrollar un gran software. ¿Por qué reinventar la rueda, no?

## Despliegue con Docker

https://semaphoreci.com/community/tutorials/dockerizing-a-node-js-web-application

En primer lugar, si eliminastéis el repositorio en su momento, debéis volver a clonarlo en vuestra Debian, en caso contrario obviad este paso:

```sh
git clone https://github.com/contentful/the-example-app.nodejs.git
```
Ahora, puesto que la aplicación ya viene con el `Dockerfile` necesario dentro del directorio para construir la imagen y correr el contenedor, vamos a estudiar su contenido.

!!!task "Tarea"
    Completa este Dockerfile con las opciones/directivas adecuadas, leed los comentarios y podéis apoyaros [en la teoría](https://raul-profesor.github.io/DEAW/dockerfile/?h=dockerfile#mi-primer-dockerfile), en [este cheatsheet](https://devhints.io/dockerfile), en [este otro](https://kapeli.com/cheat_sheets/Dockerfile.docset/Contents/Resources/Documents/index) o en cualquiera que encontréis.

```yaml
_____ node:9 #(1) 

_____ /app #(2)

_____ npm install -g contentful-cli #(3)

_____ package.json . #(4)
_____ npm install #(5)

_____ . . #(6)

_____ node #(7)
_____ 3000 #(8)

_____ ["npm", "run", "start:dev"] #(9)
```



1. Con `_____` indicamos que vamos a utilizar la imagen de Docker Hub oficial de Node, en su versión 9   
2. `_____` define el directorio sobre el que se ejecutarán las subsiguientes instrucciones del `Dockerfile`   
3. `_____` ejecuta un comando en una nueva capa de la imagen (podemos tener varios comandos `_____`)
4. `_____` como su nombre indica, copia los archivos que le indiquemos dentro del contenedor, en este caso `package.json`    
    !!!info
        Recordemos que `package.json` cumplía ciertas funciones importantes:
         + Centraliza la forma de interactuar con la aplicación por medio de definición de scripts (indica comandos que podemos correr dentro de nuestro proyecto, asociándolos a una palabra clave para que npm (o yarn) los reconozca cuando queramos ejecutarlos.)
         + Gestiona de una forma clara y sencilla las dependencias necesarias para que la aplicación pueda funcionar correctamente.
5. Con otro `_____` ejecutamos el ya conocido comando que nos instala las dependencias que se indican en el archivo que hemos copiado en el paso anterior, el `package.json`
6. Copiamos todos los archivos de nuestro directorio de trabajo al contenedor
7. Con `_____` le indicaremos el usuario con el que correrá el contenedor
8. `_____` nos permite documentar que puertos están expuestos o a la escucha en el contenedor (sólo será accesible desde otros contenedores)
9.  Y finalmente `_____` nos permite ejecutar un comando **<u>dentro</u>** del contenedor. En este caso iniciamos la aplicación.

!!!note "Nota"
    En Linux, cuando queremos hacer referencia al directorio actual, lo hacemos con un punto `.` 

    Si dentro de nuestro directorio actual tenemos una carpeta llamada `prueba`, podemos hacer referencia a ella como `./prueba`, ya que el `.` hace referencia precisamente al directorio donde nos encontramos

Así pues, tener nuestra aplicación corriendo es cuestión de un par de comandos.

Hacemos un build de la imagen de Docker. Le indicamos que ésta se llama `the-example-app.nodejs` y que haga el build con el contexto del directorio actual de trabajo, así como del Dockerfile que hay en él:

```sh
docker build -t the-example-app.nodejs .
```

Y por último, iniciamos el contenedor con nuestra aplicación. Ahora sí, con la opción `-p`, le indicamos que escuche conexiones entrantes de cualquier máquina en el puerto 3000 de nuestra máquina anfitrión que haremos coincidir con el puerto 3000 del contenedor (`-p 3000:3000`). Y con la opción `-d` lo haremos correr en modo demonio, en background:

```sh
docker run -p 3000:3000 -d the-example-app.nodejs
```

Tras esto sólo queda comprobar que, efectivamente, desde nuestra máquina podemos acceder a: `http://IP_Maq_Virtual:3000` y que allí está nuestra aplicación en funcionamiento.

!!!task "Tarea"
    Documenta, incluyendo capturas de pantallas, el proceso que has seguido para realizar el despliegue de esta nueva aplicación, así como el resultado final.

## Referencias

[Los beneficios de utilizar Docker y contenedores a la hora de programar ](https://www.campusmvp.es/recursos/post/los-beneficios-de-utilizar-docker-y-contenedores-a-la-hora-de-programar.aspx)

[Dockerizing](https://developerexperience.io/practices/dockerizing)

[Github](https://github.com/contentful/the-example-app.nodejs)