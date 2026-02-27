FROM alpine:3.22

RUN apk add --update nginx openssl && \
    mkdir -p /etc/nginx/ssl

COPY nginx.conf /etc/nginx/nginx.conf
COPY entrypoint.sh .

EXPOSE 443

ENTRYPOINT ["/bin/sh", "entrypoint.sh"]
