FROM caddy:2.7.5-alpine
WORKDIR /srv
COPY static-website-example /srv
COPY Caddyfile /etc/caddy/Caddyfile
EXPOSE 80
CMD ["sh", "-c", "caddy run --config /etc/caddy/Caddyfile --adapter caddyfile"]

