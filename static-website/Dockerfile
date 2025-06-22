FROM busybox AS website-builder
FROM alpine/git AS site-source
RUN git clone https://github.com/cloudacademy/static-website-example.git /app/static-website
FROM caddy:2.7.5-alpine
WORKDIR /srv
COPY --from=site-source /app/static-website /srv
COPY Caddyfile /etc/caddy/Caddyfile


EXPOSE 80

CMD ["sh", "-c", "caddy run --config /etc/caddy/Caddyfile --adapter caddyfile"]

