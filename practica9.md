Despliege de aplicaciones en Dockerfiles 

1. [Ejecucion de una aplicacion web php en Docker ](#tarea1)

2. [Ejecucion de una aplicacion web php en Docker (2) ](#tarea2)

3. [Ejecucion de una aplicacion web php en Docker (3)](#tarea3)

4. [Ejecucion de CMS en docker ] (#tarea4)

5. [Ejecucion de CMS en docker (2) ] (#tarea5)



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


