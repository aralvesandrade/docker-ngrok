FROM nginx:1.21.3-alpine

RUN apk update && \
    apk add --no-cache openssl bash && \
    openssl req -x509 -nodes -days 365 -newkey rsa:4096 \
    -subj "/C=BR/ST=Sao Paulo/L=Franca/O=github-aralvesandrade/OU=dev/CN=aralvesandrade" \
    -keyout /etc/ssl/private/selfsigned.key \
    -out /etc/ssl/certs/selfsigned.crt