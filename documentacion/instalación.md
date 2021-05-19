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

Crear y configurar las tablas de enrutamiento.

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

#### Creación y configuración de las tablas de enrutamiento.	
#### Creación de las instancias Bastion Hosts	
#### Security Group para Tráfico Web	
#### Security Group para Servicio Relacional de Bases de Datos.
#### Creación y configuración de la instancia del servidor de bases de datos en la subred privada.
#### Creación y configuración del sistema de archivos compartidos EFS	
#### Creación y configuración de la Instancia del Servidor Web Wordpress	
- instalación y configuración el Servidor Web/PHP (wordpress)	
- Creación de la AMI para para el Servicio de Auto Scaling.	
- Creación y configuración del balanceador de carga.	
- Creación y configuración del Grupo de Auto Scaling.	

	
Para mas informacion revisar el documento https://drive.google.com/file/d/1SSJYaouGtxkGtn4rL3IMyGCbJQuWf0IZ/view en el cual estan los pasos a seguir para las respectivas configuraciones.
