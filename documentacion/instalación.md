# Instalacion

# PROYECTO 2 - TOPICOS DE TELEMATICA

## Table of Contents

1. [DNS](#1-dns)
2. [SSL](#2-ssl)
3. [AWS - Version Monolitica](#3-aws---version-monolitica)
4. [AWS - Version Escalable](#4-aws---version-escalable)

---

## 1. DNS

- Se escogio el nombre de dominio en freenom.com, en este caso, escogimos widelab.tk
- Procedemos a delegar el dominio a Cloudflare, para esto creamos una cuenta en cloudflare y entramos nuestro dominio, Cloudflare creara unos registros NS.
- Copiamos y pegamos los nuevos registros NS en freenom para que el nuevo encargado de gestionar el dominio sea Cloudflare.

Luego de un tiempo de espera, se validará el dominio en cloudflare y podremos crear todos nuestros registros y reglas de direccionamiento desde allí.

![image](https://user-images.githubusercontent.com/30262251/118589738-99370880-b766-11eb-88c3-faf34d7561a0.png)


## 2. SSL

En una maquina virtual, instalar cerbot y algunas de sus dependencias

```bash
sudo amazon-linux-extras install epel -y
sudo yum install certbot-nginx -y
sudo yum install nginx -y
```

Ejecute certbot para pedir certificado SSL para registros especificos, en este caso, obtendremos solamente el wildcard

```bash
sudo certbot --server https://acme-v02.api.letsencrypt.org/directory -d *.widelab.tk --manual --preferred-challenges dns-01 certonly
```

Se pedirá que llene una informacion para el certificado como ubicación y correo, despues de completar todos los pasos, saldra un registro TXT que se debera añadir al dominio solicitado.

Luego de haber creado el registro TXT correctamente, procederemos a usar el certificado creado en el directorio `/etc/letsencrypt/live/$DOMAIN/` en la version monolitica o en la version escalable del wordpress.

Para la version escalable, cuando tengamos creado nuestro LoadBalancer:

1. Click en Agregar un listener
2. Protocolo: HTTPS
3. Default Action: Forward to... target group existente
4. Default SSL Certificate: Import
5. Import to: IAM
6. Certificate name key: Cualquiera
7. Certificate private key: archivo ubicado en `/etc/letsencrypt/live/$DOMAIN/privkey.pem`
8. Certificate body: archivo ubicado en `/etc/letsencrypt/live/$DOMAIN/cert.pem`
9. Certificate chain: archivo ubicado en `/etc/letsencrypt/live/$DOMAIN/fullchain.pem`

## 3. AWS - Version Monolitica


- Creación de ec2-

Para la creación del ec2 utilizamos la Amazon Linux 2 AMI (HVM) SSD Volume Type, dejando por defecto la  configuración de aws pero agregando los puertos:

 ![image](https://user-images.githubusercontent.com/30262251/118589531-27f75580-b766-11eb-9cde-fc255e67e800.png)
 
Eventualmente le asignamos la siguiente IP elástica a la instancia:

![image](https://user-images.githubusercontent.com/30262251/118589558-39d8f880-b766-11eb-9706-82b7e11a863f.png)

Con esto hecho correctamente procedemos a nuestra maquina ec2 ingresando las claves que son suministradas por aws.

- Instalación de Docker y Docker compose

Para esta instalación seguimos la guía de comandos brindada por el profesor.

![image](https://user-images.githubusercontent.com/30262251/118589639-612fc580-b766-11eb-88c5-19ac0c829771.png)

Posteriormente descargamos las imágenes de wordpress de docker-hub, el docker-compose.yml (cambiando el puerto por defecto al 80). Y continuamos con los comandos:

```bash
$ docker-compose down (detener el servidor de wordpress)
```

```bash
$ docker-compose up -d (iniciar el servidor de wordpress)
```
Con esto logramos tener un servidor instalado de wordpress en docker. 




## 4. AWS - Version Escalable

Para la version escalable montamos las siguientes configuraciones: 

#### Creación y configuración la VPC: 

●	En la consola de AWS seleccione el servicio de VPC.

●	En el panel izquierdo seleccione la opción de “Your VPCs”

●	Click en Create VPC.

o	Name: MyWebAPP-VPC

o	IPv4 CIDR block: 172.31.0.0/16

o	Click “Create VPC”

o	Debe aparecer mensaje de que la VPC se ha creado de manera exitosa.

●	En el panel izquierdo seleccione “Your VPCs”. Se debe visualizar la VPC por defecto y la nueva VPC que se ha configurado con el rango de direcciones 172.31.0.0/16.

#### Creación y configuración de las subredes:


●	En la sección de VPC, en el panel izquierdo, localice la opción de “Subnets”.

●	Click en “Subnets”.

●	Click en “Create subnet”. 

Primero crearemos la subred privada y luego la pública.

o	Name tag: Private Subnet A

o	VPC*: Seleccione MyWebApp-VPC

o	Availability Zone: Seleccione la primera zona disponible.

o	IPv4 CIDR block*: 172.31.1.0/24

o	Debe aparecer un mensaje que la subred fue creada de manera exitosa.


Otra vez, click en “Create subnet” para crear la subred privada.

o	Name tag: Public Subnet A

o	VPC*: Seleccione MyWebApp-VPC

o	Availability Zone: Seleccione la primera zona disponible.

o	IPv4 CIDR block*: 172.31.2.0/24

o	Debe aparecer un mensaje que la subred fue creada de manera exitosa.


Otra vez, click en “Create subnet” para crear la subred privada.

o	Name tag: Private Subnet B

o	VPC*: Seleccione MyWebApp-VPC

o	Availability Zone: Seleccione la segunda  zona disponible.

o	IPv4 CIDR block*: 172.31.3.0/24

o	Debe aparecer un mensaje que la subred fue creada de manera exitosa.


Otra vez, click en “Create subnet” para crear la subred privada.

o	Name tag: Public Subnet B

o	VPC*: Seleccione MyWebApp-VPC

o	Availability Zone: Seleccione la segunda zona disponible.

o	IPv4 CIDR block*: 172.31.4.0/24

o	Debe aparecer un mensaje que la subred fue creada de manera exitosa.



#### Creación y configuración del Internet Gateway.

●	En la sección de VPC, en el panel izquierdo, localice la opción de “Internet Gateway”.

●	Click en Internet Gateways.

●	Click en internet Gateway.

o	Name tag: MyWebVPC-IGW

●	Click en Create Internet gateway.

●	Debe aparecer mensaje que el Internet Gateway fue creado de manera exitosa.

●	Ahora, se debe asociar el Internet Gateway a la VPC.

●	Seleccione el internet Gateway creado: MyWebApp-IGW

●	Click en “Actions” y click en “Attach to VPC”.

●	En Available VPCs seleccione MyWebApp-VPC.

●	Click en “Attach Internet Gateway”.


#### Security Group para los NATs Instance.


En el servicio de VPC, escoja la opción “create security group” y configure los siguientes parámetros:

●	Security group name: SG-NAT-Instance

●	Description: Enable outgoing traffic from private subnet to Internet

●	VPC: MyWebApp-VPC


De click en “create security group”. Una vez esté creado vamos a agregar las reglas de tráfico de entrada a este. En la pestaña de “Inbound rules”:

● Click en “Edit inbound rules”. Click “Add rule”. Configure los siguientes parámetros:

o Type: HTTP

o Source: 172.31.1.0/24

o Description: Allow inbound HTTP traffic from servers in the private subnet.

● Click en “Edit inbound rules”. Click “Add rule”. Configure los siguientes parámetros:

o Type: https

o Source: 172.31.2.0/24

o Description: Allow inbound HTTPS traffic from servers in the private subnet

● Click en “Edit inbound rules”. Click “Add rule”. Configure los siguientes parámetros:

o Type: http

o Source172.31.3.0/24

o Description: Allow inbound HTTP traffic from servers in the private subnet

● Click en “Edit inbound rules”. Click “Add rule”. Configure los siguientes parámetros:

o Type: https

o Source: 172.31.4.0/24

o Description: Allow inbound HTTPS traffic from servers in the private subnet

● Click en “Edit inbound rules”. Click “Add rule”. Configure los siguientes parámetros:

o Type: ssh

o Source: MyIP

o Description: Allow inbound SSH access to the NAT instance from your home network (over the internet gateway)

#### Creación de las instancias NAT	

En esta sección se presenta como configuraremos una instancia de NAT. Esta instancia permite enviar tráfico de los equipos que están en la red privada hacia Internet en cada. Para esto, diríjase al “home” de la consola de administración de AWS. Escoja el servicio de EC2. En el panel izquierdo seleccione la opción de “Instances” seleccione la opción “launch instances” y ejecute lo siguientes pasos:

**NAT Instance AZ-A:**

● Busque la imagen: amzn-ami-vpc-nat-hvm-2018.03.0.20181116-x86\_64-ebs

● Seleccione Amazon Linux AMI 2018.03.0.20181116 x86\_64 VPC HVM ebs. Click en select.

● Seleccione el tipo de instancia t2.micro (columna type) y click en “Next:Configure Instance details”.

● Ahora configure, los siguientes parámetros:

o Network: MyWebAPP-VPC

o Subnet: Public Subnet A.

o Auto-assign Public IP: Enable

● Click en “Next:Add storage”.

● Click en “Next:Add tags”.

o Key: Name.

o Value: NAT-Instance.

● Click en “Next:Configure security group”:

o Seleccione la opción de un security group existente. Seleccione “SG-NAT-Instance”

● Click en “Next:Review instance and launch”.

● Click en “Launch”.

● Seleccione an existing key pair or create a new key pair.

o Seleccione el key pair que ud ha creado para el curso.

Finalmente, y, dado que es una NAT instance, para que trabaje de forma adecuada se debe detener la característica de “source/destination checking”. Para esto realice lo siguiente:

● Seleccione el servicio de EC2. En el panel izquierdo click en instances. Click en Running instances.

● Seleccione la casilla de “NAT Instance” desplegada.

● Click en “Actions”. Click en Networking. Click en “Change source/destination check”.

● Seleccione la casilla “Stop”.

● Click en “Save”.


Se repite el procedimiento para la NAT Instance AZ-B.

#### Creación y configuración de las tablas de enrutamiento.

En esta subsección vamos a crear cuatro tablas de enrutamiento (dos por cada AZ), una asociada a cada subred que hemos definido. Igualmente vamos a configurar que la ruta por defecto de cada una de estas tablas de enrutamiento

● Click en “Create route table” para crear la tabla de enrutamiento para la subred privada.

o Name tag: Private Route Table A

o VPC: MyWebAPP-VPC

o Click en “create”.

● Click en “Create route table” para crear la tabla de enrutamiento para la subred pública.

o Name tag: Public Route Table A

o VPC: MyWebAPP-VPC

o Click en “create”.

● Click en “Create route table” para crear la tabla de enrutamiento para la subred privada.

o Name tag: Private Route Table B

o VPC: MyWebAPP-VPC

o Click en “create”.

● Click en “Create route table” para crear la tabla de enrutamiento para la subred pública.

o Name tag: Public Route Table B

o VPC: MyWebAPP-VPC

o Click en “create”.

Ahora se debe crear las rutas por defecto. Para el caso de la subred privada, el tráfico debe ser enviado al NAT Gateway. Para esto en el panel izquierdo, seleccione la opción de Route Tables. Seleccione la subred privada “Private Route Table” y abajo, en el panel de “Routes”

● Click en “Edit routes”.

● Click en “Add route”.

o Destination: 0.0.0.0/0

o Target: Seleccione la instancia del NAT Gateway (NAT-GW-Instance) que se desplegó para cada AZ.

● Click en “Save routes”.

● Debe observar el mensaje de que la ruta se ha editado de manera exitosa.

Por otro lado, para el caso de la red pública, este tráfico debe ser enviado al Internet Gateway. Para esto en el panel izquierdo, seleccione la opción de Route Tables. Seleccione la subred pública “Public Route Table” y abajo, en el panel de “Routes”

● Click en “Edit routes”.

● Click en “Add route”.

o Destination: 0.0.0.0/0

o Target: Seleccione el Internet Gateway (MyWebApp-IGW).

● Click en “Save routes”.

● Debe observar el mensaje de que la ruta se ha editado de manera exitosa.

Ahora debemos asociar a cada tabla de enrutamiento, las subredes. Seleccione la “Private Route Table A” y en la pestaña de “Subnet Associations”:

● Click en “Edit subnet associations”.

● Marque la casilla de la subred privada 172.31.1.0/24.

● Click en Save.

Ahora debemos asociar a cada tabla de enrutamiento, las subredes. Seleccione la “Private Route Table B” y en la pestaña de “Subnet Associations”:

● Click en “Edit subnet associations”.

● Marque la casilla de la subred privada 172.31.3.0/24.

● Click en Save.

Para el caso de las subredes públicas, seleccione la “Public Route Table A” y en la pestaña de “Subnet Associations”:

● Click en “Edit subnet associations”.

● Marque la casilla de la subred privada 172.31.2.0/24.

● Click en Save.

Ahora, seleccione la “Public Route Table B” y en la pestaña de “Subnet Associations”:

● Click en “Edit subnet associations”.

● Marque la casilla de la subred privada 172.31.4.0/24.

● Click en Save.

#### Security groups para  Bastion Hosts	

En este apartado usted creará un grupo de seguridad el cual oficiará como un firewall virtual. De esta forma, cuando usted lance una instancia, usted asociará un o varios grupos de seguridad. 

En el panel de navegación ubicado en la izquierda, en la sección de “Security”. Seleccione la opción de “Security Groups”. Allí puede ver los diferentes “security groups” configurados. En el servicio de VPC, escoja la opción “create security group” y configure los siguientes parámetros:

● Security group name: SG-Bastion

● Description: Enable SSH Access

● VPC: MyWebApp-VPC

De click en “create security group”. Una vez esté creado vamos a agregar las reglas de tráfico de entrada a este. En la pestaña de “Inbound rules”:

● Click “Add rule”. Configure los siguientes parámetros:

o Type: SSH

o Source: MyIP

o Description: Allow ssh traffic


#### Creación de las instancias Bastion Hosts 

En esta sección crearemos una instancia EC2 la cual actuará como Host Bastion. Recuerde que esta VM estará asociada a la subred pública de la VPC por cada zona de disponibilidad.

## **Bastion Host AZ-A:**

Diríjase al “home” de la consola de administración de AWS. Escoja el servicio de EC2. En el panel

izquierdo seleccione la opción de “Instances” seleccione la opción “launch instances” y ejecute lo

siguientes pasos:

● Escoja la imagen de Amazon Machine Image (A|MI) la cual contiene la imagen del sistema

operativo. Seleccione Amazon Linux 2 AMI (HVM), SSD Volume Type (click select).

● Seleccione el tipo de instancia t2.micro (columna type) y click. En “Next:Configure Instance

details”.

● Ahora configure, los siguientes parámetros:

o Network: MyWebAPP-VPCMyLabVPC

o Subnet: Public Subnet A.

o Auto-assign Public IP: Enable

● Click en “Next:Add storage”.

● Click en “Next:Add tags”.

o Key: Name.

o Value: Bastion Host.

● Click en “Next:Configure security group”:

o Seleccione la opción de un security group existente. Seleccione “SG-Bastion”

● Click en “Next:Review instance and launch”.

● Click en “Launch”.

● Seleccione an existing key pair or create a new key pair.

o Seleccione el key pair que ud ha creado para el curso.

## **Bastion Host AZ-B:**

Diríjase al “home” de la consola de administración de AWS. Escoja el servicio de EC2. En el panel izquierdo seleccione la opción de “Instances” seleccione la opción “launch instances” y ejecute lo siguientes pasos:

● Escoja la imagen de Amazon Machine Image (AMI) la cual contiene la imagen del sistema operativo. Seleccione Amazon Linux 2 AMI (HVM), SSD Volume Type (click select).

● Seleccione el tipo de instancia t2.micro (columna type) y click. En “Next:Configure Instance details”.

● Ahora configure, los siguientes parámetros:

o Network: MyWebAPP-VPCMyLabVPC

o Subnet: Public Subnet B.

o Auto-assign Public IP: Enable

● Click en “Next:Add storage”.

● Click en “Next:Add tags”.

o Key: Name.

o Value: Bastion Host.

● Click en “Next:Configure security group”:

o Seleccione la opción de un security group existente. Seleccione “SG-Bastion”

● Click en “Next:Review instance and launch”.

● Click en “Launch”.

● Seleccione an existing key pair or create a new key pair.

o Seleccione el key pair que ud ha creado para el curso.

#### Security Group para Tráfico Web
Escoja la opción “create security group” y configure los siguientes parámetros:

● Security group name: SG-Web

● Description: Enable HTTP Access

● VPC: MyWebApp-VPC

De click en “create security group”.

Una vez esté creado vamos a agregar las reglas de tráfico de entrada a este. En la pestaña de “Inbound rules”, click en “Edit inbound rules” y click en “add rule”. Configure los siguientes parámetros:

● Type: HTTP

● Source: Anywhere

● Description: Permit Web Requests

● Type: HTTPS

● Source: Anywhere

● Description: Permit HTTPS Requests

● Type: SSH

● Source: Anywhere //Preferiblemente ssh SÓLO desde los bastion. 172.31.2.0/24. 172.31.4.0/24.

● Description: Permit SSH Requests

● Type: NFS

● Source: Anywhere

● Description: Permit NFS Requests for EFS

Click “Create security group”.

#### Security Group para Servicio Relacional de Bases de Datos.


En el servicio de VPC, escoja la opción “create security group” y configure los siguientes parámetros:

● Security group name: SG-RDS-DB

● Description: Permit Access from web security group.

● VPC: MyWebApp-VPC

De click en “create security group”. Una vez esté creado vamos a agregar las reglas de tráfico de entrada

a este. En la pestaña de “Inbound rules”:

● Click en “Edit inbound rules”. Click “Add rule”. Configure los siguientes parámetros:

● Click “Add rule”. Configure los siguientes parámetros:

o Type: MySQL/Aurora

o Source: Custom. Digite sg y seleccione el security group creado para web.

o Description: Allow DB connection

De esta forma se configura el security group de la bases de datos para aceptar las peticiones entrantessobre el puerto 3306 desde cualquier instancia EC2 que esté asociada con ese security group.


#### Creación y configuración de la instancia del servidor de bases de datos en la subred privada.
## **Crear el grupo de subred para el servicio RDS.**
Antes que cualquier y con el fin de emplear el servicio de RDS, se debe crear un subnet group el cual es usado para decirle a la bases de datos cuales subredes pueden ser usadas por ésta. Cada subnet group de bases de datos, requiere tener subredes al menos en dos zonas de disponibilidad (AZ).

● En la sección de servicios, escoja RDS.

● En el panel de configuración izquierda, click en Subnet Groups.

● Click en Create DB Subnet Group y configure los siguientes aspectos:

o Name: DB-Subnet Group.

o Description: Subnet group for RDS.

o VPC: MyWebbApp-VPC

● Baje hasta la sección que permita adicionar las subredes.

● Despliegue la lista de valores de subredes y seleccione las subredes asociadas con los rangos

172.31.1.0/24 y 172.31.3.0 /24.

● Click en create.

Este grupo de subred de bases de datos va a ser utilizado para desplegar el motor de bases de datos.

## **Crear una instancia de Amazon RDS.**

En esta sección se procederá a desplegar una instancia de bases de datos de MySql en un entorno de múltiples zonas de disponibilidad. Cuando se lanza este servicio, de manera automática amazon crea una instancia principal de la bases de datos y sincroniza los datos con una estancia secundaria que despliega en una zona de disponibilidad diferente en la cual desplegó la primera instancia.


● Click Create database.

● Click en el método Standard create.

● Seleccione el motor de bases de datos MySQL.

● Seleccione free tier como template.

● En la sección de settings.

o	DB instance identifier: exampledb

o	Master username: exampleuser

o	Master password: examplepass

o	Confirm password: examplepass

● En la sección de DB instance size:

o DB instance class: seleccione burstable classes (includes t classes).

o Seleccione db.t2.micro.

● En la sección Storage, configure:

o Storage type: General purpose (SSD).

o Allocated storage: 20

● En la sección de Availability y durability:

o Multi-AZ deployment. Seleccione a standby instance (recommender for production

usage).

● En la sección de Connectivity

o Virtual Private Cloud: (VPC): MyWebApp-VPC.

o Expanda la opción para Additional connectivity configuration:

▪ Selecciona las zonas de disponibilidad y subredes para el despliegue de la base

de datos. Recuerde que esta va ubicada en la subred privada.

o Public access: Click en No.

o Existing VPC security groups: Seleccione el security group definido para la BD.

● En la sección de Database authentication:

o Seleccione Password authentication.

● Expanda la opción para Additional configuration:

o Initial database name: wordpress.

o No marque la opción de “enable automatic backups”

o No marque la opción de “enable enhanced monitoring”

● Click en create database. En este momento su bases de datos va a ser desplegada. Tomará unos 5 mins en ser desplegada.

#### Creación y configuración del sistema de archivos compartidos EFS

En esta sección se presenta como crearemos un sistema de archivos EFS que será compartido por todas las instancias del Servidor Web/PHP (wordpress). Este sistema de archivos permite compartir todos los archivos de la aplicación wordpress así como todos los archivos estáticos que normalmente maneja un servidor CMS (imágenes, pdfs, videos, sonidos, y en general muchos otros tipos de archivos). Para esto, diríjase al “home” de la consola de administración de AWS. Escoja el servicio de EFS. 

En el panel izquierdo seleccione la opción de “File Systems” seleccione la opción “Create file system” y ejecute lo siguientes pasos:

● Name - optional: WP-EFS

● Virtual Private Cloud: (VPC): MyWebApp-VPC

● Availability and Durability: Regional

● Click. Create

Una vez creado en sistema de archivos, hacer Click en WP-EFS y realice los siguientes cambios:

● Click en “Network”

● Click en “Manage”

○ Borre en “Security groups” con la X el grupo de seguridad creado por defecto. Haga esto por cada zona de disponibilidad.

○ Adicione en “Security groups” en cada zona de disponibilidad:

■ Seleccione “Web Security Group”

○ Click: Save

Obtener la dirección IP del punto de montaje: (\*+\*)

● Le damos Click en WP-EFS (fs-f168b545)

● Click en: Attach

● Seleccione: Mount vía IP

● Anote la <dirección-IP WP-EFS> del punto de montaje para actualizarlo en la instancia del Web Wordpress

● Listo

#### Creación y configuración de la Instancia del Servidor Web Wordpress

En esta sección crearemos una instancia EC2 la cual actuará como Web Server. Recuerde que esta VM estará asociada a la subred privada a la VPC. Esta instancia será creada para servir como Imagen AMI más adelante en el Autoscaling Group.

Diríjase al “home” de la consola de administración de AWS. Escoja el servicio de EC2. En el panel izquierdo seleccione la opción de “Instances” seleccione la opción “launch instances”. Escoja la imagen de Amazon Machine Image (AMI) la cual contiene la imagen del sistema operativo. Seleccione Amazon Linux 2 AMI (HVM), SSD Volume Type (click select). Seleccione el tipo de instancia t2.micro (columna type) y click “configure instance details”. Ahora configure, los siguientes parámetros:

● Network: MyLabVPC

● Subnet: Private Subnet A

● Auto-assign Public IP: disable

● File systems: Click en “Add file system”

o Seleccionar el EFS Creado anteriormente (WP-EFS)

o Como ruta en vez de /mnt/efs/fs1 -> coloque /mnt/efs/wordpress

o Des-seleccionar la opción: Automatically create and attach the required security groups.

● Click en “Add storage”.

● Click en “Add tags”.

o Key: Name.

o Value: Web Server.

● Click en “Configure security group”:

o Seleccione la opción de un security group existente. Seleccione “Web Security Group”

● Click en “Review and launch”.

(Nota: Cuando esta instancia bootea, el sistema de archivo EFS se ‘monta’ automáticamente en el archivo /etc/fstab, PERO está realizando en mount con el nombre de dominio, PERO no funciona, se opta por utilizar la dirección IP, Mirar la IP del punto anterior (*+*)

Debe Editar el archivo /etc/fstab así:

Originalmente:

{file_system_id_1}.efs.us-east-1.amazonaws.com:/ /mnt/efs/wordpress nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport,_netdev 0 0

Cambie la línea correspondiente al mount así:

172.16.1.85:/ /mnt/efs/wordpress nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport,_netdev 0 0
 
OJO OJO: Ud deberá usar su propia IP, Mirar la IP del punto anterior (*+*)

Para que todo funcione OK, debe bootear la máquina, y verifique después de booteado, que montó el EFS así:

	$ df -k
	
	le debe aparecer el /mnt/efs/wordpress
	
	Este directorio debe esta vacio, si tiene archivos borrelos:
	
	$ sudo rm -rf /mnt/efs/wordpress/*
	
Para que la imagen quede lista para copiar, debe verificar que el wordpress está corriendo:

	$ docker-compose up -d


#### instalación y configuración el Servidor Web/PHP (wordpress)

Una vez conectado a la máquina vía ssh, se procede a instalar docker, docker-compose y el archivo docker-compose.yml de wordpress, para esto se requiere que ejecute los siguientes comandos:

// EN LA MÁQUINA EC2-WEB-WORDPRESS:

// instalar docker:

sudo amazon-linux-extras install docker -y

sudo yum install git -y

sudo systemctl enable docker

sudo systemctl start docker

sudo usermod -a -G docker ec2-user

// instalar docker-compose:

sudo curl -L https://github.com/docker/compose/releases/download/1.27.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

// utilizar este docker-compose.yml:

[ec2-user@ip-172-16-1-64 ~]$ more docker-compose.yml 

version: '3.1'

services:

  wordpress:
  
    image: wordpress
    
    restart: always
    
    ports:
    
      - 80:80
      
    environment:
    
      WORDPRESS_DB_HOST: dns-url-rdsdns-url-rds
      
      WORDPRESS_DB_USER: exampleuser
      
      WORDPRESS_DB_PASSWORD: examplepass
      
      WORDPRESS_DB_NAME: exampledb 
      
    volumes:
    
      - /mnt/efs/wordpress:/var/www/html
      
volumes:

  wordpress: 
  

Nota:  Actualice el valor de dns-url-rds al real de la base de datos MySQL del anterior numeral (4.10)


#### Creación de la AMI para para el Servicio de Auto Scaling:

A continuación, vamos a crear una AMI del servidor web que contiene el wordpress. De esta forma se guardaran el contenido del boot disk y las nuevas instancias desplegadas a partir de esta se van a instanciar con un contenido idéntico. Es así como una Amazon Machine Image se convierte en una plantilla que contiene una configuración básica la cual sirve para instanciar posteriormente máquinas. 

Para crear una AMI, en el home de la consola de administración, seleccione el servicio de EC2.

●	En el panel izquierdo, click en Instances. Confirme que la instancia de la cual vamos a realizar la AMI esté corriendo (web server, 2/2 checks passed). Click en refresh para actualizar si es necesario.

●	Seleccione Web Server.

●	En el menú de Actions, click en Image > Create Image y configure los siguientes parámetros:

o	Image name: Web Server AMI

o	Image description: Lab AMI for Web Server

o	Click en Create Image. Observará un mensaje de confirmación que muestra el AMI ID para la nueva AMI.

o	Click en Close.

Ahora podrá usar esta AMI en el servicio de Auto Scaling.

#### Creación y configuración del balanceador de carga.	

En esta sección, vamos a implementar un balanceador de carga. A través de este distribuiremos las peticiones entrantes hacia múltiples instancias y en diferentes zonas de disponibilidad. Para esto realizaremos lo siguiente:

●	En el home de la consola, seleccione servicio de EC2.

●	En el panel izquierdo, click Load Balancers.

●	Click en Create Load Balancer. 

●	Como puede observar, se definen tres tipos diferentes de balanceadores de carga.  Haga click en Create en la sección de Application Load Balancer.

●	En la sección de configuración básica, configure los siguientes parámetros:

o	Name: ELB-MyWebApp

o	Scheme: Seleccione internet-facing.

o	IP address type: ipv4

o	VPC: MyWebApp-VPC.

●	En la sección de Listeners, configure los siguientes parámetros:

o	Load Balancer Protocol: http.

o	Load Balancer Port: 80.

●	En la sección de Availability Zones:

o	VPC: MyWebApp-VPC

o	Availability Zones: Seleccione la casilla de cada zona de disponibilidad.  Y seleccione la subred pública para ambas zonas. Recuerde que el balanceador de carga va desplegado en la subred pública.

o	Click en Next: Configure Security Settings

●	En la sección de Security Settings, puede ignorar el mensaje y dar click en Next: Configure Security Groups.

●	En la sección de Configure Security Group, seleccione la opción de escoger un security group existente. En este sentido, escoja el web security group definido anteriormente para permitir el tráfico entrante de peticiones web. Click en Next: Configure Routing.

●	En la sección de Configure Routing, se procede a configurar hacia donde enviar las peticiones que llegan al balanceador de cargas. Aquí vamos a crear un Target group que será usado por el servicio de autoscaling. 

o	Name: TG-MyWebApp

o	Click en Next: Register Targets.

●	El servicio de autoscaling de manera automática registra las instancias como targets. Esto lo haremos posteriormente.

●	Click en Next: Review. Verifique los parámetros configurados. 

●	Click en Create.

Adicionarle el Listener en HTTPS con certificado SSL en Letsencrypt o CloudFlare.

Importar los certificados con IAM

Certificate private key(PEM encoded): (copy-paste de privkey.pem)

Certificate body(PEM encoded): (copy-paste de cert.pem)

Certificate chain(PEM encoded): (copy-paste de chain.pem)


#### Creación y configuración del Grupo de Auto Scaling.	

En esta sección se procede a configurar un grupo de Auto Scaling así como la configuración de lanzamiento (Launch Configuration). Esta configuración de lanzamiento es un template que utiliza el grupo de Auto Scaling para instanciar las máquinas EC2. Cuándo se crea una configuración de lanzamiento, se hace necesario especificar la información de la instancia tal como: AMI, tipo de instancia, key pair, security groups y disco.

En el home de la consola de administración, seleccione el servicio de EC2.

●	En el panel izquierdo, seleccione Launch configurations.

●	Click en Create launch configuration.

●	Proceda a configurar los siguientes aspectos:

o	Launch configuration name: MyWebbApp

o	Amazon machine image: Seleccione el nombre de la AMI creada: Web Server – AMI.

o	Instance type: Click en Choose instance type.

▪	Buscar y seleccionar t2.micro

▪	Seleccione Choose.

o	Additional Configuration:

▪	Monitoring: Seleccione la casilla para Enable EC2 instance detailed monitoring within Cloud Watch. Esto va a permitir al Auto Scaling reaccionar rápidamente  a cambios en la utilización.

o	En Security Groups, se configurará utilizando el security group que realmente ha sido creado para este laboratorio.

▪	Seleccione Select an existing security group.

▪	Escoja Web Security group.

o	En la sección de Key Pair:

▪	Key pair options: Choose an existing key pair.

▪	Existing key pair: la definida por ud para el curso.

▪	Marque la casilla de I acknowledge…

▪	Click en Create launch configuration.

Ahora se procederá a crear el grupo de Auto Scaling. Para esto:

●	Seleccione el launch configuration creado.

●	Click en el menú Actions. Click en Create auto scaling group.

●	Digite el nombre del auto scaling group:

o	Name: MyWebApp-Auto Scaling Group

●	Click en Next.

●	En la sección de Network configure los siguientes parámetros:

o	Network: MyWebApp-VPC.

o	Subnet: Seleccione las dos subredes privadas ubicadas en las dos zonas de disponibilidad.

o	Click en Next.

●	En la sección de Configure advanced options:

o	Seleccione la casilla para: Attach to an existing load balancer.

o	Escoja: Choose from your load balancer target groups

o	Escoja el target group que se creó para la aplicación. TG-MyWebApp.

o	Marque la casilla de Enable group metrics collection within CloudWatch.

o	Click en Next.

●	En la sección de Configure Group Size 

o	Desired capacity: 2

o	Minimum capacity: 2

o	Maximum capacity: 3

o	Esta configuración va a permitir escalar entre dos y tres máquinas.

●	En esta misma sección para scaling policies:

o	Seleccione target tracking scaling policy.

o	Scaling policy name: MyWebApp-ScalingPolicy

o	Metric type: Average CPU utilization.

o	Target Value: 60.


Esta configuración le va indicar al servicio de auto scaling mantener en promedio una utilización de CPU de las instancias del 60%. De esta forma, el servicio de auto scaling automáticamente adicionará o quitará capacidad tanto como sea requerido con el fin de mantener la métrica seleccionada lo más cercana posible al umbral definido. Click en Next.

o	Click en Next. Add notifications.

o	Click en Next. Add tag.

▪	Key: Name

▪	Value: Web Instance

o	Click. Next.

●	Revise la información. Click en Create Auto Scaling Group.

	
Para mas informacion revisar el documento https://drive.google.com/file/d/1SSJYaouGtxkGtn4rL3IMyGCbJQuWf0IZ/view en el cual estan los pasos a seguir para las respectivas configuraciones.
