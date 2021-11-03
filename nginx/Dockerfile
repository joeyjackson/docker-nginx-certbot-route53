FROM nginx:alpine

# Setup reload cron
RUN echo "0       19      *       *       *       nginx -s reload" >> /etc/crontabs/root

CMD crond && \
  DOMAIN_ESCAPED=$(echo $DOMAIN | sed 's/\./\\\./g') envsubst '\$DOMAIN \$DOMAIN_ESCAPED' < /etc/nginx/templates/app.conf.tmpl > /etc/nginx/conf.d/app.conf && \ 
  nginx -g "daemon off;"