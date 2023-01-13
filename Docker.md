Por que são mais leves?
- São processos, e não uma virtualização

Como garantem o isolamento?
- Através de namespaces. Com a utilização de namespaces, os containers conseguem garantir isolamento em diversas camadas.
	- PID - Provê isolamento dos processos rodando dentro do container
	- NET - Provê isolamento das interfaces de rede
	- IPC - Provê isolamento da comunicação entre processos e memória compartilhada
	- MNT - Provê isolamento do sistema de arquivos / Pontos de montagem
	- UTS - Provê isolamento do kernel. Age como se o container fosse outro host

Como funcionam sem "Instalar um SO"?
Como fica a divisão de recursos do sistema?

Benefício da utilização de containers em diferentes aplicações.

Muitos sistemas atualmente são compostos por diversas aplicações executadas simultaneamente. Diversos problemas podem ocorrer quando muitas aplicações precisam se comunicar. Porém, antes de pensarmos em comunicação de aplicações, é preciso visualizar outras questões importantes para nosso trabalho, como isolamento de contextos e versionamento de aplicações.


Preparando o Ambiente

Windows
https://docs.docker.com/desktop/install/windows-install/
Verificando se o Docker está funcionando
>> docker run hello-world
Linux
>> 
```
sudo apt-get update
```

>>
```
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

>>
```
sudo mkdir -p /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

>>
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

>> 
```
 sudo apt-get update
```

>>
```
sudo chmod a+r /etc/apt/keyrings/docker.gpg
$ sudo apt-get update
```

>>
Após instalar, lembre-se de executar o comando 
```
docker run 
```
e reiniciar sua sessão para utilizar o Docker sem precisar usar o sudo.

O comando docker run é responsável por executar um container em nosso host. Através deste comando, o docker irá executar o container da maneira esperada.

Local das imagens do Docker
https://hub.docker.com/_/hello-world


- Máquinas virtuais possuem camadas adicionais de virtualização em relação a um container;
- Containers funcionam como processos no host;
- Containers atingem isolamento através de namespaces;
- Os recursos dos containers são gerenciados através de cgroups.

Mostra os containers em funcionamento
```
docker ps 
ou
docker container ls
```

Mostra todos os containers
```
docker ps -a 
ou 
docker container ls -a
```

Executa um processo dentro do Container para deixa-lo em pé
```
docker run ubuntu sleep 1d 
```
Neste caso, melhor usar o 
```
docker run -it ubuntu bash
```
Permite iteragir com ubuntu

Fluxo Docker
Quando queremos executar um container e usamos o comando docker run, ocorre uma série de etapas ordenadas até que a execução seja feita efetivamente.
Procura a imagem localmente -> Baixa a imagem caso não encontre localmente -> Valida o hash da imagem -> Executa o container.
Este é o fluxo adotado pelo docker para execução de um container.

Stop na execução do Container - Normal
```
docker stop 1d475db87a99
```

Stop Rápido na execução do Container
```
docker stop -t=0 1d475db87a99
```

Inicia a execução do Container
```
docker start 1d475db87a99
```

Acessa Container
```
docker exec -it 1d475db87a99 bash
```
O docker run cria um novo container e o executa. O docker exec permite executar um comando em um container que já está em execução.

Pausa Container
```
docker pause 1d475db87a99 bash
```

DesPausa Container
```
docker unpause 1d475db87a99 bash
```

Remove Container
```
docker rm 1d475db87a99 bash
```

Para ver o Docker em funcionamento, podemos exportar do Docker Hub o dockersamples/static-site. Não é oficial do Docker mas serve para testar 

Stop e Remove container
```
docker rm bce9d33997d8 --force
```


Mapeia Portas
```
docker run -d -P dockersamples/static-site
```

Mostra mapeamento de portas do container específico em relação ao Host
```
 docker port 6530c16ce5df
```


para acessar:
http://localhost:49154/

Mapeamento de portas do container específico em relação ao Host para 8080
```
 docker run -d -p 8080:80 dockersamples/static-site
```
para acessar:
http://localhost:8080/

As flags -p e -P são úteis quando queremos fazer o mapeamento de portas entre nosso container e o nosso host. Existe um comando responsável pela visualização de como o mapeamento de portas de um container está sendo feito.
```
 docker port
```
Este comando é responsável por exibir como o mapeamento de portas de um container está sendo feito.

- O Docker Hub é um grande repositório de imagens que podemos utilizar;
- A base dos containers são as imagens;
- Como utilizar comandos acerca do ciclo de vida dos containers, como: docker start, para iniciar um container que esteja parado; docker stop, para parar um que esteja rodando; docker pause, para pausar um container e docker unpause para iniciar um container pausado; -Conseguimos mapear portas de um container com as flags -p e -P.

Lista imagens
```
docker images
ou 
docker image ls
```

Detalhamento da imagem
```
docker inspect f589ccde7957
```

Lista camadas do container
```
docker history f589ccde7957
```

Imagens são compostas por uma ou mais camadas
As camadas são a menor unidade que compõem uma imagem.

Podemos visualizar as camadas de uma imagem através do comando docker history.
Este comando é responsável por exibir quais são as camadas de uma imagem.


Docker File

https://docs.docker.com/engine/reference/builder/


```
//SEM WORKDIR
FROM node:14 
EXPOSE 3000 
COPY . /app-node 
RUN npm install /app-node
ENTRYPOINT npm start

//COM WORKDIR
FROM node:14 
WORKDIR /app-node
EXPOSE 3000 
COPY . .
RUN npm install
ENTRYPOINT npm start

//https://hub.docker.com/_/node - Imagem Base

FROM node:14 //A imagem do node na versão 14 será a base da nossa imagem
WORKDIR /app-node //Define diretório de trabalho padrão
EXPOSE 3000 //Expõe a porta na chamada do docker ps
COPY . . //Copia diretório atual para  a imagem do container
RUN npm install /app-node //Instala dependências do node
ENTRYPOINT npm start

##Final
FROM node:14  
WORKDIR /app-node 
ARG PORT_BUILD=6000 //Usado para construção da imagem 
ENV PORT=$PORT_BUILD //Define variáveis de ambiente para dentro do Container 
EXPOSE $PORT_BUILD //Expor porta aos usuários 
COPY . . 
RUN npm install 
ENTRYPOINT npm start
```
A instrução FROM é usada para definirmos uma imagem como base para a nossa.
Desta maneira, podemos adicionar à nossa imagem conteúdos que utilizaremos de maneira mais prática.

A instrução ARG carrega variáveis apenas no momento de build da imagem, enquanto a instrução ENV carrega variáveis que serão utilizadas no container.
Desta maneira, é possível diferenciar o que é necessário para a etapa de build e para a etapa de execução.


Executa o dockerfile e criar a imagem
```
docker build -t dglz/app-node:1.0 .
```

Roda o container na porta 8081 com a imagem criada
```
docker run -dp 8081:3000 dglz/app-nod e:1.0
```

Stop em todos container de uma vez
```
docker stop $(docker container ls -q)
```

Exclui todos os containers 
```
docker container rm $(docker container ls -aq) 
```

Exclui todas as imagens sem referência em multiplos repoitórios
```
docker rmi $(docker image ls -aq) 
```

Exclui todas as imagens - Forçado
```
docker rmi $(docker image ls -aq) --force
```

Sobe imagem para o Docker Hub do Usuário dglandre
```
docker login -u dglandre
docker tag dglz/app-node:1.3 dglandre/app-node:1.3
docker push dglandre/app-node:1.3
```

- Imagens são imutáveis, ou seja, depois de baixadas, múltiplos containers conseguirão reutilizar a mesma imagem;
- Imagens são compostas por uma ou mais camadas. Dessa forma, diferentes imagens são capazes de reutilizar uma ou mais camadas em comum entre si;
- Podemos criar nossas imagens através de Dockerfiles e do comando docker build;
- Para subir uma imagem no Docker Hub, utilizamos o comando docker push.

Mostra tamanho do container - SIZE
```
docker ps -s
```

Podemos querer que os dados da nossa aplicação sejam persistentes, porque assim garantimos que ela esteja distribuída e disponível se precisarmos consultá-la. Porém, se escrevermos os dados nos containers, por padrão eles não ficarão armazenados nesta camada, criada para ser descartável. Existem três possibilidades para contornar esta situação com o Docker.

Selecione as alternativas com meios para persistir dados importantes.

Volumes.

Com volumes, é possível escrever os dados em uma camada persistente.



Bind mounts.
Com bind mounts, é possível escrever os dados em uma camada persistente baseado na estrutura de pastas do host.



Persistência - Bind Mounths - https://docs.docker.com/storage/

```
docker run -it -v C:\Docker\volume-docker:/app ubuntu bash
```
Persiste camada write no diretório volume-docker


Persistência - Bind Mounths - https://docs.docker.com/storage/

```
docker run -it --mount type=bind,source=C:\Docker\volume-docker,target=/app ubuntu bash
```
