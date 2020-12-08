FROM nginx:latest
COPY default.conf /etc/nginx/conf.d/default.conf

WORKDIR /app

RUN usermod -u 1001 nginx \
    && groupmod -g 1001 nginx \
    && touch /var/run/nginx.pid \
    && chown -R nginx: /var/cache/nginx \
    && chown nginx: /var/run/nginx.pid \
    && chown -R nginx: /etc/nginx

USER 1001
EXPOSE 8000