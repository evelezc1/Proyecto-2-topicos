# Analisis y Diseño

# PROYECTO 2 - TOPICOS DE TELEMATICA

## Table of Contents

1. [Integrantes](#1-integrantes)
2. [Roles](#2-roles)
3. [Github del Proyecto](#3-github-del-proyecto)
4. [Requisitos](#4-requisitos)
5. [Diseño Escalable](#5-dise-o-escalable)
6. [Otra Informacion](#6-otra-informacion)

---

## 1. Integrantes

- Estefania Velez Cortes <evelezc1@eafit.edu.co>
- Juan Camilo Marin Navia <jcmarinn@eafit.edu.co>

## 2. Roles

- **Juan Camilo Marin Navia:** Seguridad, customizacion del sitio, configuracion de dominio.
- **Estefania Velez Cortez:** Monitoreo de la aplicacion, Despliegue de servicios en nube

## 3. Github del Proyecto

https://github.com/evelezc1/Proyecto-2-topicos

## 4. Requisitos
   | Requisito | 
   |------------|
   |Disponibilidad |
   |Rendimiento | 
   |Seguridad | 

1.2. Disponibilidad  
   | Requisito | Descripcion | Implementacion |
   |------------|-------------|----------------|
   | Sistema de monitoreo |Este se encarga de hacer un seguimiento del estado, tanto de la infraestructura como de los subsistemas presentes. Su objetivo es asegurar que el sistema es fiable y estable, capaz de proporcionar los servicios para los que ha sido diseñado. | Se utilizó Uptimerobot para la implementación del sistema de monitoreo|
   | Balanceador de carga |Este se encarga de direccionar a un cliente al servidor web que se encuentre con mayor disponibilidad entre los que cuentan con el mismo contenido. |Se implemento en AWS ELB|
   | Crecimiento Horizontal | Potenciamos el rendimiento del sistema desde un aspecto de mejora global, teniendo dos zonas de Disponibilidad A - B | Se configuro el AutoScaling group correspondiente que contiene mínimo 2 servidores, máximo 3, y una capacidad de CPU del 60% |
   | Disponibilidad en capa de servicios |Este se realiza para la conversión de las direcciones de red (NAT) para permitir a las instancias de la subred privada conectarse a Internet o a otros servicios | Se creó una imagen NAT de AMI de la comunidad para proveer este servicio |


1.3. Rendimiento  
   | Requisito | Descripcion | Implementacion |
   |------------|-------------|----------------|
   | Tiempo de respuesta | El tiempo de respuesta del servidor (response time en inglés), es el tiempo en segundos o fracciones de segundo necesario, para hacer la petición y recibir el contenido HTML de una página determinada | En este caso con la ayuda de pagespeed de google, identificamos muy buen tiempo de respuesta.  |
   | CDN & Caching | Este se usa para minimizar los retrasos en la carga de contenidos de páginas web al reducir la distancia física entre el servidor y el usuario. |En este caso de delegó el manejo de servicios del DNS desde el Freenom hasta el CloudFlare|

1.4. Seguridad  
   | Requisito | Descripcion | Implementacion |
   |------------|-------------|----------------|
   | Redes Privadas/NAT |Las redes locales tienen en este caso dos direcciones IP privadas que pertenecen a dispositivos específicos de la red. A través de un sistema NAT, estas direcciones privadas se traducen en una dirección IP pública cuando las peticiones salientes de los dispositivos de red se envían a Internet. | En este caso se definieron dos conjuntos de direcciones para redes privadas una para la zona de disponiblidad de A (172.31.1.0/24) y otra para B (172.31.3.0/24) y las NATS respectivas|
   | Autentication de doble factor |Este es un método de control de acceso en el que a un usuario se le concede acceso al sistema o a una parte del sistema solo después de que presente dos o más pruebas diferentes de que es quien dice ser. |Para esto usamos el pluggin de wordpress llamado "miniOrange 2 Factor Authentication" | 
   | Autenticacion SSO - OAuth | Este es un protocolo de autorización que a diferencia de los de autenticación, este permite la controlar los servicios a los que se puede acceder por parte del usuario autenticado. | Para esto usamos el pluggin de wordpress llamado "OAuth Single Sign On - SSO (OAuth Client)" |
   | Manejo del protocolo HTTPS |Protocolo que permite establecer una conexión segura entre el servidor y el cliente, que no puede ser interceptada por personas no autorizadas. | En este requisito usamos un listener de ELB con certificados de Let's Encrypt|

## 5. Diseño Escalable

1. Patrones de arquitectura

   - Mejores Practicas
     - Minimizar la transferencia de datos por cable
     - Carga de datos bajo demanda
   - Seleccion de tacticas
     - Carga de Datos asincronica
     - Cache & CDN
     - Compresion de archivos
     - Sesion sin estado
   - Decisiones de diseño
     - Arquitectura orientada a servicios
     - Computación distribuida

2. Herramientas Usadas

   - Servidor DNS - Cloudflare
   - CDN - Coudflare
   - Balanceador de Cargas - AWS ELB
   - Base de Datos - AWS RDS
   - Instacias de servidor Web & Bastion - AWS EC2
   - Sistema de archivos - AWS EFS
   - Despliegue automatico para tolerancia a fallos - AWS AutoScaling
   - Manejo de redes y subredes - AWS VPC & Security Groups

## 6. Otra Informacion

Para esta práctica nos basamos en las instrucciones del documento inicial donde se definen funcionalidades y servicios relacionados con la implementación de una aplicación en donde se diseña e implementa una comunidad temática alrededor de las materias y contenidos de las diferentes asignaturas de ingeniería de sistemas en EAFIT (páginas, documentos, foros, chats, eventos, calendario, etc). La cual fue instalada y ejecutada en el ambiente en nube de amazon Amazon Web Services (AWS).

- ![image](https://user-images.githubusercontent.com/30262251/118755791-25146780-b82f-11eb-9c1f-5dd27ec7dcc1.png)
- En esta imagen podemos evidenciar que el tiempo de interactivo (el cual es una métrica que calcula el tiempo que tarda un sitio web en ser visible e interactivo para el visitante.) se encuentra en un rango muy bueno, ademas de otras metricas que demuestran las buenas practicas empleadas. 


