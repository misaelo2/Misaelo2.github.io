---
layout: default
---

## Esta practica consta de las siguientes tareas :

1. [Instalacion pila LAMP](##Tarea1) 
2. [Instalacion y configuracion de un CMS](##tarea2) 



##Tarea1 
 
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
