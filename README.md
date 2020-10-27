# learning-docker

# Projeto criado para aprender e aplicar os conceitos do Docker na prática

- Exemplos de Dockerfiles:
 
 FROM debian

RUN apt-get update && apt-get install -y apache2 && apt-get clean
ENV APACHE_LOCK_DIR="/var/lock"
ENV APACHE_PID_FILE="/var/run/apache2.pid"
ENV APACHE_RUN_USER="www-data"
ENV APACHE_RUN_GROUP="www-data"
ENV APACHE_LOG_DIR="/var/log/apache2"

LABEL description="Webserver"

VOLUME /var/www/html/
EXPOSE 80

----------------------------------------------------------------------------------------------
FROM debian

RUN apt-get update && apt-get install -y apache2 && apt-get clean
ENV APACHE_LOCK_DIR="/var/lock"
ENV APACHE_PID_FILE="/var/run/apache2/apache2.pid"
ENV APACHE_RUN_USER="www-data"
ENV APACHE_RUN_DIR="/var/run/apache2"
ENV APACHE_RUN_GROUP="www-data"
ENV APACHE_LOG_DIR="/var/log/apache2"

LABEL description="Webserver"

VOLUME /var/www/html/
EXPOSE 80

ENTRYPOINT ["/usr/sbin/apachectl"]
CMD ["-D", "FOREGROUND"]

----------------------------------------------------------------------------------------------
package main
import "fmt"

func main() {
	fmt.Println("GIROPOPS STRIGUS GIRUS - LINUXTIPS")
}

----------------------------------------------------------------------------------------------
FROM golang

WORKDIR /app
ADD . /app
RUN go build -o goapp
ENTRYPOINT ./goapp

----------------------------------------------------------------------------------------------
FROM golang AS buildando

ADD . /src
WORKDIR /src
RUN go build -o goapp

FROM alpine:3.1

WORKDIR /app
COPY --from=buildando /src/goapp /app
ENTRYPOINT ./goapp

----------------------------------------------------------------------------------------------

# Exemplos de comandos utilizados no Dockerfile:

- ADD => Copia novos arquivos, diretórios, arquivos TAR ou arquivos remotos e os adicionam ao filesystem do container;

- CMD => Executa um comando, diferente do RUN que executa o comando no momento em que está "buildando" a imagem, o CMD executa no início da execução do container;

- LABEL => Adiciona metadados a imagem como versão, descrição e fabricante;

- COPY => Copia novos arquivos e diretórios e os adicionam ao filesystem do container;

- ENTRYPOINT => Permite você configurar um container para rodar um executável, e quando esse executável for finalizado, o container também será;

- ENV => Informa variáveis de ambiente ao container;

- EXPOSE => Informa qual porta o container estará ouvindo;

- FROM => Indica qual imagem será utilizada como base, ela precisa ser a primeira linha do Dockerfile;

- MAINTAINER => Autor da imagem; 

- RUN => Executa qualquer comando em uma nova camada no topo da imagem e "commita" as alterações. Essas alterações você poderá utilizar nas próximas instruções de seu Dockerfile;

- USER => Determina qual o usuário será utilizado na imagem. Por default é o root;

- VOLUME => Permite a criação de um ponto de montagem no container;

- WORKDIR => Responsável por mudar do diretório / (raiz) para o especificado nele;

----------------------------------------------------------------------------------------------

# DockerHub e Registry - Exemplo comandos: 

- docker image inspect debian
- docker history linuxtips/apache:1.0
- docker login
- docker login registry.suaempresa.com
- docker push linuxtips/apache:1.0
- docker pull linuxtips/apache:1.0
- docker image ls
- docker container run -d -p 5000:5000 --restart=always --name registry registry:2
- docker tag IMAGEMID localhost:5000/apache

----------------------------------------------------------------------------------------------

# Docker Machine
 - Para fazer a instalação do Docker Machine no Linux, faça:
 #curl -L https://github.com/docker/machine/releases/download/v0.16.1
/docker-machine-`uname -s`-`uname -m` >/tmp/docker-machine

- chmod +x /tmp/docker-machine 
- sudo cp /tmp/docker-machine /usr/local/bin/docker-machine

# Abaixo alguns comandos básicos usando docker-machine:

- docker-machine version
- docker-machine create --driver virtualbox linuxtips
- docker-machine ls
- docker-machine env linuxtips
- eval "$(docker-machine env linuxtips)"
- docker container ls
- docker container run busybox echo "LINUXTIPS, VAIIII"
- docker-machine ip linuxtips
- docker-machine ssh linuxtips
- docker-machine inspect linuxtips
- docker-machine stop linuxtips
- docker-machine ls 
- docker-machine start linuxtips
- docker-machine rm linuxtips
- eval $(docker-machine env -u)

----------------------------------------------------------------------------------------------

# Docker Swarm exemplos de comandos:

- docker swarm init
- docker swarm join --token \ SWMTKN-1-100_SEU_TOKEN SEU_IP_MASTER:2377
- docker node ls
- docker swarm join-token manager
- docker swarm join-token worker
- docker node inspect LINUXtips-02
- docker node promote LINUXtips-03
- docker node ls
- docker node demote LINUXtips-03
- docker swarm leave
- docker swarm leave --force
- docker node rm LINUXtips-03
- docker service create --name webserver --replicas 5 -p 8080:80  nginx
- curl QUALQUER_IP_NODES_CLUSTER:8080
- docker service ls
- docker service ps webserver
- docker service inspect webserver
- docker service logs -f webserver
- docker service rm webserver
- docker service create --name webserver --replicas 5 -p 8080:80 --mount type=volume,src=teste,dst=/app  nginx
- docker network create -d overlay giropops
- docker network ls
- docker network inspect giropops
- docker service scale giropops=5
- docker network rm giropops
- docker service create --name webserver --network giropops --replicas 5 -p 8080:80 --mount type=volume,src=teste,dst=/app  nginx
- docker service update <OPCOES> <Nome_Service> 
----------------------------------------------------------------------------------------------

# Secrets, exemplos de comandos:

echo 'minha secret' | docker secret create 
docker secret create minha_secret minha_secret.txt
docker secret inspect minha_secret
docker secret ls
docker secret rm minha_secret
docker service create --name app --detach=false --secret db_pass  minha_app:1.0
docker service create --detach=false --name app --secret source=db_pass,target=password,uid=2000,gid=3000,mode=0400 minha_app:1.0
ls -lhart /run/secrets/
docker service update --secret-rm db_pass --detach=false --secret-add source=db_pass_1,target=password app






 	
 	
