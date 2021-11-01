FROM nginx:alpine

# Setup reload cron
RUN echo "0       19      *       *       *       nginx -s reload" >> /etc/crontabs/root

CMD crond && nginx -g "daemon off;"