Entorno de Desarrollo y produccion con aplicacion web python con Django

## Esta Practica consta de las siguientes tareas :

1. [Entorno de desarrollo](#tarea1)
2. [Desarrollo de nuestra aplicacion](#tarea2)
3. [Entorno de produccion](#tarea4)
4. [Modificacion en entorno de produccion](#tarea5)
5. [Despliege en un hosting python](#tarea5)


## Tarea1

Nuestro equipo sera el entorno de desarrollo donde desarrollaremos y haremos pruebas con una aplicacion web sobre python ,para ello 
tenemos que configurar nuestro equipo como entorno de desarrollo .

-Primero , Hacemos un fork del repositorio de github que tiene el contenido de la aplicacion web Y lo clonamos a nuestro equipo .
~~~
git clone [repositorio]
~~~

-Luego , cremamos un entorno virtual , yo lo suelo crear todos en una misma carpeta llamada "venv"
~~~	
virtualenv [nombredeentorno]
source bin/activate
~~~

-Una vez en el entorno virtual , nos metemos en el repositorio clonado de la aplicacion e instalamos los requisitos para nuestra aplicacion :
~~~
pip install -r requirements.txt
~~~

Nota : yo he tenido que instalar las cabeceras en mi quipo para que "pip" pudiera compilar python 

~~~
apt-get install python-dev
~~~

-Comprobamos el nombre de la base de datos que vamos a crear , se encuentra en gestion/settings.py
~~~
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'SuperDB'),
    }
}
~~~

-Nuestra base de datos se llamara SuperDB

-Creamos la base de datos :
~~~
python manage.py migrate 
~~~

![crearbd](capturas/migrate_pr4.png)

-Poblamos nuestra Base de datos :
~~~
python manage.py loaddata > datos.jason 
~~~
-Ahora observamos que los datos se han añadido correctamente entrando en la zona de administracion "/admin"

![poblar](capturas/admin_insercion.png)

-Ahora ejecutamos el script manage.py para correr un servidor web de desarrollo :
~~~
python manage.py runserver
~~~

-Introducimos las credenciales "usuario" y "asdasd1234" y conectamos para comprobar que funciona :

![login](capturas/Captura de pantalla de 2017-11-10 08-43-02.png)


## Tarea2

Ahora comenzaremos a desarrollar nuestra aplicacion aplicando cambios y comprobando que se realizan adecuadamente .

-Realizamos cualquier cambio en la aplicacion , como por ejemplo , que se cambie nuestro nombre .

![cambios](capturas/Captura de pantalla de 2017-11-10 09-11-20.png)

-Subimos los cambios al repositorio :
~~~
git commit -am 'modificando cambios'
git push
~~~
-Mostramos el fichero cambiado en el repositorio :

![subirepo](capturas/Captura de pantalla de 2017-11-10 09-17-10.png)
