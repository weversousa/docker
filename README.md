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

## Inicializar

Para verificar o estado do serviço `service docker status`.

Para inicializar o serviço `service docker start`.

Todas opções `{start|stop|restart|status}`

## Sintaxe

`docker [ Management Command ] [ Command ] [ Options ]`

Depois do **Options** vai variar conforme o **Management Command**.

A melhor que coisa a se fazer é usar a ajuda para saber o que pode ser usado  
após cada comando, para isso:
* `docker --help`
* `docker [ Management Command ] --help`
* `docker [ Management Command ] [ Command ] --help`

Após o comando **docker** o **--help** pode ser usado em "qualquer" lugar.

## Sobre

* `docker version`
    * Client: Docker Engine - Community
        * Versão
    * Server: Docker Engine - Community
        * Está funcionando
        * A máquina vai poder subir Containers

## Listar

Todos os **Management Command** podem ser listados.

Alguns deles são:
* `docker [ container|image|network|volume ] ls -a -q`
    * **ls**
        * Lista somente o que estiver rodando.
    * **-a**
        * Opcional, lista também o que está parado.
    * **-q**
        * Opcional, lista somente os IDs.

## Inspecionar

Todos os **Management Command** podem ser inspecionados.

Alguns deles são:
* `docker [ container|image|network|volume ] inspect --format='{{json .[ Chave ].[Chave]}}' [ nome|id ]`
* `docker container inspect --format='{{json .Config.Env}}' [ nome|id ]`

O comando **inspect** sozinho retorna um JSON uma descriçao completa do objeto.

A flag **-f** permite especificar a chave do JSON que deseja obter.

## Imagem

Para se criar um Container é preciso uma **Imagem**.

Para baixar uma **Imagem**:
* `docker image pull [ nome_imagem:versão ]`.
* `docker image pull postgres:15.0`.
* `docker image pull apache:latest`.
* `docker image pull nginx:alpine`.

Após isso pode usar o comando **ls** para ver as imagens baixadas.

## Container

Criar:
* `docker container create [ image ]`

Caso a **Imagem** não tenha sido baixada ainda, ela será baixada na hora.

Inicializar:
* `docker container start [ nome_container ]`

Criar e Inicializar:
*  `docker container run -d [ image ]`
    * **run** cria e incializa.
    * **-d** é para que o Container seja executado em background, se ao criar  
    um Container de um servidor web por exemplo pode ser o nginx e não usar  
    essa flag, automaticamente o seu terminal vai passar a exibir os processos  
    do nginx, e você não podera usar o terminal até encerrar "Ctrl + C".

### Flags

* **--name**
    * Boa prática dar nome a eles.
* **--p [ host:guest ]**
    * Definer as portas.
* **--P**
    * As portas são definidas automaticamente.

### Sub-comandos

* `docker container logs [ nome|id  ]`
    * Para exibir os logs do Container
* `docker container top [ nome|id  ]`
    * Para exibir os processos do Container
* `docker container states [ nome|id ]`
    * Para exibir dados como uso da CPU, Memória Usada/Mwmória Limite de todos  
    os Containers
    * O nome|id é opcioanal, sem eles exibe sobre todos os Containers.

### Terminal do Container

Mas é possível entrar no Container e usar seu o terminal Linux.

Umas Imagens usam o **bash** já outras o **sh**.

Entrar no Container no momento da criação:
* `docker container run -d -it [ image ] [ bash|sh ]`
    * **-i**
        * Interativo.
    * **-t**
        * Terminal.

É importante ao usar **run** não esquecer da flag **-d**.

Entrar em um Container que já existe:
* `docker container start [ nome|id_container ]`
    * Somente se não estiver rodando.
* `docker container exec -it [ nome|id_container ] bash`

## Imagem vs Container

Utilizando uma analogia com POO, podemos compararr um container a um
objeto (instância), enquanto a imagem seria uma classe (modelo).

## Rede

Ao instalar o Docker, ele já vem com 3 redes configuradas.

Essas redes foram nomeadas com o nome do driver que ela usa.

### Rede Bridge

Bridge no conceito de rede é quando é feito uma ponte.

Com exmeplo o VirtualBox, a máquina Virtual vai ter um IP na mesma faixa de
rede da Host OS.

No caso do Docker não, a rede Bridge faz um NAT, faz com que as faixas de
IPs sejam diferentes.

* Rede padrão dos Containers
* Comunicação interna enetre Containers
* Bridge (NAT)
* Melhores práticas é criar uma nova rede virtual para cada app.
    * my_web = mysql, apache, php
    * my_api = flask, nodejs

~~~bash
docker container run -d --name c_1 --network 'bridge' nginx:alpine
docker container run -d --name c_2 --network 'bridge' nginx:alpine
~~~

~~~bash
docker container ls
~~~

~~~bash
docker network inspect --format='{{json .Containers}}' bridge
~~~

* Dentro da chave Containers, vão estar os 2 associados a essa rede.
    * Pegar os IPv4Address deles.

~~~bash
docker container exec -it c_1 ping [ ip_c_1 ]
docker container exec -it c_2 ping [ ip_c_2 ]
~~~

* Só testar a comunicação entre eles fazendo um ping de um no outro.

#### Atenção!!!

É importante entender que os Containers estão na mesma rede!

O DRIVER brigde permite conexão entre Containers desde que estejam na mesma  
rede!

Se criarmos outra rede de nome qualquer com driver bridge ela não vai conseguir  
se conectar com c_1 e c_2, pois mesmo ela sendo bridge ela é outra rede.

O `--network 'bridge'` é o nome e não o driver, que fique claro! Essa rede já  
vem criada com o Docker, e claramenet ela usa o driver conforme seu nome.

Poderia ser `--network 'abobrinha'`, quando abobrinha for criada o driver que  
representa ela será especificado.

#### DNS

Ao realizar o ping nos exemplos acima é obrigatório que seja por IP.

Ao usar a rede que já vem por padrão não é possível fazer a comunicação através  
do nome da rede.

Para se realizar uma comuniacação com outro Container atrvés do nome da rede é  
obrigatório criar uma nova rede **com um nome**.

Criar uma rede (Bridge|Host|None):
* `docker network create [ Options ] [ nome_rede ]`

~~~bash
docker network create --driver 'bridge' n_b_1
docker container run -d --name c_3 --network 'n_b_1' nginx:alpine
docker container run -d --name c_4 --network 'n_b_1' nginx:alpine
docker container exec -it c_3 ping c_4
docker container exec -it c_4 ping c_3
~~~

* Pode até fazer um teste e tentar comunicação com c_1 e c_2 através do IP  
deles. Vai ver que não tem comunicação entre redes diferentes.

### Rede Host

Nesse caso o IP do Container é o mesmo da Host OS.

* Faz bridge
* Não precisa publicar portas (-p ou -P)
* Não consegue iniciar vários Containers com a mesma porta
* Não funciona em modo swarm

~~~bash
docker container run -d --name h_1 --network 'host' nginx:alpine
docker container run -d --name h_2 --network 'host' nginx:alpine
~~~

~~~bash
docker container ls
~~~

* O nginx usa a porta 80 por padrão, ao dar o ls vai ver que só um dos  
Containers vai estar rodando, pois em host não é possível usar a mesma  
porta juntas.

~~~bash
docker network inspect --format='{{json .Containers}}' host
~~~

* Vai ter somente o Container que está rodando.

~~~bash
docker container logs h_2
~~~

* `bind() to [::]:80 failed (98: Address in use)`
    * Deixa claro que falhou pois a porta 80 está em uso.

#### Atenção!!!

É o mesmo texto usado acima com Bridge.

### Rede None

Não possui acesso externo e nem de outros Containers.

## Volume

É uma forma de mapear um diretório do Host OS para dentro do Container.

Resumindo, é um atalho de uma pasta de fora do Container colocado dentro
dele, então ao salvar coisas nessa pasta elas vão estar sendo salvas fora dele.

Todo Container nasce para morrer, sendo assim sempre que ele morre os seus  
dados vão juntos.

Por isso criamos volumes, por seu uma pasta do Host OS, os dados não morrem com  
o Container.

Sintaxe:
* `docker volume create [ Options ] [ nome ]`

Após isso pode inspecionar o **Volume** e acessar a chave **Mountpoint**, onde  
contém o diretório padrão que o Docker usa, normalmente é  
`/var/lib/docker/volumes/v_postgres/_data`

Associar o Volume ao Container:
* `docker container run \`  
    `--mount type=volume,source=[ nome|id_volume ],destination=/[ guest_os_diretório ] \`  
    `[ image ]`

### Tipo Bind

É quando usamos um diretório já existente.

Eu tenho um diretório e quero que ele seja montado dentro do Container.

Essa forma só pode ser usada junto a criação do Container.

Exemplo:
* `docker container run \`  
    `--mount type=bind,source=/[ host_os_diretóro ],destination=/[ guest_os_diretório ] \`  
    `[ image ]`

## Remover

* `docker [ container|image|network|volume ] rm [ nome|id ]`
    * Somente o que está parado ou não for dependência de outro.
* `docker [ container|image|network|volume ] rm -f [ nome|id ]`
    * A flag **-f** vai forçar o que estiver rodando.
* `docker [ container|image|network|volume ] prune`
    * Todos de uma vez (parado e não dependência).
* `docker container rm $(docker container ls -a -q)`
    * Listas e remove todos os Containers de uma vez

## Resumão CLI

~~~bash
docker image pull postgres:15.0
docker network --driver=bridge n_postgres
docker volume create v_postgres
docker container run \
-d \
--env=POSTGRES_USER=postgres --env=POSTGRES_DB=postgres --env=POSTGRES_PASSWORD=1234 \
--name=c_postgres \
--network=n_postgres \
-p 5432:5432 \
--volume=v_postgres \
postgres:15.0
~~~

## Dockfile

Criamos a nossa própria Imagem.

~~~Dockerfile
FROM debian

RUN apt-get update && apt-get install -y apache2 
ENV APACHE_LOCK_DIR = "/var/lock"
ENV APACHE_PID_FILE = "/var/run/apache2.pid"
ENV APACHE_RUN_USER = "www-data"
ENV APACHE_RUN_GROUP = "www-data"
ENV APACHE_LOG_DIR = "/var/log/apache2"

COPY index.html /var/www/html/

LABEL description="Webserver"
LABEL version="1.0.0"

USER www-data

WORKDIR /var/www/html

VOLUME /var/www/html/
EXPOSE 80

ENTRYPOINT ["/usr/sbin/apachectl"]
CMD ["-D", "FOREGROUND"]
~~~

* RUN
    * Comandos a serem executados no shell do Container no build
* LABEL
    * Qualquer descrição em Chave/Valor
* EXPOSE
    * Ao usar a flag -P, o Docker usa essa porta e faz um bind com uma  
    aleatória do Host.
* ENTRYPOINT
    * Processo principal do Container, o que vai ficar em execução
    * Diretório raiz
* CMD
    * Passa parâmetros ao ENTRYPOINT
        * FOREGROUND
            * Em primeiro plano, Container sempre me primero plano
    * Se não tiver o ENTRYPOINT, ele se torna o processo principal
        * `CMD /usr/sbin/apachectl -D FOREGROUND`
            * Caso ele fosse o principal, pode usar a sintaxe shell
* COPY
    * Copia um arquivo do Host para dentro do Container.
    * Poderia ser o ADD, tem a mesma funcionalidade com coisas a mais.
* USER
    * Define o usuário ao invés de usar o root
    * No caso do apache vai dar erro, pois tem que ser o root
* WORKDIR
    * Define o diretório padrão, ao entrar nesse Container vai cair nesse local 

~~~bash
docker image build -t [ nome:versão ] .
~~~

~~~bash
docker image build -t [ nome:versão ] . --no-cache
~~~

## Multistage

~~~Dockerfile
FROM golang AS buildando
WORKDIR /app
ADD . /app
RUN go build -o meugo

FROM alpine
WORKDIR /giropops
COPY --from=buildando /app/meugo /giropops/
ENTRYPOINT ./meugo
~~~

* Vai ter uma ação na parte 1
* O resultado vou usar na parte 2

Ao criar um Container da imagem alpine, e entrar nele, vai ser exibido o que  
foi colocado no aruivo do golang.

## Docker Compose

É uma forma de passar para um arquivo como suas imagens/stacks/ambiente de  
aplicação seja realizado o deploy.

~~~bash
docker stack deploy --compose-file 'docker-compose.yml' [ nome ]
docker service ls
docker stack ls
docker stack service ls [ nome ]
~~~

~~~yaml
version: '3.7'

services:
  web:
    image: nginx:1.23.2
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
      restart_policy:
        condition: on-failure
    ports:
      - "8080:80"
    networks:
      - webserver

networks:
  webserver
~~~

~~~yaml
version: '3.7

services:
  db:
    image: mysql:5.7
    volumes:
    - db_data:/var/lib/mysql
    enviroment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
    - db
    image: wordpress:latest
    ports:
    - '8000:80'
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress

volumes:
  db_data:
~~~

* Stack é um ou mais Services.
  * Um conjunto de aplicações (componentes) para que a minha aplicação funcione
* Services é um ou mais Containers.

* O nome do Service é o nome DNS para comunicação entre outros Containers.

* Todo Stack cria uma rede. Mas pode ser criada uma rede manualmente ( recomendado )

