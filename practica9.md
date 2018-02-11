Despliege de aplicaciones en Dockerfiles

1. [Ejecucion de una aplicacion web php en Docker ](#tarea1)

2. [Ejecucion de una aplicacion web php en Docker (2) ](#tarea2)

3. [Ejecucion de una aplicacion web php en Docker (3)](#tarea3)

4. [Ejecucion de CMS en docker ](#tarea4)

5. [Ejecucion de CMS en docker (2) ](#tarea5)



# Tarea1

Para el despliege he elegido una aplicacion escrita en php que no dispone de repositorio oficial , asi nosotros nos la aviamos para desplegar esta aplicacion en contenedores 

Clonamos el repositorio de [bookmedik](https://github.com/evilnapsis/bookmedik)

Primero , creamos la base de datos :

~~~
docker pull mysql/mysql-server
~~~

Ahora ejecutamos un contenedor que contendra un servidor mysql 

~~~
 docker run --name mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=bookmedik -e MYSQL_USER=bookmedik -e MYSQL_PASSWORD=bookmedik  -d mysql
~~~

Cargamos en la base de datos el archivo "schema.sql" 

~~~
cat schema.sql | docker exec -i mysql /usr/bin/mysql -u bookmedik --password=bookmedik bookmedik
~~~

Con esto hemos creado La base de datos en en nuestro contenedor 
Ahora , como no queremos estar creando constantemente la base de datos al crear y destruir contenedores,
vamos a ver volumenes :

~~~
docker volume create --name bookmedik
~~~

con esto creamos un volumen que usaremos para conectarlos a los contenedores
pero vamos a borrarlo y a crear el volumen justo al crear el contenedor de la base de datos 

~~~
docker volume rm  bookmedik
docker run --name mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=bookmedik -e MYSQL_USER=bookmedik -e MYSQL_PASSWORD=bookmedik  -d \
-v bookmedik:/var/lib/mysql mysql
cat schema.sql | docker exec -i mysql /usr/bin/mysql -u bookmedik --password=bookmedik bookmedik
~~~


~~~
Con esto , si eliminamos el contenedor , solo tenemos que enganchar el volumen a otro contenedor mysql 
Para comprobar la informacion contenida en el contenedor :
~~~

~~~
docker inspect bookmedik 
~~~

Aqui nos mostrara el directorio en nuestro sistema de archivos local donde guarda los datos 



Ahora creamos un dockerfile con una imagen base de debian y añadimos la aplicacion 
(acordad situarnos en la carpeta del directorio creado)
~~~
FROM debian 


ADD . /var/www/html

RUN apt-get update \
&& apt-get install -y apache2 \
&& apt-get install  -y php libapache2-mod-php

EXPOSE 80

CMD apachectl -D FOREGROUND
~~~

Antes de buildear la imagen tenemos que retocar los parametros de la conexion en la base de datos en core/controller/Database.php

~~~
<?php
class Database {
        public static $db;
        public static $con;
        function Database(){
                $this->user="bookmedik";$this->pass="bookmedik";$this->host="mysql";$this->ddbb="bookmedik";
        }

        function connect(){
                $con = new mysqli($this->host,$this->user,$this->pass,$this->ddbb);
                $con->query("set sql_mode=''");
                return $con;
        }

        public static function getCon(){
                if(self::$con==null && self::$db==null){
                        self::$db = new Database();
                        self::$con = self::$db->connect();
                }
                return self::$con;
        }

}
?>
~~~

Buildeamos la imagen 

~~~
Docker build -t bookmedik .
~~~

Ahora creamos el contenedor con un volumen para guardar los logs de apache y lo enlazamos con el servidor mysql para que resuelva los nombres 

~~~
docker run --name bookmedik -d --link mysql:mysql -v logs_bookmedik:/var/log/apache2/ -p 80:80 bookmedik 
~~~



Ahora accedemos a la direccion de la maquina host 

![conseguido ](capturas/Captura de pantalla de 2018-02-07 19-41-01.png)



# Tarea2

Ahora vamos a desplegar la aplicacion con la imagen oficial de php de Docker Hub 

Segun la documentacion oficial de la imagen , para desplegar php con apache usamos el siguiente dockerfile 

~~~
FROM php:7.0-apache

ADD . /var/www/html

#instalamos dependencias necesarias 

RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli
~~~

Ahora etiquetamos la imagen y la subimos a nuestro docker hub :

~~~
docker tag bookmedik_php:latest  misaelo/bookmedik:v1
docker push misaelo/bookmedik:v1
~~~

![php_bookmedik](capturas/Captura de pantalla de 2018-02-11 11-36-44.png)

-Aqui esta mi repositorio de GitHub con el contexto de la imagen : [bookmedik](https://github.com/misaelo2/Bookmedik)

Con esta imagen y nuestro antiguo contenedor de mysql , podemos crear la aplicacion , pero esta ves lanzaremos los dos mediante docker compose 

docker compose no es mas que una herramienta que nos permite desplegar varios contenedores con la configuracion deseado que le hemos escrito en un archivo YML previamente definido en vez de tener que ejecutar "docker run" . Si habeis visto Ansible , es una cosa Muy parecida 


Instalamos Docker Compose 

~~~
curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose 
docker-compose --version 
~~~

Con este ultimo comando podemos ver la version instalada de docker-compose , Ahora pasaremos a definir el escenario con un fichero YML :

~~~
mysql:
   image: mysql
   environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: bookmedik 
      MYSQL_USER: bookmedik
      MYSQL_PASSWORD: bookmedik   
   volumes:
     - bookmedik:/var/lib/mysql
bookmedik_php
   image: bookmedik_php
   links: 
      -mysql
   ports:
      -"80:80"
~~~
* Nota , el fichero debe llamarse "docker-compose.yml"

Ahora levantamos la herramienta docker-compose y creamos el escenario 

~~~
docker-compose up -d
~~~

- la opcion -d es para que nos muestre la salida del estado 

Comprobamos los contenedores creados :

![docker_compose](capturas/Captura de pantalla de 2018-02-11 12-10-26.png)

* Nota , aun no tengo claro el porque docker-compose asigna esos nombres tan raros , tendre que investigar

Accedemos a nuestra aplicacionsita nomas 

![aplicansionsita](capturas/Captura de pantalla de 2018-02-11 12-28-29.png)



# Tarea3

Ahora , como este despliege me sabe a poco , vamos a aumentar el numero de contenedores a tres  en los que serviremos :

- Contenedor1 : Nginx 

- Contenedor2 : php-fpm

- Contenedor3 : nuestra aplicacion 

Manos a la obra :




- nginx.conf ( configuracion de nginx  para el contenedor 1)


Creamos el docker-compose.yml para que lo cree todo automaticamente 

~~~
web:
    image: nginx:latest
    ports:
        - "80:80"
    volumes:
        - ./:/var/www/html/
        - ./nginx.conf:/etc/nginx/nginx.conf
    links:
        - php
php:
    image: superphp
    volumes:
        - ./:/var/www/html
    links:
        - mysql
mysql:
  image: mysql
  environment:
    MYSQL_ROOT_PASSWORD: root
    MYSQL_DATABASE: bookmedik
    MYSQL_USER: bookmedik
    MYSQL_PASSWORD: bookmedik
  volumes:
    - bookmedik:/var/lib/mysql


~~~

Ahora  lanzamos el escenario y accedemos a nuestra aplicacion

![falta_alog](capturas/Captura de pantalla de 2018-02-11 16-46-50.png)


