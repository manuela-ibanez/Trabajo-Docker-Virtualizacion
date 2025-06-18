# Despliegue de Wordpress y Base de Datos MySQL con Docker

## Tabla de contenidos:
- [Descripción del proyecto](#descripción-del-proyecto)
- [Despliegue de la base de datos](#despliegue-de-la-base-de-datos)
- [Despliegue de Wordpress](#despliegue-de-wordpress)
- [Configuración adicional](#configuración-adicional)
- [Personalización de la imagen](#personalización-de-la-imagen)



## Descripción del proyecto:

Este proyecto despliega una aplicación compuesta por multiples contenedores, estos son un sitio Wordpress y una base de datos, en mi caso utilice MySQL, utilizando Docker.
- Autor: Manuela Ibañez
- Sistema opertivo usado: Ubuntu 24.04.2 LTS.
- Entorno: Virtualbox.

## Despliegue de la base de datos:
Utilice MySQL.
Lo primero que realice fue una red docker para facilitar la comunicación entre wordpress y MySQL.
Comando utilizado: (wordpress-net es el nombre de la red).
```bash
sudo docker network create wordpress-net
```
Luego cree un volumen para almacenar los datos del contenedor y asegurar la persistencia de los datos.
Comando utilizado:
```bash
sudo docker volume create mysql-data
```
Para comenzar a descargar MySQL descargue la respectiva imagen.
Comando utilizado:
```bash
sudo docker pull mysql:8.0
```
Comence a correr el contenedor:
```bash
sudo docker run -d --name mysql_container  --network wordpress-net -e MYSQL_ROOT_PASSWORD=manu -e MYSQL_DATABASE=mi_base -e MYSQL_USER=manu -e MYSQL_PASSWORD=manu -v mysql-data:/var/lib/mysql -p 3306:3306 mysql:8.0
```
Use las variables de entorno necesarias para su funcionamiento:  
name:nombre del contenedor.  
network: red para poder conectar luego wordpress y MySQL más rápidamente.  
-e: variables de entrono.  
-v: volumen donde va a almacenar los datos y los cambios realizados.  

## Despliegue de Wordpress:
Cree un volumen para almacenar los datos de wordpress.
Comando utilizado:
```bash
sudo docker volume create wp-content
```
Descargue la imagen Wordpress para luego comenzar a correr el contenedor.
Comando utilizado:
```bash
docker pull wordpress:latest.
```
Este paso no es estrictamente necesario, ya que, cuando se ejecuta el comando sudo docker run, Docker busca la imagen localmente y si no la encuentra la descarga automaticamente antes de correrlo, sin embargo queria incluirlo igualmente en la practica para tenerlo en cuenta.
Comenzar a correr el contenedor:
```bash
docker run -d --name wordpress_container --network wordpress-net  -e WORDPRESS_DB_HOST=mysql-container:3306 -e WORDPRESS_DB_NAME=mi_base -e WORDPRESS_DB_USER=manuela -e WORDPRESS_DB_PASSWORD=manu -v wp-content:/var/www/html/wp-content -p 8080:80 wordpress:latest
```




