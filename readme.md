# Curso Full Cycle 3 - Docker

### Comandos

- Lista todos os container em execução.
```bash
docker ps
```

- Lista todos os containers que já foram executados, e neste momento estão parados.
```bash
docker ps -a
```

- Executa um container com a imagem `helo-world`
```bash
docker run hello-world
```

- Inicia a execução de um container existente na maquina.
```bash
docker start <id or name of container>
docker start 71b291fa5122
docker start quizzical_haibt
```

- Para a execução de um container existente na maquina.
```bash
docker stop <id or name of container>
docker stop 71b291fa5122
docker stop quizzical_haibt
```

- Executa um container com a imagem do Ubuntu, executando o processo do `bash` no container.
```bash
docker run -it ubuntu bash
```

- Parâmetro `-it` (interativo e tty) permite a entrada de comandos 
- Parâmetro `-i` modo interativo, mantem o STDIN ativo para permite a entrada de comando no terminal, atacha o seu terminal com bash do ubuntu
- Parâmetro `-t` modo tty, permite digitar comandos no terminal
- Para obter mais parâmetos que pode ser executado com `docker run ...` execute `docker run --help` no terminal.
```bash
docker run -it --rm ubuntu bash
```

- Roda um container com uma imagen do nginx e expoe a porta 8080 para poder acessar via browser (http://localhost:8080/).
- Desta forma deixa o console preso com a imagem do nginx em execução.
- Parâmetro `-p` (plublisher) permite expor uma porta interna do container para ser acessada pela máquina onde este esta rodando `-p <porta-exerna:porta-interna>`
```bash
docker run -p 8080:80 nginx
```

- Com o `-d` (detached), executa o container sem deixar o console preso.
```bash
docker run -d -p 8080:80 nginx
```

- Remove um container existente na maquina, para isso o container deve estar parado.
- Para forçar a remoção de um container em execução adicionar o parâmetro `-f`.
```bash
docker rm <id or name of container>
docker rm 71b291fa5122
docker rm quizzical_haibt
docker rm quizzical_haibt -f
```
