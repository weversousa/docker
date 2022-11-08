# Docker

## Instalação

https://docs.docker.com/engine/install/ubuntu/

### Erros

~~~bash
weverlinux@DESKTOP-LM0HPNH:~$ sudo docker run hello-world
docker: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?.
See 'docker run --help'.
~~~

#### Resolvendo

~~~bash
weverlinux@DESKTOP-LM0HPNH:~$ sudo service docker status
 * Docker is not running
~~~

~~~bash
weverlinux@DESKTOP-LM0HPNH:~$ sudo service docker start
 * Starting Docker: docker
~~~

~~~bash
weverlinux@DESKTOP-LM0HPNH:~$ sudo docker run hello-world
~~~

## Imagem vs Container

Pensando em POO, a Imagem seria uma Classe e o Container um Objeto/Instância.

