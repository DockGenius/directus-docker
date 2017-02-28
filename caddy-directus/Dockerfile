FROM alpine:3.4

RUN addgroup -g 82 www-data && adduser -S -u 82 -G www-data www-data
RUN mkdir -p /opt/caddy

RUN apk --update --no-cache add ca-certificates openssl && \
								update-ca-certificates

RUN wget https://github.com/mholt/caddy/releases/download/v0.9.4/caddy_linux_amd64.tar.gz && \
    echo "f16b17ffe92b0734ebb86ee30d36439207117277  caddy_linux_amd64.tar.gz" | sha1sum -c - && mkdir -p /opt/caddy && tar -xf caddy_linux_amd64.tar.gz -C /opt/caddy && \
    rm caddy_linux_amd64.tar.gz
COPY Caddyfile /etc/caddy/Caddyfile

RUN touch /var/log/caddy.log && chown www-data.www-data /var/log/caddy.log

CMD /opt/caddy/caddy_linux_amd64 -conf="/etc/caddy/Caddyfile"
