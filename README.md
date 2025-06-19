# Despliegue de Wordpress y Base de Datos MySQL con Docker

## Tabla de contenidos:
- [Descripción del proyecto](#descripción-del-proyecto)
- [Despliegue de la base de datos](#despliegue-de-la-base-de-datos)
- [Despliegue de Wordpress](#despliegue-de-wordpress)
- [Configuración adicional](#por-qué-no-se-pueden-subir-archivos-grandes-a-wordpress-sin-una-previa-configuración)
- [Personalización de la imagen](#personalización-de-la-imagen)



## Descripción del proyecto:

El objetivo del proyecto es el despliegue una aplicación compuesta por multiples contenedores, tal como un sitio Wordpress y una base de datos
- Autor: Manuela Ibañez 
- SO: 24.04.2 LTS  
![ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
- Entorno:  
![virtualbox](https://img.shields.io/badge/VirtualBox-21416b?style=for-the-badge&logo=VirtualBox&logoColor=white)
- BBDD:    
![mysql](https://img.shields.io/badge/MySQL-005C84?style=for-the-badge&logo=mysql&logoColor=white)
- Utils:  
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![WordPress](https://img.shields.io/badge/WordPress-%23117AC9.svg?style=for-the-badge&logo=WordPress&logoColor=white)

## Despliegue de la base de datos:
> [!NOTE] 
> Lo primero ha realizar es una red docker para facilitar la comunicación entre wordpress y MySQL.  
Comando utilizado: 
```yaml
sudo docker network create wordpress-net
```
> [!CAUTION]
> (wordpress-net es el nombre de la red).

> [!NOTE] 
> Luego se creea un volumen para almacenar los datos del contenedor y asegurar la persistencia de los datos.  
> Comando utilizado:
```yaml
sudo docker volume create mysql-data
```
> [!IMPORTANT]
> Para comenzar a descargar MySQL se descarga la respectiva imagen.  
> Comando utilizado:
```yaml
sudo docker pull mysql:8.0
```
> [!TIP]
> Comenzar a correr el contenedor:
```yaml
sudo docker run -d --name mysql_container  --network wordpress-net -e MYSQL_ROOT_PASSWORD=contraseña -e MYSQL_DATABASE=mi_base -e MYSQL_USER=manu -e MYSQL_PASSWORD=contraseña -v mysql-data:/var/lib/mysql -p 3306:3306 mysql:8.0
```
> [!WARNING]
> Variables de entorno necesarias para su funcionamiento:  
> name: nombre del contenedor.  
> network: red para poder conectar luego wordpress y MySQL más rápidamente.  
> -e: variables de entrono.  
> -v: volumen donde va a almacenar los datos y los cambios realizados.  

## Despliegue de Wordpress:
> [!NOTE] 
> Crear un volumen para almacenar los datos de wordpress.   
>Comando utilizado:
```yaml
sudo docker volume create wp-content
```
> [!IMPORTANT]  
> Descargar la imagen Wordpress para luego comenzar a correr el contenedor.  
> Comando utilizado:
```yaml
docker pull wordpress:latest.
```
>[!tip]
> Este paso no es estrictamente necesario, ya que, cuando se ejecuta el comando sudo docker run,
> Docker busca la imagen localmente y si no la encuentra la descarga automaticamente antes de correrlo, sin embargo queria incluirlo
> igualmente en la practica para tenerlo en cuenta.

>[!NOTE]
>Comenzar a correr el contenedor:
```yaml
docker run -d --name wordpress_container --network wordpress-net  -e WORDPRESS_DB_HOST=mysql-container:3306 -e WORDPRESS_DB_NAME=mi_base -e WORDPRESS_DB_USER=manuela -e WORDPRESS_DB_PASSWORD=contraseña -v wp-content:/var/www/html/wp-content -p 8080:80 wordpress:latest
```
> [!NOTE]
>  Imagenes creadas hasta el momento:      

![Image](https://github.com/user-attachments/assets/a7f58ff2-4cae-4c23-9289-c217f0a05a52)
> Aquí aparece Wordpress y MySQL

> [!NOTE] 
> Comenzar a correr el contenedor Wordpress.  
> Con el siguiente comando:
```yaml
docker run -d --name wordpress_container --network wordpress-net  -e WORDPRESS_DB_HOST=mysql-container:3306 -e WORDPRESS_DB_NAME=mi_base -e WORDPRESS_DB_USER=manu -e WORDPRESS_DB_PASSWORD=contraseña -v wp-content:/var/www/html/wp-content -p 8080:80 wordpress:latest
```
> En el comando se pueden ver parametros muy similares a cuando se comienzó a correr el contenedor MySQL.  

## Posible error
> [!WARNING]   
> Al ir a buscar la pagina web donde esta corriendo Wordpress se dió el error de "Error establishing a database connection".  
> Para solucionar el problema, se borraron ambos contenedores creados anteriormente y ambos volumenes.

> [!TIP]
> Comando utilizado para borrar los contenedores:  
```yaml
Docker stop wordpress_container
Docker rm wordpress_container
Docker stop mysql_container
Docker rm mysql_container
```

> [!TIP]
> Volver a correrlos:

![Image](https://github.com/user-attachments/assets/0f95d22f-18f7-4e6b-858d-21c3487d70e7)  
![Image](https://github.com/user-attachments/assets/23dbd0e9-09b9-40b5-bdd7-2a0510453be2)  
> [!NOTE]  
> Luego de esta corrección de errores en ambos codigos, la pagina web funciona correctamente:

![Image](https://github.com/user-attachments/assets/8eff947b-e35d-429c-af78-4e84b4ee47ed)
> En la imagen se muestra como se ve la pagina luego de solucionar los errores:

### Comprobar persistencia
> [!NOTE]  
> Se realiza un cambio en la página de inicio con las opciones a la derecha, en entradas.  
> Cambio realizado: se cambió ¡Hola mundo! por ¡Trabajo docker manu!.  
> Se reinician los contenedores para comprobar que ese cambio se guarde correctamente:  

![Image](https://github.com/user-attachments/assets/0b7d3dd3-450c-41cd-b099-f020c8893663)  
> [!NOTE]  
> Al volver a entrar en la pagina se ve la persistencia de los datos modificados, lo que indica la correcta configuración de los volumenes.  

![Image](https://github.com/user-attachments/assets/cb5328a7-0d97-4074-987e-4a545d9ca405)  
> En la imagen se puede ver el cambio realizado  

## Por qué no se pueden subir archivos grandes a Wordpress sin una previa configuración
> [!CAUTION]
> Debido a que la configuracion predeterminada de PHP y Apache, en la imagen de Wordpress, tiene límites bajos, lo recomendable es crear un Dockerfile personalizado que modifique estos parametros.  
> Para comenzar se debe crear un directorio:  
```yaml
mkdir dockerfile
```
> [!IMPORTANT]
> En este directorio vamos a añadir nuestro Dockerfile personalizado, que es un archivo de instrucciones para construir una imagen de Docker.  
> Dockerfile personalizado:  
```yaml
nano Dockerfile
```
> [!IMPORTANT]
> Y dentro del Dockerfile:  
```yaml
FROM wordpress:latest

COPY uploads.ini /usr/local/etc/php/conf.d/uploads.ini
```
> [!TIP]
> COPY toma el archivo uploads.ini (que define los nuevos límites para que WordPress permita subir archivos grandes), que se añade al directorio y busca configuraciones extra.  

> [!NOTE] 
> Uploads.ini:
   
![Image](https://github.com/user-attachments/assets/7f074bc1-6bcf-401d-9c44-966464c32ab3)

### Parametros dentro del Uploads.ini
> [!IMPORTANT]
> Explicación de los parametros dentro del Uploads.ini, que es el archivo que define los nuevos limites para el contenedor:  
> file_uploads = On -> Habilita la subida de archivos por formularios HTML (si está en Off, no podés subir nada).   
>memory_limit = 512M -> Cantidad máxima de memoria RAM que puede usar un script PHP (como los de WordPress).  
> upload_max_filesize = 512M -> Tamaño máximo de archivo individual que podés subir (por ejemplo, una imagen, PDF o video).  
> post_max_size = 512M -> Tamaño máximo total de los datos que puede recibir un formulario POST (por ejemplo, cuando subís varios archivos juntos).  
> max_execution_time = 300 -> Tiempo máximo (en segundos) que se permite a un script PHP para ejecutarse. Si tarda más, se corta.  

> [!NOTE]  
> Ahora se le indica a Docker que construya una imagen usando el archivo Dockerfile realizado anteriormente,  
> wordpress-custom es el nombre de la nueva imagen creada.  
> Comando utilizado:  
```yaml
docker build -t wordpress-custom .
```
![Image](https://github.com/user-attachments/assets/2a679176-3833-493d-a24f-59c6bbeb2373)  

> [!WARNING]
> Se debe detener y eliminar el contenedor antiguo de Wordpress, para levantar uno nuevo con las imagen creada anteriormente, que contiene las nuevas configuraciones.  
> Comandos usados:  
```yaml
docker stop wordpress_container
docker rm wordpress_container
```
> [!WARNING]
> Se debe levantar un contenedor con la imagen creada anteriormente,
> cuyo nombre es wordpress-custom.    
> Comandos usados:

![Image](https://github.com/user-attachments/assets/4b75ec73-dada-4f30-a4b8-d3738b4cc80e)  
> Se usa la imagen personalizada anteriormente en vez de wordpress:latest, como se ve en la imagen.

### Posible error al configurar: 
> [!CAUTION]
> Al subir un archivo en el que el tamaño máximo es de 2MB,  
> por lo tanto algo falló en la configuraciones del wordpress.  
> Lo primero ha realizar fue verificar que la imagen se haya creado correctamente y que el contenedor haya levantado con wordpress-custom.  
![Image](https://github.com/user-attachments/assets/42657d70-25b1-486d-991f-b0cbf1f89406)

> [!CAUTION]
> Luego se reinició el contenedor.  
> El tamaño sigue siendo el mismo,
> El error se basaba en que el archivo con la configuración upload.ini que no fue copiado correctamente,  
> no se encuentra en el contenedor.

![Image](https://github.com/user-attachments/assets/c395f1cf-52e4-4a8f-ad27-9c710ef4a243)  
> [!CAUTION]
> El contenedor no estaba leyendo el archivo realizado anteriormente para tomar las configuraciones:

![Image](https://github.com/user-attachments/assets/86d26251-7e6e-4885-b2aa-519f15852ae7)  

### Solucion:
> [!TIP]
> Se optó por cambiar el nombre del archivo a php con las mismas configuraciones, y en el Dockerfile personalizado.
> Se realizaron los siguientes cambios:
```yaml
FROM wordpress:latest

COPY php.ini /usr/local/etc/php/php.ini
```
> [!IMPORTANT] 
> Antes era COPY uploads.ini /usr/local/etc/php/upsloads  
> [!NOTE] 
> Reinicio de contenedor con las nuevas configuraciones.

![Image](https://github.com/user-attachments/assets/64482a11-78b2-47fe-8566-571395077e12)  
> Funcionó correctamente.

### Explicación del problema: 
> [!WARNING]  
> Cuando se estaba usando el archivo uploads.ini dentro de /usr/local/etc/php/conf.d/,  
> PHP no estaba leyendo ese archivo de configuración adicional.  
> Por eso seguía usando los valores por defecto (como upload_max_filesize = 2M).  
> Al copiar el archivo directamente como php.ini en /usr/local/etc/php/php.ini,  
> que es el archivo principal de configuración de PHP, me aseguré que las configuraciones sean leídas sí o sí.

## Personalización de la imagen:
> [!NOTE] 
> Se Crea un Dockerfile que extiende la imagen oficial de WordPress para incluir el plugin Wordfence.
> Sirve para añadir seguridad contra malware, ataques y firewall. Aumenta la seguridad de Wordpress.
> Se crea una carpeta para el proyecto:  
```yaml
mkdir wp-custom
```
> [!NOTE]  
> Dentro de esa carpeta:
> Se Intento de dos formas diferentes y ninguna dejaba descomprimir luego el plugin,  
> comandos intentados:
```yaml
wget https://downloads.wordpress.org/plugin/wordfence.latest-stable.zip
curl -L -o wordfence.zip https://downloads.wordpress.org/plugin/wordfence.latest-stable.zip
```
> [!TIP]
> Se optó por descargarlo manualmente desde el navegador.
> Luego de descargar el archivo, quedó en la carpeta de descargas, descomprimido en otra carpeta wp-custom1.
> Comando utilizado:

![Image](https://github.com/user-attachments/assets/10567afd-794b-434f-85e0-bca46a4f5c22)  
> [!NOTE]  
> Creación del Dockerfile personalizado dentro de la carpeta:  
```yaml
FROM wordpress:latest

COPY wordfence /usr/src/wordpress/wp-content/plugins/wordfence
```
> [!NOTE]  
> Esto sitúa el plugin en el directorio correcto para que WordPress lo instale automáticamente al arrancar el contenedor.
>   
> Se Creo nueva imagen para levantar el contenedor Wordpress:
```yaml
docker build -t wordpress-wordfence
```
> [!IMPORTANT]  
> Parar y eliminar el contenedor Wordpress creado anteriormente para poder correrlo con la nueva configuración, con el plugin:

![Image](https://github.com/user-attachments/assets/ede7b277-fe73-403e-9797-9dca58e1dc53)  

> [!TIP]
> En la siguiente imagen se ve que el plugin Wordfence Security puede activarse, ya aparece dentro de los plugins:

![Image](https://github.com/user-attachments/assets/bb9ba8a2-f6fd-41c8-b6d1-38bd61cdd7fd)  
> [!TIP]
> Y en la siguiente imagen se ve el plugin ya activado:

![Image](https://github.com/user-attachments/assets/68755476-f2d7-40ad-9558-fde7b52386a3)  


### Contacto:  
![gmail](https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white)  
mibaniez@alumnos.exa.unicen.edu.ar






























