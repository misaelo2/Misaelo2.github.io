---
layout: default
---

## Esta practica consta de las siguientes tareas :

1. [Instalacion pila LAMP](#tarea1) 
2. [Instalacion y configuracion de un CMS](#tarea2) 
3. [Configuracion Multinodo](#tarea3)


## Tarea1 
 
La pila LAMP es el acronimo de L (linux ) , A (apache2) , M ( MySQL) , P (php) y se refiere a la capacidad de poer gestionar nuestros sitios virtuales como queramos al tener instalados estos componentes , a continuacion , os enseño como :

Primero , partiendo del hecho de que tienes una distro linux , instalemos apache : 
~~~
  apt-get install apache2
~~~

Segundo , es el turno de una base de datos , en mi caso he elegido MySQL :
~~~
  apt-get install mysql-server mysql-client 
~~~


y por ultimo , solo nos queda el interprete php , que despendiendo de una distribucion o otra , elegiremos la version , en este caso es la 7.0 
~~~
  apt-get install php7.0 
~~~

Listo! ahora ya podremos configurar nuestro sitio web e incluso tener varios usando el sistema de Virtual Hosting


## Tarea2 

Esto ya es mas complicado , se trata de , mediante un gestor de contenido web , en mi caso "drupal 7 " descargar y configurar tu propio sitio web :
 
 
- instalamos el paquete de drupal : 

- descomprimimos el paquete : 
~~~
	tar -xcfz 
~~~
 - lo movemos a nuestro sitios de apache ( /var/www/ )
~~~
	mv drupal-7.0  /var/www/
~~~

 - creamos usuario para la base de datos :
~~~	
	createuser --pwprompt --encrypted --no-adduser --no-createdb drupal 
~~~
 - creamos la base de datos 
~~~
	createdb --encoding=UNICODE --owner=username CMSservidor
~~~
 - Si no tenemos en /var/www/drupal un archivo llamado settings.php o default-settings.php pasamos al paso 4 

- Ponemos en virtual hosting el servidor y instalamos el install.php 

 - si nos aparecen varios errores , echamos mano de la documentacion y los solucionamos , en mi caso :
	- modificar permisos de archivos para escribir :  chmod o+w sites/default/files  
	
	- copiar ~~~ cp ./sites/default/default.settings.php    
		./sites/default/settings.php ~~~ 
	esto es si como en el paso     anterior , no tenemos el settings.php 




	- tambien he tenido que instalar librerias de php : 	apt-get install  php7.0-xml
  								  apt-get install  php7.0-gd


 - Ahora le  indicamos nuestra base de datos previamente creada y el usuario y contraseña para credenciarse  


Si todo va bien , ahora procederemos a configurar nuestro site 


y listo , esta todo instalado y podemos configurar nuestro sitio .
![instalado](capturas/ya_instalado.png)

para cambiar de tema 	:
 descarga un tema y descomprimelo  en  /var/wwe/sites/all/themes  y activalo luego en el apartado apariencia de la pagina .
![tema](capturas/he_cambiado_tema.png)

para instalar alguna funcionalidad con modulos se hace lo mismo , pero en la carpeta modulos .
![modulo](capturas/activar_modulos.png)

Por ejemplo , yo me descargue un modulo para tener un foro : 
 ![foro](capturas/modulo_foro.png)


## Tarea3
Ahora , haremos que nuestro gestor de contenido utilize una base de datos dispuesta en otra maquina .

Estas son las tablas a copiar  : 

![Base_Datos](capturas/BD.png)


-Primero creamos una copia de seguridad de la base de datos.

mysqldump --user=TU_USUARIO --password=TU_CONTRASEÑA NOMBRE_BASE_DE_DATOS > copia_seguridad.sql
	
	
	si no te deja , concedele los permiso al usuario.

-creamos el escenario vagrant , conectado a la maquina que teniamos nuestro servicio CMS anterior ,y le instalamos una base de datos . 

-creamos una base de datos 
	
	create database copia_drupal ;
	
-nos pasamos el fichero a la otra maquina (podemos usar el plugin scp , hacerlo por scp cojiendo la clave de vagrant , o mas facil , introduciendo el fichero sql que hemos creado , en el directorio donde esta el vagrantfile) .

	cp /home/misael/Documentos/copia_seguridad.sql 	~/vagrant/Practicaiaw1 



-utilizamos  el script  de la base de datos

	mysql --user=root --password=root  copia_drupal  < copia_seguridad.sql

-creamos un usuario en la base de datos con privilegio a esa base de datos : 
		
	GRANT ALL PRIVILEGES ON copia_drupal.* TO 'misael'@'%'
	IDENTIFIED BY 'misael' WITH GRANT OPTION;

-Ahora nos conectamos con el usuario y observamos las tablas :

![Base_Datos](capturas/copiaBD.png)
magia ! las mismas tablas que antes .


-Desactivamos el servidor de base de datos en el servidor anterior 
	systemctl stop mysql 


-Ahora indicamos a nuestro CMS que coja la Base de datos en la otra maquina virtual : 

Nota : a estas alturas , no deberiamos poder acceder a nuestra pagina :
![Base_Datos](capturas/error_BD.png)


-editamos el settings.php 

	ponemos las caracteristicas de nuestro servidor remoto 

~~~

  @code
  array(
    'driver' => 'mysql',
    'database' => 'databasename',
    'username' => 'username',
    'password' => 'password',
    'host' => 'localhost',
    'port' => 3306,
    'prefix' => 'myprefix_',
    'collation' => 'utf8_general_ci',
  );
  @endcode

~~~


-Ahora vamos al servidor remoto y permitimos las conexiones  
~~~
	bind-address            = 0.0.0.0
~~~
Listo , refrescamos nuestro drupal : 

![exito](capturas/exito.png)
