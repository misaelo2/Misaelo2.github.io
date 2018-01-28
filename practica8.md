Hoy comenzaremos con algo que lo esta petando ultimamente , contenedores , en especial , los de docker .

1. [Ejecutando un servicio en un contenedor docker](#tarea1)

2. [Creacion de una imagen de docker con Dockerfile](#tarea2)

3. [Configuracion de contenedores por medio de variables de entorno](#tarea3)

4. [Creacion automatica de una imagen docker en dockerHub](#tarea4)


# Tarea1

Primeros , tenemos que instalar docker , dado que en muchos sitios te explican como instalar docker de distintas fuentes ( y seguramente mejor que yo) pasaremos directamente a usar docker una vez instalado :

-En docker hub encontramos un repositorio con imagenes de contenedores de servicios o incluso SO para usar , vamos a crear un contenedor docker interactivo con una imagen oficial de debian y vamos a instalarse el servidor Nginx 

Nos bajamos la imagen 

~~~
docker pull debian
~~~

podemos ver si nos hemos descargado la imagen y las imagenes que tenemos con el comando 

~~~
docker images
~~~

como siempre el comando "docker --help" sera nuestro gran amigo a la hora de enfrentarnos contra este señor 

Con la imagen que nos hemos descargados , ejecutamos un contenedor 

~~~
docker run -i -t --name primero debian /bin/bash
~~~

la opcion -i es para decirle que sera un contenedor interactivo , la -t para ordenarlo que abra una terminal en el contenedor , y el comando escrito al final es lo que se ejecutara en el contenedor 

Ahora ejecutamos los comandos necesario para instalar nginx 

~~~
apt-get update 
apt-get install nginx
~~~

Ahora vamos a crear una  imagen con ese contenedor que hemos creado para que asi tener una imagen con nginx lista  para el despliege 

docker commit -m "añadido nginx" -a "misaelo" 926045cfed4e misael/nginx:v1


Si ahora listamos la imagen debe aparecernos el nombre de la nuestra 

Lo que debemos hacer ahora es crear un contenedor nuevo con la imagen que hemos creado , que incluira nginx ya .
~~~
docker run -d -p 8080:80 --name segundo misael/nginx:v1 /usr/sbin/nginx -g "daemon off;" 
~~~

con la opcion - d le especificamos a docker para que ejecute el contenedor como un demonio , -p  para especificar el mapeo de puertos y por ultimo escribi el comando a ejecutar 

si accedemos a la ip del servidor docker por la ip y el puerto 8080 , nos aparecera la pagina principal del nginx del contenedor .

![nginx](capturas/nginx.png)


una vez comprobado que el contenedor esta hecho correctamente , nos creamos una cuenta en docker hub y lo subimos a nuestro repositorio 

~~~
docker login
docker tag misael/nginx:v1 misaelo/nginx:v1
docker push misaelo/nginx:v1
~~~

el comando docker tag sirve para etiquetar nuestra imagen con diferentes versiones para documentar los cambios que vamos haciendo

la imagen aparecera en nuestro docker cloud 

![docker](capturas/Captura de pantalla de 2018-01-23 13-20-26.png)

Si ahora queremos descargarnos esta imagen en el entorno de produccion solo tendriamos que ejecutar el comando que nos dice la pagina de docker cloud

~~~
docker push misaelo/nginx:v1
~~~

y para ejecutarlo docker start misaelo/nginx:v1

Para hacer Mysql en un Contenedor :

~~~
docker pull mysql
~~~

Ahora ejecutamos una instancia de servidor mysql

~~~
 docker run --name mysql-server -e MYSQL_ROOT_PASSWORD=root -d mysql
~~~

Ahora nos conectamos al contenedor con la linea de comandos de mysql

~~~
docker run -it --link mysql-server:mysql --rm mysql sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'

~~~

Y si todo ha salido bien nos aparecera en el prompt mysql para introducir comandos 


![mysql_docker](capturas/Captura de pantalla de 2018-01-24 09-40-11.png)


# Tarea2 

Ahora veremos la creacion de contenedores mediante archivos dockerfile .


Creamos un directorio vacio y accedemos a el , y creamos un archivo llamado "Dockerfile"

En ese archivo añadimos las siguientes lineas 

~~~
* cojemos la imagen debian 

FROM debian:stretch

* el creador del contenedor 
MAINTAINER misael

* los comandos a realizar cuando se ejecute el contenedor
RUN apt-get update \
&& apt-get install -y nginx

* Seleccionar el puerto por el que escuchara nuestra maquina
EXPOSE 80

* Realizar los comandos una vez creado el contenedor
CMD ["nginx", "-g", "daemon off;"]
~~~ 


Con esto , solo nos queda construir una imagen  a travez de nuestro docker file 

~~~
Docker build -t miprimeraimagencondockerfile ./ 
~~~

* NOTESE QUE ESPECIFICO DONDE SE ENCUENTRA MI DOCKERFILE

A partir de aqui , solo nos queda crear un contenedor a partir de la imagen generada por el dockerfile 

~~~
docker run --name miprimerdockerfile -d  -p 80:80 nginx
~~~

* la opcion -p indica la redireccion de puertos que haremos en nuestra propia maquina , y la ultima opcion especifica la imagen

Ahora accedemos a la ip de nuestra maquina y nos redirigira al contenedor 

![contenedor con Dockerfile](capturas/Captura de pantalla de 2018-01-26 09-52-34.png)

Si queremos modificar ahora el contenido de un fichero dentro del contenedor añadimos al dockerfile 

~~~
ADD ./index.html /var/www/html/
~~~
 
por como esta configurado nginx por defecto , accedera a este fichero .

creamos otra vez la imagen pero aumentamos la version 

~~~
docker build -t nginx:v2 ./
docker run --name misegundocontenedordocker -d  -p 80:80 nginx:v2
~~~

Ahora accedemos al contenedor :

![nginx modificado](capturas/Captura de pantalla de 2018-01-26 10-07-01.png)

# Tarea3

-Ahora configuraremos contenedor por medios de variables de entorno y configuraremos nginx mediante un script de nuestra propia mano que ejecutara el contenedor 

Aqui esta nuestro Dockerfile 
~~~
FROM debian:stretch

MAINTAINER misael

ENV DOCUMENTROOT /var/www/html
ENV SERVERNAME  supercontenedor
ADD ./dngix.sh /
ENTRYPOINT ["/bin/bash","/dnginx.sh"]

#RUN apt-get update \
#&& apt-get install -y nginx

EXPOSE 80

#CMD ["nginx", "-g", "daemon off;"]
~~~

Hemos comentado las lineas porque todo eso lo haremos con nuestro script siguiente 

~~~
!#/bin/bash

apt-get update && apt-get install -y nginx 

echo "contenedor creado mediante variables"  > $DOCUMENTROOT/index.html

nginx -g "daemon off;"
~~~


creamos la imagen como antes y el contenedor con 

~~~
docker build -t tarea3imagen 
docker run --name tarea3container -p 80:80 -d 
~~~
Ahora al crear la imagen y acceder al server name nos aparece tal como lo queremos 

![tarea3docker](capturas/Captura de pantalla de 2018-01-28 12-32-55.png)

- Ahora modicaremos las variables de nuestro entorno en nuestro script y veremos el resultado 

modificamos la variable de entorno de "DOCUMENTROOT" 

~~~
FROM debian:stretch

MAINTAINER misael

ENV DOCUMENTROOT /var/www/tarea3docker
ENV SERVERNAME  supercontenedor
ADD ./dngix.sh /
ENTRYPOINT ["/bin/bash","/dnginx.sh"]
EXPOSE 80
~~~

Ahora solo queda añadir al script que cree la carpeta con los permisos adecuador , y al lanzar el contenedor otra vez :

![tarea3docker](capturas/Captura de pantalla de 2018-01-28 12-32-55.png)

Pero esta vez , en otro documentROOT 


# Tarea4 

Ahora vamos a configurar un despliege automatico de nuestro repositorio automatico a nuestro docker hub 

creamos un repositorio para docker y alli subimos nuestros archivos a traves de una maquina que no tenga docker 

Como vemos , ya tenemos el escenario en nuestro github 

![tarea4dcoker](capturas/Captura de pantalla de 2018-01-28 12-52-15.png)

Ahora eliminamos en nuestro servidor docker los contenidos para luego bajarlos de nuevo y simular un entorno de desarrollo , pero antes ,

Tenemos que configurar en nuestro docker hub un "automatic builds" y darle permisos de nuestro repositorio de github 

![tarea4dockehub](capturas/Captura de pantalla de 2018-01-28 12-56-44.png)


![dandopermisos](capturas/Captura de pantalla de 2018-01-28 12-58-04.png)

-Ahora solo tenemos que crear un nuevo repositorio desde el panel grafico 

![tarea4creando](capturas/Captura de pantalla de 2018-01-28 13-01-15.png)

Seleccionamos el repositorio y algun comentario si queremos 

![thefinaldcountdown](capturas/Captura de pantalla de 2018-01-28 13-02-35.png)

como vemos ,  ya tenemos otro repo de docker listo para traernos con docker pull 


-Ahora solo queda crear el contenedor con docker run y acceder a este 

Si queremos cambiar el contenido del fichero , lo que tenemos que hacer es modificar el index.html , hacer un push en github y automaticamente se lanzara una imagen en el docker hub , que tendremos que bajarnos y lanzar de nuevo el contenedor 

![trigering](capturas/Captura de pantalla de 2018-01-28 13-37-01.png)

Con esto "forzamos" a docker build a desplegar automaticamente los cambios 

Ahora bajamos la imagen y creamos un contenedor 

~~~
docker pull misaelo/tareadocker
docker run -d  --name tarea4ocker -p 80:80 misaelo/tareadocker:latest
~~~

![docker4fin](capturas/Captura de pantalla de 2018-01-28 13-53-41.png)



