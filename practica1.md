---
layout: default
---

## Esta practica consta de las siguientes tareas :

1. [Instalacion pila LAMP](##tarea1) 
2. [Instalacion y configuracion de un CMS](##tarea2) 



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
 
 
-instalamos el paquete de drupal : 

-descomprimimos el paquete : 
~~~
	tar -xcfz 
~~~
 -lo movemos a nuestro sitios de apache ( /var/www/ )
~~~
	mv drupal-7.0  /var/www/

~~~

 -creamos usuario para la base de datos :
~~~	
	createuser --pwprompt --encrypted --no-adduser --no-createdb drupal 
~~~
 -creamos la base de datos 
~~~
	createdb --encoding=UNICODE --owner=username CMSservidor
~~~
 -Si no tenemos en /var/www/drupal un archivo llamado settings.php o default-settings.php pasamos al paso 4 

-Ponemos en virtual hosting el servidor y instalamos el install.php 

-si nos aparecen varios errores , echamos mano de la documentacion y los solucionamos , en mi caso :
	-modificar permisos de archivos para escribir :  chmod o+w sites/default/files  
	
	-copiar ~~~ cp ./sites/default/default.settings.php    
		./sites/default/settings.php ~~~ 
	esto es si como en el paso     anterior , no tenemos el settings.php 




	-tambien he tenido que instalar librerias de php : 	apt-get install  php7.0-xml
  								  apt-get install  php7.0-gd


 -Ahora le  indicamos nuestra base de datos previamente creada y el usuario y contraseña para credenciarse  


Si todo va bien , ahora procederemos a configurar nuestro site 


y listo , esta todo instalado y podemos configurar nuestro sitio .
!(./capturas/ya_instalado.png)

para cambiar de tema 	:
 descarga un tema y descomprimelo  en ~~~ /var/wwe/sites/all/themes ~~~ y activalo luego en el apartado apariencia de la pagina .
!(./capturas/he_cambiado_tema.png)

para instalar alguna funcionalidad con modulos se hace lo mismo , pero en la carpeta modulos .
!(./capturas/activar_modulos.png)

Por ejemplo , yo me descargue un modulo para tener un foro : 
 !(./capturas/modulo_foro.png)


