Consta de las siguientes Ejercicios

1. [Ejecucion de una aplicacion web python en Docker ](#tarea1)

2. [Ejecucion de una aplicacion web python  en Docker (2) ](#tarea2)

3. [Ejecucion de una aplicacion web python en Docker (3)](#tarea3)

4. [Ejecucion de CMS python en docker ] (#tarea4)

5. [Ejecucion de CMS python  en docker  (2) ] (#tarea5)

# Tarea1 

Una vez visto como hemos desplegado aplicaciones php en docker , ahora lo haremos para aplicaciones python .

Para eso disponemos de una aplicacion escrita en python del IES gonzalo nazareno por el magnifico profesor :

[gestiona](https://github.com/josedom24/gestion)


Como es habitual , clonamos el repositorio y creamos un dockerfile con lo necesario para desplegar la imagen :
~~~
FROM debian

WORKDIR /var/www/html/

ADD iaw_gestionGN/ /var/www/html/

ADD gestion.conf /etc/apache2/sites-available/gestion.conf 

RUN apt-get update \
&& apt-get install -y apache2 \
&& apt-get install  -y  python python-pip libapache2-mod-wsgi \
&& apt-get install python-dev

EXPOSE 80

RUN pip install -r requirements.txt \
&& python manage.py migrate \
&& python manage.py loaddata  datos.json \
&& a2ensite gestion.conf 

CMD apachectl -D FOREGROUND
~~~

Retocamos los siguientes ficheros de la aplicacion :
1. gestion/settings.py
~~~
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': '/var/lib/SuperDB',
    }
}
~~~

2. Creamos el fichero que configurara apache para servir nuestra aplicacion
~~~
ServerName www.iaw-gestiona.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/

        WSGIDaemonProcess www.iaw-gestiona.com python-path=/var/www/html/
        WSGIProcessGroup www.iaw-gestiona.com
        WSGIScriptAlias / /var/www/html/gestion/wsgi.py


        <Directory /var/www/html/gestion>
                <Files wsgi.py>
                Require all granted
                </Files>
        </Directory>
~~~ 

Ahora creamos la imagen del contenedor que contendra la base de datos , guardaremos la base de datos en un volumen para no tener que crearla mas veces 
~~~
docker volume create gestion
~~~

* Permisos de la DB
* Alias de estilos
