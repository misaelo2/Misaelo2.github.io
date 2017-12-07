-Esta practica consta de las siguientes tareas :

1. [Intalacion y despliege de una pequeña pagina en surge.sh](#tarea2)
2.  [Despliege de un blog desarrollado con pelican en Github Page](#tarea3)
3. [Integracion continua con aplicacion Django](#tarea4)

# Tarea2

-Para utilizar la aplicacion surge.sh necesitamos instalar los requisitos necesarios :
~~~
1- apt-get install nodejs-legacy
2-  apt install build-essential libssl-dev
3- npm install express
4- npm install --global surge 
~~~
-Ahora a continuacion escribimos el comando "surge" y rellenamos la informacion para crear una cuenta
~~~
root@misael-PC:/home/misael/Plantillas# surge

    Welcome to Surge! (surge.sh)
    Please login or create an account by entering your email and password:

              email: misaelnodaquintero@gmail.com
           password: 
       project path: /home/misael/Plantillas/
               size: 1 files, 25 bytes
             domain: misael.surge.sh
             upload: [====================] 100%, eta: 0.0s
   propagate on CDN: [====================] 100% 
               plan: Free
              users: misaelnodaquintero@gmail.com
         IP Address: 45.55.110.124

    Success! Project is published and running at misael.surge.sh
~~~

-Nos vamos al dominio : 

![creacion del dominio con surge](capturas/Captura de pantalla de 2017-12-07 17-49-16.png)


Ahora implementaremos esta herramienta con Travis y despliege continuo , para ello :

1- Clonamos el repositorio 
2- Activamos para que travis actue en ese repositorio clonado (en el propio dashboard de travis ) 
3- Clonamos nuestro repositorio y abrimos el fichero -travis.yml para especificar nuestro dominio 
~~~
language: python
python:
 - "2.7"
addons:
  apt:
    packages:
      - oracle-java8-set-default  # install Java8 as required by vnu.jar

branches:
  only:
    - master

install:
 - pip install html5validator

script: html5validator --root _build/


deploy:
  provider: surge
  project: ./_build/
  domain: misael.surge.sh
  skip_cleanup: true
~~~

4- Generamos un token para que travis pueda desplegar en surge :
~~~ 
surge token 
~~~
5- con este token , nos vamos a la pagina de travis y creamos dos variables de entorno 
SURGE_LOGIN (correo con el que nos hemos registrado en surge)
SURGE_TOKEN (token anteriormente conseguido)

6- Ahora comprobamos que al desplegar el html sea valido se despliega automaticamente .

-Modificamos el index.html y realizamos un push para que travis monte automaticamente el repositorio , si todo sale bien ,
podremos ver el log de la maquina de travis 

![travis_surge](capturas/Captura de pantalla de 2017-12-07 18-18-54.png)

-Y si accedemos a la pagina web :

![html](capturas/Captura de pantalla de 2017-12-07 18-19-34.png)

-Ahora introduciremos un error en el codigo html para que no sea valido 

al realizar un push del repositorio , travis lo detecta y automaticamente comienza el despliege , como 
el validador de html5 esta mal nos da el siguiente error en el log de travis :

![error](capturas/Captura de pantalla de 2017-12-07 18-25-57.png)

y en nuestro correo nos avisa :

![error_correo](capturas/Captura de pantalla de 2017-12-07 18-26-51.png)



