FROM nginx:latest

RUN rm -f /etc/nginx/conf.d/*
COPY supervisord.conf /etc/supervisor/conf.d/

VOLUME ["/var/www", "/etc/nginx/conf.d"]
EXPOSE 80
