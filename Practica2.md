Despliege Tradicional de un CMS PHP : 

## Esta Practica consta de las siguientes tareas :

1. [eleccion opcion](#tarea1)
2. [InstalacionCMS](#tarea2)
3. [instalacion en hosting](#tarea3)
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

## Tarea3

-Ahora , Debemos migrar nuestro servidor dedicado a una pagina de hosting externa :

-Como hosting externo he elegido cdmon (en su version gratuita de pruebas) 

-ahora subimos los archivos del CMS mediante ftp a nustro hosting :

~~~	
	ftp "direccion ip de nuestro dominio"

	nos logueamos
~~~

-Una vez dentro pasamos los archivos al servidor , con el comando "put" ( yo he enviado el archivo comprimido para luego descomprimirlo desde el cpanel de CDMON )

~~~
	ftp> put latest.zip wordpress.zip
~~~
	
al completar el envio , estara en nuestro dominio 

![ftp](capturas/ftp.png)

ahora , me he ido al gestor de archivos de mi hosting y he descomprimido el archivo comprimido de wordpress en la carpeta /web

![descompresion](capturas/descompresion.png)

una vez descomprimidos , nos vamos a la url para instalar wordpress como si de nuestro virtualhosting se tratara en nuestra maquina fisica :

![instalacion_cdmon](capturas/instalacion_cdmon.png)

Los pasos serian los mismos , solo que esta vez , para la base de datos , tendremos que gestionarlo a traves de nuestro hosting :

![BD_cdmon](capturas/BD_cdmon.png)

Ya tenemos nuestro cms php creado .

![listo_cdmon](capturas/listo_cdmon.png)

Si queremos añadir alguna funcionalidad mas a nuestra pagina , debemos subir un modulo o tema a nuestro hosting a traves de ftp y activarlo como lo hicimos la anterior vez en [Practica 1](./Practica1.md).


## Tarea 5 


- Para hacer una copia de seguridad de nuestra aplicacion , la mayoria de hostings te da opcion a gestionarlas, por ejemplo , en cdmon :

![seguridad](capturas/copia_seguridad.png)

 

- Podemos automatizar estos procesos mediante plugins y aplicaciones , por ejemplo , en wordpress hay un plugin llamado BackWPup , que permite realizar copias de seguridad automaticas y enlazarla a una cuenta de dropbox configurada previamente , aqui os dejo un enlace :

[copia](https://youtu.be/FKwRBLpSNhE)












