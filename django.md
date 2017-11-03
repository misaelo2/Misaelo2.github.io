---
layout: default
---

## Esta practica consta de las siguientes tareas :

1. [Iniciacion Django](#tarea1) 
2. [Entorno de Produccion](#tarea2) 


## Tarea1

clonamos el repositorio 

~~~
git clone https://github.com/josedom24/django_tutorial.git
~~~

-Creamos un entorno virtual : 
~~~
virtualenv   (carpeta) 

source   bin/activate
~~~

-Abrimos el fichero /settings.py y vemos a ver que base de datos utilizara :
~~~
vim /settings.py 
~~~

abrimos el fichero settings.py y vemos la base de datos que utilizara 

![bd](capturas/django_bd.png)
	

ejecutamos el script manage.py que nos permite realizar varias tareas 

	python manage.py migrate 

![bd_creada](capturas/django_crea_bd.png)

el script crea la base de datos segun el modelo polls/model.py

ahora creamos un superuser para administrar el sitio 

	python manage.py createsuperuser

![bd_creada](capturas/django_su.png)

ejecutamos la opcion 'runserver'
~~~
		python manage.py runserver
~~~
nos metemos en la pagina :
~~~
		http://127.0.0.1:8000/admin/ 
~~~
introducimos los datos del superusuario y nos lleva a la siguiente pagina 

![admin](capturas/django_admin.png)(https://misaelo2.github.io./Practica2)



añadimos encuestas 

![encuesta](capturas/django_encuesta.png)

listo , nuestras encuestas ya apareceran en la raiz 

![encuesta2](capturas/django_vota.png)


## Tarea2


- Instalamos apache2,git,python  y el modulo wsgi para ejecutar python : 

~~~
apt-get install apache2 

apt-get install libapache2-mod-wsgi

apt-get install git

apt-get install python-django
~~~

- nos movemos a nuestro document root por defecto en apache y clonamos el repositorio 

~~~
cd /var/www/html

git clone  https://github.com/josedom24/django_tutorial
~~~

- Configuramos  nuestro servidor apache para que sirva la aplicacion :

![conf_apache](capturas/django_conf.png)


- Creamos base de datos 

![python_crea_db](capturas/django_bd_produccion.png)

- Creamos un super user 

![creando_admin](capturas/django_superuser_produccion.png)

- Desactivamos el debug a false : 

~~~
vim settings.py 
~~~

![debug_asi](capturas/django_debug.png)

nota : cambiar los permisos a www-data:www-data  con el comando 
~~~
chown -r www-data:www-data /django_tutorial
~~~

-Accedemos a la pagina web /admin

![amdinistrando_django](capturas/django_admin_apache.png)

-Nos logueamos y estamos en el menu administracion para crear encuestas :

![crea_encuestas_django](capturas/django_crea_encuestas.png)


-Se ve sin hoja de estilos porque por defecto django deja al servidor web que ejecuta la aplicacion servir los estilos . 
