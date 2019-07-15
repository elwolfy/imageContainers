# imageContainers
Staying image containers of cluster scalability and availability by software

listar containers exited (parados)
=================================
docker ps --filter "status=exited"
docker ps -f "status=exited"
docker ps -a

instalar docker
===============
sudo yum check-update
curl -fsSL https://get.docker.com/ | sh

listar imagenes docker
=====================
docker image ls

remove container
================
docker stop idContainer
docker container rm idContainer

iniciar docker
=============
systemctl start docker.service

reiniciar container
===================
docker start containerId

eliminar imagen docker
======================
docker rmi Image Image

construir una imagen docker desde un archivo dockerfile
=======================================================
docker build -t nginx_image .

listar pid y puerto
===================
netstat -tulpn

eliminar pid
============
kill -9 pid_of_process

0. leer el ip de docker container
=================================

docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' 6574d6d1f658

Tabla
postgresql 172.17.0.2
mtp-backend 172.17.0.3
mtp-backend 172.17.0.4
mtp-apigateway 172.17.0.6
mtp-apigateway 172.17.0.7
mtp-apigateway 172.17.0.8




1. ejecutar imagen postgresql en docker:
===================================

descargar imagen docker hub
===========================
docker pull postgres

pasos:
1. imagen de postgresql para docker
https://github.com/CentOS/CentOS-Dockerfiles/tree/master/postgres/centos7
**** crear cada archivo con nano 
docker build -rm -t postgres/postgresql .

2.
docker run --name=postgresql -d -p 5432:5432 postgres/postgresql

#entrar como bash
docker exec -it 0132e9897816 bash

#cambiar password root de postgresql
sudo -u postgres psql postgres

\password

#crea base de datos
CREATE DATABASE dbmtppoc;

#listar bases de datos
\l
\c DBNAME

#crear tabla
CREATE TABLE public.persona (persona_id serial NOT NULL, nombre varchar(100) NULL, apellido varchar(100) NULL, direccion varchar(150) NULL, CONSTRAINT pk_persona PRIMARY KEY (persona_id));

alter sequence persona_persona_id_seq rename to persona_seq;

select * from public.persona; 

insert into public.persona (nombre, apellido, direccion) values ('juan', 'lopez', 'Av. brasil 123');
insert into public.persona (nombre, apellido, direccion) values ('maria', 'perez', 'Av. chile 123');
insert into public.persona (nombre, apellido, direccion) values ('carlos', 'castro', 'Av. venezuela 123');

obtener base datos
==================
GRANT ALL PRIVILEGES ON DATABASE database_name TO username;

aplicacion spring boot
======================
http://localhost:8092/mtp-backend/api/persona/get/2


mtp-backend
===========
compilar el software componente java

vi Dockerfile

FROM java:8
VOLUME /tmp
ADD apigatewayarchetype-0.0.1.jar  mtp-backend.jar
RUN bash -c 'touch /mtp-backend.jar'
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/mtp-backend.jar"]

FROM openjdk:8-jdk-alpine
VOLUME /tmp
ARG JAR_FILE=apigatewayarchetype-0.0.1.jar
ADD ${JAR_FILE} apigateway.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/apigateway.jar"]

docker build -t mtp-backend .

docker images

crear un container mtp-backend
==============================
docker run mtp-backend

#opcional ---- docker run --name=postgresql -d -p 5432:5432 postgres/postgresql




docker build -t mtp-apigateway .

docker run mtp-apigateway


HAPROXY
=======

mkdir haproxy
[root@localhost usr]# cd haproxy
[root@localhost haproxy]# vi Dockerfile


FROM haproxy:1.7
COPY haproxy.cfg /usr/local/etc/haproxy/haproxy.cfg

docker build -t mtp-haproxy .
docker run mtp-haproxy
cd /etc/haproxy

restaurar haproxy
================
systemctl restart haproxy
setsebool -P haproxy_connect_any 1

service haproxy status -l


========================


exportar docker image to tar file
=================================
https://docs.docker.com/engine/reference/commandline/export/

usar pscp para exportar remotamente tar de containers
=========================================
https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter5.html
pscp [options] source [source...] [user@]host:target

importar docker image desde tar file
============================
sudo tar -c . | docker import - mtp-backend


docker save -o /home/jorge/imagesContainer/postgresql.tar postgres/postgresql



