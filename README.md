Criar rede

```
docker network create my-network
```

Iniciar containers usando comando `docker compose`

```
docker compose up
#ou
docker compose up -d
```

Configurar o ngrok

```
ngrok http --domain={ngrok_domain} 443
```

Criar arquivo de configuração do ngrok, para que o mesmo seja iniciado junto com Linux

```yaml
version: 2
authtoken: { token }
tunnels:
  your_tunnel_name:
    proto: http
    hostname: { ngrok_domain }
    addr: 127.0.0.1:443
```

Instalando o ngrok como um serviço do Linux

```
sudo ngrok service install --config ngrok/ngrok.yaml
```

Finalizar containers usando comando `docker compose`

```
docker compose down
#ou
docker compose down --volumes
```

Atualizar nginx, caso o arquivo seja alterado, executar o comando

```
docker exec -it nginx bash
apk update && apk add --no-cache nano
nano /etc/nginx/conf.d/nginx.conf
nginx -t && nginx -s reload
#ou
docker exec -it nginx /bin/sh -c "nginx -t && nginx -s reload"
```