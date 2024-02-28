Criar volumes

```
docker volume create postgres
docker volume create postgres_data
docker volume create sonarqube_data
docker volume create sonarqube_extensions
docker volume create sonarqube_logs
docker volume create mysql_data
```

Criar rede

```
docker network create minha-rede
```

Iniciar containers

```
docker run -d --name postgres --network minha-rede -e POSTGRES_DB=sonar -e POSTGRES_USER=sonar -e POSTGRES_PASSWORD=sonar -e TZ=America/Sao_Paulo -p 5432:5432 --restart=always -v postgres:/var/lib/postgresql -v postgres_data:/var/lib/postgresql/data postgres:16.0

docker run -d --name sonarqube --network minha-rede -p 9000:9000 -e SONAR_JDBC_USERNAME=sonar -e SONAR_JDBC_PASSWORD=sonar -e SONAR_JDBC_URL=jdbc:postgresql://postgres:5432/sonar -e SONAR_SEARCH_JAVAADDITIONALOPTS=-Dnode.store.allow_mmap=false,-Ddiscovery.type=single-node -e TZ=America/Sao_Paulo --restart=always -v sonarqube_data:/opt/sonarqube/data -v sonarqube_extensions:/opt/sonarqube/extensions -v sonarqube_logs:/opt/sonarqube/logs sonarqube:9.9.4-community

docker run -d --name mysql --network minha-rede -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=test -e MYSQL_USER=test -e MYSQL_PASSWORD=test -e TZ=America/Sao_Paulo -p 3306:3306 --restart=always -v mysql_data:/var/lib/mysql mysql:8.0

docker run -d --name grafana --network minha-rede -p 3000:3000 --restart=always grafana/grafana-oss

docker run -d --name nginx --network minha-rede -e TZ=America/Sao_Paulo -p 8080:80 --restart=always -v /nginxconf/nginx.conf:/etc/nginx/conf.d nginx

docker run -d --name nginx --network minha-rede -p 8080:80 --restart=always -v /home/alexandre/nginxcfg:/etc/nginx/conf.d nginx
```

Na pasta /home/alexandre/nginxcfg está o arquivo nginx.conf

Atualizar nginx, caso o arquivo seja alterado, executar o comando

```
docker exec -it nginx bash
nginx -t && nginx -s reload
#ou
docker exec -it nginx /bin/sh -c "nginx -t && nginx -s reload"
```

Acessar determinado container

```
docker exec -it postgres bash
docker exec -it sonarqube bash
docker exec -it mysql bash
docker exec -it nginx bash
```

Exemplo de como executar algumas comandos dentro do container mysql

```
mysqladmin ping -h localhost -u root -proot
mysql -h localhost -u root -proot
show databases;
use test;
create table people (id int auto_increment primary key not null, name varchar(100) not null, dta_created datetime not null default now());
insert into people (name) values ('Alexandre Andrade');
show tables;
select * from people;
```

Configura o ngrok no domain criado no dash, exportando a porta 9000, que está configurado para sonarqube

```
ngrok http --domain={ngrok_domain} 9000
```

Criar arquivo de configuração do ngrok, para que o mesmo seja iniciado junto com Linux

```yaml
version: 2
authtoken: { token }
tunnels:
  your_tunnel_name:
    proto: http
    hostname: { ngrok_domain }
    addr: 127.0.0.1:9000
```

Instalando o ngrok como um serviço do Linux

```
sudo ngrok service install --config ngrok/ngrok.yaml
```

Cenário mais simples, postado no fórum do FullCycle

```
docker network create minha-rede

docker run -d --name sonarqube --network minha-rede -p 9000:9000 sonarqube

docker run -d --name grafana --network minha-rede -p 3000:3000 grafana/grafana-oss

docker run -d --name nginx --network minha-rede -p 8080:80 -v /home/alexandre/nginxcfg:/etc/nginx/conf.d nginx
```
