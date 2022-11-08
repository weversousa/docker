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

Utilizando uma analogia com POO, podemos compararr um container a um
objeto (instância), enquanto a imagem seria uma classe (modelo).

