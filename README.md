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



