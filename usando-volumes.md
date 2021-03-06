### Volumes

> Volumes são usados para armazenar dados de forma que estes fiquem disponíveis para serem usados em outros containers ou compartilhados com o host.
Os dados não ficam limitados ao container atual.

> Se rodarmos um container como root, os arquivos criados em volumes usadados por esse container também pertencerão ao usuário root mesmo depois que o container estiver encerrado. Para evitar esse problema devemos executar o container como um usuário não-root.


##### Criando volumes

`docker volume create meu_volume`

##### Listando todos os volumes

`docker volume list` ou `docker volume ls`

##### Inspecionando o volume

`docker volume inspect meu_volume`

##### Montando um volume dentro de um container

> Será necessário usar o parâmetro `-v` com o comando `docker run`.

`docker container run -it -v meu_volume:/local_de_montagem ubuntu bash`

##### Removendo um volume

> O volume não pode estar em uso por um container para ser removido.

`docker volume rm meu_volume`

##### Removendo todos os volumes que não estão sendo usados por pelo menos um container

`docker volume prune`

##### Exemplo de criação e uso de volumes:

* Crie um volume: `docker volume create meu_volume`;
* Verifique se o volume foi criado: `docker volume list`;
* Crie um container montando nele o nosso volume criado: `docker run -it -v meu_volume:/dados_salvos alpine sh`;
* Crie alguns arquivos dentro desse volume: `cd /dados_salvos && touch arquivo1 arquivo2 && exit`;
* Agora crie um novo container e monte nosso volume: `docker run -it -v meu_volume:/dados_salvos busybox sh`;
* Então verifique que os arquivos criados no outro container permanecem no volume: `ls /dados_salvos`.  

> Volumes são muito úteis quando precisamos compartilhar arquivos entre vários containers. Como exemplo podemos citar os arquivos de um banco de dados que precisem ser acessado por diversos containers.


* Pode ser usado a diretiva `VOLUME` para adicionar um volume pelo `Dockerfile`.
* Os dados dos volumes geralmente ficam gravados no host no diretório `/var/lib/docker/volumes/`.

```sh
FROM ubuntu:16.04
MAINTAINER Fulano de Tal <fulano@email.com>
RUN apt update && apt install nginx -y
RUN cd ~ && mkdir novo_diretorio
RUN rm -rf novo_diretorio
COPY ola.txt /tmp/ola.txt
VOLUME /meu_volume
CMD bash
```


##### Permissões

> Proceda da seguinte forma para definir as permissões para o nosso volume em `Dockerfile`:

```sh
FROM ubuntu:16.04
MAINTAINER Fulano de Tal <fulano@email.com>
RUN useradd usuario
RUN mkdir /novo_volume && touch /novo_volume/teste.txt
RUN chown -R usuario:usuario /novo_volume
VOLUME /novo_volume
CMD bash
```

* O segredo aqui é adicionar o volume depois das permissões.
