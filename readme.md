# Curso Full Cycle 3 - Docker

## Comandos


- Lista todos os container em execução.
```bash
❯ docker ps
```

- Lista todos os containers que já foram executados, e neste momento estão parados.
```bash
❯ docker ps -a
```

- Executa um container com a imagem `helo-world`
```bash
❯ docker run hello-world
```

- Inicia a execução de um container existente na maquina
- Usando o comando: `docker start [id or name of container]`
```bash
❯ docker start 71b291fa5122
❯ docker start quizzical_haibt
```

- Para a execução de um container existente na maquina.
- Usando o comando: `docker stop [id or name of container]`
```bash
❯ docker stop 71b291fa5122
❯ docker stop quizzical_haibt
```

- Executa um container com a imagem do Ubuntu, executando o processo do `bash` no container.
```bash
❯ docker run -it ubuntu bash
```

- Parâmetro `-it` (interativo e tty) permite a entrada de comandos 
- Parâmetro `-i` modo interativo, mantem o STDIN ativo para permite a entrada de comando no terminal, atacha o seu terminal com bash do ubuntu
- Parâmetro `-t` modo tty, permite digitar comandos no terminal
- Para obter mais parâmetos que pode ser executado com `docker run ...` execute `docker run --help` no terminal.
```bash
❯ docker run -it --rm ubuntu bash
```

- Roda um container com uma imagen do nginx e expoe a porta 8080 para poder acessar via browser (http://localhost:8080/).
- Desta forma deixa o console preso com a imagem do nginx em execução.
- Parâmetro `-p` (plublisher) permite expor uma porta interna do container para ser acessada pela máquina onde este esta rodando `-p [external-port:internal-port]`
```bash
❯ docker run -p 8080:80 nginx
```

- Com o `-d` (detached), executa o container sem deixar o console preso.
```bash
❯ docker run -d -p 8080:80 nginx
```

- Remove um container existente na maquina, para isso o container deve estar parado.
- Para forçar a remoção de um container em execução adicionar o parâmetro `-f`.
- Usando o comando: `docker rm [id or name of container]`
```bash
❯ docker rm 71b291fa5122
❯ docker rm quizzical_haibt
❯ docker rm quizzical_haibt -f
```

- Parâmetro `--name [my-container]` define um nome para o container
```bash
❯ docker run -d --name my-nginx nginx
f79dab8e4edef32ecb50a13fbe85c2c69c5b0684f2eb2a98e95536fc9893a6be
❯ docker ps   
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
f79dab8e4ede   nginx     "/docker-entrypoint.…"   11 seconds ago   Up 10 seconds   80/tcp    my-nginx
```

- Comando `docker exec [container-name] [comands]` executa comandos dentro de um container em execuçã
```bash
# Executa um container com a imagem no NGinx, com o nome my-nginx desatachado do terminal com a porta 8080 exposta
❯ docker run -d --name my-nginx -p 8080:80 nginx
a6b2b434e517cc470b8c1ed5b27a0afa86523b8a81dd2f09e400824fdb104dc7
# Executa o comando ls no container do nginx
❯ docker exec my-nginx ls                          
bin
boot
dev
...
# Executa o comando bash no container do nginx, atachando o terminal da maquina no bash do container
❯ docker exec -it my-nginx bash
root@a6b2b434e517:/# 
```

### Alterando arquivos dentro do container

- Exemplo de como alterar arquivos dentro do container
- Todas as alterações realizadas ficam armezanadas na camada de cache de escrita do container
- Quando para e iniciar o container novamente todas as alterações serão perdidas.
```bash
# Acessar o bash do container do nginx
❯ docker exec -it my-nginx bash
root@a6b2b434e517:/# 
# Primeira instalar o vim para editar o arquivo html  
root@a6b2b434e517:/# apt-get update
root@a6b2b434e517:/# apt-get install vim
root@a6b2b434e517:/# cd /usr/share/nginx/html/
root@a6b2b434e517:/usr/share/nginx/html# ls
50x.html  index.html
root@a6b2b434e517:/usr/share/nginx/html# vim index.html 
```

### Bind Mounts
- Mapeamento de um diretório local com o container

- Parâmetro `-v [local-directory:container-directory]` realiza o mapeamento de entre a maquina e o container.
- Parâmetro `-v ...` é o volume, ou seja, o docker o mapeamento de um volume da maquina com o container.
- Ao usar este parâmetro para realizar o mapeamento de um diretório que não existe na maquina local, o parâmetro `-v ...` irá criar esse diretorio na maquina local.
```bash
# Executa um container com a imagem no NGinx, e fazendo o mapeamento do diretório
❯ docker run -d --name my-nginx -p 8080:80 -v ~/dev/projects/docker/fullcycle3-docker/html/:/usr/share/nginx/html/ nginx

# Mapeando um volume que não existe na maquina local
❯ docker run -d --name my-nginx -p 8080:80 -v ~/dev/projects/docker/fullcycle3-docker/html/x:/usr/share/nginx/html/ nginx
```

- Parâmetro `--mount ...` é mais explicito e mais utilizado para o mapeamento de diretórios entre a maquina local e o container
- Parâmento `--mount type=bind,source=[origin-directory],target=[destination-directory]`, o `type=bind` corresponde em mapear um diretório local
- Ao usar este parâmetro para realizar o mapeamento de um diretório que não existe na maquina local, um erro é exibido no console.
```bash
# Executa um container com a imagem no NGinx, e fazendo o mapeamento do diretório
❯ docker run -d --name my-nginx -p 8080:80 --mount type=bind,source="$(pwd)"/html/,target=/usr/share/nginx/html/ nginx

# Mapeando um volume que não existe na maquina local
❯ docker run -d --name my-nginx -p 8080:80 --mount type=bind,source="$(pwd)"/html/x,target=/usr/share/nginx/html/ nginx   
docker: Error response from daemon: invalid mount config for type "bind": bind source path does not exist: /host_mnt/home/leticia/dev/projects/docker/fullcycle3-docker/html/x.
See 'docker run --help'.
```

### Volumes
- Docker permite criar volumes para serem utilizados no container
- Esses volumes são utilizados para armazenar arquivos e dados que serão utilizados pelo container. - Podemos mapear um volume em diversos containers, compartilhar volumes
- Utilizado quando não temos controle sobre os diretórios da maquina local
- Podemos realizar backup desse volumes

```bash
# Criando um volume
❯ docker volume create my-volume
my-volume
# Listando os volumes existentes
❯ docker volume ls              
DRIVER    VOLUME NAME
local     my-volume
```

- Exemplo: compartilhar o mesmo volume em vários containers usando o parâmentos `--mount` e `-v`.
- Criar o container 1, e dentro desde container criar o arquivo hello na pasta app
- Criar o container 2, e este deve conter o aquivo hello na pasta app, pois esta utilizando o mesmo volume do container 1
- Criar um container


- Parâmento `--mount type=volume,source=[origin-directory],target=[destination-directory]`, o `type=volume` corresponde em mapear um volume criado no docker

```bash
# Container 1 mapeando o volume my-volume
❯ docker run -d --name my-nginx1 --mount type=volume,source=my-volume,target=/app nginx                                 
6a31e1bf2043cc04d59485c481d7d06bcb16553cdcc85f8a149482dcc4bd4205
❯ docker exec -it my-nginx1 bash                                                       
root@6a31e1bf2043:/# ls -la /app/
total 8
drwxr-xr-x 2 root root 4096 Apr 18 14:18 .
drwxr-xr-x 1 root root 4096 Apr 18 14:20 ..
root@6a31e1bf2043:/# touch /app/hello
root@6a31e1bf2043:/# ls -la /app/
total 8
drwxr-xr-x 2 root root 4096 Apr 18 14:18 .
drwxr-xr-x 1 root root 4096 Apr 18 14:17 ..
-rw-r--r-- 1 root root    0 Apr 18 14:18 hello
root@6a31e1bf2043:/# 


# Container 2 mapeando o volume my-volume
❯ docker run -d --name my-nginx2 --mount type=volume,source=my-volume,target=/app nginx
451c4ab40bbbeab14efee313d60fc2f1ac9922d4b5f5b46402cc72380e414428
❯ docker exec -it my-nginx2 bash                                                       
root@451c4ab40bbb:/# ls -la /app/
total 8
drwxr-xr-x 2 root root 4096 Apr 18 14:18 .
drwxr-xr-x 1 root root 4096 Apr 18 14:18 ..
-rw-r--r-- 1 root root    0 Apr 18 14:18 hello
root@451c4ab40bbb:/# 

# Container 3 mapeando o volume my-volume
❯ docker run -d --name my-nginx3 -v my-volume:/app nginx                                                
a21a2a05960dd5725ad9002eb037052435563a0e21e1e6bf8d23e8491749fb45
❯ docker exec -it my-nginx3 bash                        
root@a21a2a05960d:/# ls -la /app/
total 8
drwxr-xr-x 2 root root 4096 Apr 18 14:29 .
drwxr-xr-x 1 root root 4096 Apr 18 14:28 ..
-rw-r--r-- 1 root root    0 Apr 18 14:29 hello
root@a21a2a05960d:/# 
```

- Para remover os volume
```bash
❯ docker volume ls                   
DRIVER    VOLUME NAME
local     my-volume

# Remove todos os volumes
❯ docker volume prune

❯ docker volume ls                   
DRIVER    VOLUME NAME
```
