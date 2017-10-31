Despliege Tradicional de un CMS PHP : 

## Esta Practica consta de las siguientes tareas :

1. [eleccion opcion](#tarea1)
2. [InstalacionCMS](#tarea2)
3. [Migracion](#tarea4)
4. [copias de seguridad](#tarea5)
## Tarea1

He elegido la opcion de instalar un servidor dedicado y luego migrarlo a un hosting externo de internet .


## Tarea2 

Como opcion a instalar he decidido instalar Wordpress en virtual hosting 

-primero configuramos nuestro virtual hosting y nuestro /etc/hosts para resolver estaticamente la direccion de nuestro hosting y instalar 

![instalacion](capturas/instalacion.png) 

-Ahora introducimos los datos de nuestra base de datos para que la aplicacion se conecte :

![BD](capturas/BD2.png)

-Si la informacion es correcta , solo nos queda seguir los pasos de instalacion y seguir rellenando datos 

![informacion](capturas/informacion.png)

-accedemos a wordpress y introducimos nuestro usuario y contraseña :

![listo](capturas/listo.png)

ya tenemos y podemos perfeccionar nuestro sitio web .

## Tarea4

-Ahora , Debemos migrar nuestro servidor dedicado a una pagina de hosting externa :

-Como hosting externo he elegido cdmon (en su version gratuita de pruebas) 

-ahora subimos los archivos del CMS mediante ftp a nustro hosting :

~~~	
	ftp "direccion ip de nuestro dominio"

	nos logueamos
~~~

-nos situamos en la carpeta de instalacion y Comprimimos los documentos de nuestro CMS instalado :

~~~
 zip -r wordpress.zip wordpress/
~~~ 

-Una vez dentro pasamos los archivos al servidor , con el comando "put" ( yo he enviado el archivo comprimido para luego descomprimirlo desde el cpanel de CDMON )

~~~
	ftp> put wordpress.zip migracion.zip
~~~
	
al completar el envio , estara en nuestro dominio 

![ftp](capturas/ftp.png)

tambien creamos una copia de la base de datos de nuestro servidor local y la enviamos a el hosting 
~~~
mysqldump --user=debian --password="contraseña" "Base de datos"  > migracion.sql
~~~

Ejecutamos el script en el hosting :

![migracion_bd](capturas/migrar_bd.png)

IMPORTANTE : al descomprimir , tenemos que entrar en nuestra carpeta de wordpress , y editar el wp-config.php para que apunte a la base de datos y el usuario mysql que tendremos en nuestro hosting . un ejemplo : 

![config_bd](capturas/config_bd.png)

ahora , me he ido al gestor de archivos de mi hosting y he descomprimido el archivo comprimido de wordpress en la carpeta /web

![descompresion](capturas/descompresion.png)

una vez descomprimidos , nos vamos a la url  como si de nuestro virtualhosting se tratara en nuestra maquina fisica :

![wordpress_funcionando](capturas/wordpress_funcionando.png)

Si queremos añadir alguna funcionalidad mas a nuestra pagina , debemos subir un modulo o tema a nuestro hosting a traves de ftp y activarlo como lo hicimos la anterior vez en [Practica 1](./Practica1.md).


## Tarea5 

Para hacer copias de seguridad de mi aplicacion , lo que haria seria establecer la aplicacion que tengo en local como plataforma de desarrollo , y una vez que quiero implantarla en produccion , seria migrarla otra vez como hemos hecho en los primeros pasos ,esto daria lugar a tener que comprimir otra vez los archivos en la carpeta de instalacion y hacer una copia de la base de datos cada vez que queramos implantar una nueva version . 

Para ahorrarnos trabajo , lo podriamos programar mediante el archivo de tareas programadas llamada "crontab" 

-Abrimos el fichero /etc/crontab y escribimos lo siguiente :

~~~
00	12	*	*	7	zip /home/debian/apli_web/copia_web.zip    /var/www/wordpress
00	12	*	*	7	sqldump --user=debian --password=debian wordpress /home/debian/apli_web/copia_web.sql
~~~

con este comando , establecemos que queremos que se comprima el archivo /var/www/wordpress en la carpeta /home/debian/apli_web/copia_web.zip 
los domingos de todas las semanas de todos los meses a las 12:00 horas 

Nota : para usar este metodo tienes que tener en cuenta que el usuario y la base de datos tienen que estar bien configurados en el wp-settings.php
o puedes crear en tu hosting las mismas caracteristicas que la base de datos de tu local.


Aun asi , sigue resultando algo tedioso tener que ir subiendo los ficheros por ftp y descomprimirlos asi que podemos hacer uso de plugins de wordpress como "Backup WordPress" o "xcloner" . 









