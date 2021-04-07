# Docker and Docker-compose with Nginx, MySQL, NodeJS, Laravel

#DOCKER COMANDOS

1. Visualizar Containers ativos
docker ps / docker ps -a

2. Buscando imagens no repositório
docker run nome-imagem: docker run hello-world

3. Executar uma imagem preder o bash e posibilitar escrever/comandos. Caso não ache a imagen irá baixar. Ctrl + D para sair do container
 docker run -it ubuntu bash or docker run -it --rm ubuntu bash - --rm remove o container toda vez que é executado

4. Docker start iniciar container parado
docker start id-container
4.2 Docker stop parar container
docker stop id-container

5. Passando parametros, como --name para nome, -p para publicar porta port-localhost:port-container, -d para não trava o terminal
docker run --name my-nginx -p 8080:80 -d nginx

6. Entrando no container
docker exec -it tipo-comando: docker exec -it nginx bash

7. Trabahando com bind mouths - Enviando arquivos da minha máquina para o container
Forma antiga:
docker run -d --name nginx -p 8080:80 -v /home/skopo/projects/docker:/usr/share/nginx/html nginx
Forma nova:
docker run -d --name nginx -p 8080:80 --mount type=bind,source="$(pwd)",target=/usr/share/nginx/html nginx

8. Comandos para ver detalhes de volumes
docker volume 

9. criação de volumes
docker volume create meuvolume

10. Após criar um volume, podemos mapea-lo para dentro de um ou mais containers 
docker run -d --name nginx2 -p 8081:80 --mount type=volume,source=meuvolume,target=/app nginx

11. Consultando imagens
docker imagens

12. Apagando imagens
docker rmi id-imagem ou name:tag

13. Criando imagens no Docker. Primeiro configurar o Dockerfile começando com FROM e demais comandos. O ponto no
final "." informa onde está o arquivo dockerfile. Nesse caso na raiz
informando usuário do docker hub registry, nome imagem e tag: docker build -t wesleymassine/nginx-with-vim:latest .

14. Podemos entrar na imagens após o build
docker run -it wesleymassine/nginx-with-vim bash

15. Após build/geração de imagens docker, podemos fazer pull para o nosso docker registry - docker.hub
docker pull login-hub/nome-imagem:tag : docker push wesleymassine/nginx-with-vim:latest

16. Ver opções de rede/network
docker network

17. Visualizar os containers inspeção por tipo
network inspect tipo-network: network inspect bridge

18. Entrar no bash do container via name
docker attach name-container: docker attach ubuntu1

19. Criando uma rede
docker network create --driver bridge myrede 

20. Colando o container na rede criada
docker run -dit --name ubuntu1 --network myrede bash e docker run -dit --name ubuntu2 --network myrede bash
Podemos entrar em um dos containers e realizar ping para ver se está na mesma rede
Ex: ping ubuntu2

21. Posso contectar outros containers com uma rede já crida
docker network connect myrede ubuntu3
E verificar se todos estão na mesma rede
docker network inspect myrede

22. Exemplo de conexão na network tipo host
docker run --rm -d --name nginxrede --network host nginx

23. Container acessando uma aplicação em nossa máquina
Exemplo de aplicação rodando na máquina em http://localhost:8081/

Comando Docker: http://host.docker.internal:8081

#Dicas docker
1. Lista todos os ID's de containes ativos e inativos
docker ps -a -q
2. Apangando em lote containes via ID's 
docker rm $(docker ps -a -q) -f
3. Substituindo comandos no CMD
docker run --rm wesleymassine/nome-imagem echo "substituir o que tem dentro do CMD"
EX: CMD ["Echo", "Word"]
Podemos utilizar o entrypint, pois esse é fixo:

ENTRYPOINT ["Echo", "Hello"]
CMD["World"]

Ao executar: docker run --rm wesleymassine/nome-imagem Comando-Aqui
Saída: Hello Comando-Aqui 
4. Ver IP da máquina
ip addr show

# Laravel
- docker run -it --name php php:7.4-cli bash

- docker run --rm --name laravel-app -d -p 8000:8000 wesleymassine/laravel

- docker logs

- docker build -t wesleymassine/laravel:prod . -f Dockerfile.prod
- docker images | grep laravel


# Node
- Compartilhando o volume para a maquina e vice-versa. Assim escuta e pegar as alteraçẽos

1. docker run --rm -it -v $(pwd)/:/usr/src/app -p 3000:3000 node:15 bash
cd usr/src/app

2. Depois ao configurar um arquivo dockerfile podemos gerar a imagem da aplicação
docker build -t wesleymassine/node-express .

3. Rodando
docker run --name node-app -p 3000:3000 -d node-express:latest

4. buildando a iamgem informando o dockerfile
docker build -t wesleymassine/node-express . -f Dockerfile.prod

# Trabalhando com docker-compose 

1. levando os serviços
docker-compose up

2. para os serviços
docker-compose down

3. Lista serviços
docker-compose ps

4. Rebuide as imagens quando realiazado alterações no dockerfile
docker-compose up -d --build

5. Parando apenas um serviço do docker-compose
docker-compose stop db

# Utilizando o dockerize
https://github.com/jwilder/dockerize
Instale em "Ubuntu Images"

1. Testando dependências entre containers
MySQL: dockerize -wait tcp://db:3306
passando timeout:  dockerize -wait tcp://db:3306 -timeout 50s