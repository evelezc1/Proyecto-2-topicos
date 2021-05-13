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

- Estefania Velez Cortez <evelezc1@eafit.edu.co>
- Juan Camilo Marin Navia <jcmarinn@eafit.edu.co>

## 2. Roles

- **Juan Camilo Marin Navia:** Seguridad, customizacion del sitio, configuracion de dominio.
- **Estefania Velez Cortez:** Monitoreo de la aplicacion, Despliegue de servicios en nube

## 3. Github del Proyecto

https://github.com/evelezc1/Proyecto-2-topicos

## 4. Requisitos

1. Funcionales y No Funcionales  
   | Requisito | Descripcion | Implementacion |
   |------------|-------------|----------------|
   | | | |
   | | | |
   | | | |

2. Disponibilidad  
   | Requisito | Descripcion | Implementacion |
   |------------|-------------|----------------|
   | Sistema de monitoreo | | |
   | Balanceador de carga | | |
   | Crecimiento Horizontal | | |
   | Disponibilidad en capa de servicios | | |
   | Disponibilidad en capa de persistencia | | |

3. Rendimiento  
   | Requisito | Descripcion | Implementacion |
   |------------|-------------|----------------|
   | Tiempo de respuesta | | |
   | Concurrencia | | |
   | CDN & Caching | | |

4. Seguridad  
   | Requisito | Descripcion | Implementacion |
   |------------|-------------|----------------|
   | Redes Privadas/NAT | | |
   | Autentication de doble factor | | |
   | Autenticacion SSO - OAuth | | |
   | Manejo del protocolo HTTPS | | |

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
