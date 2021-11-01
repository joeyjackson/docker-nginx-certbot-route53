FROM certbot/dns-route53
ENTRYPOINT []
ADD [ \
  "https://raw.githubusercontent.com/certbot/certbot/master/certbot-nginx/certbot_nginx/_internal/tls_configs/options-ssl-nginx.conf", \
  "/etc/letsencrypt/options-ssl-nginx.conf" \
]
ADD [ \
  "https://raw.githubusercontent.com/certbot/certbot/master/certbot/certbot/ssl-dhparams.pem", \
  "/etc/letsencrypt/ssl-dhparams.pem" \
]

# Setup renew cron
ADD certbot-renew-cron /etc/periodic/12h/certbot-renew-cron
RUN chmod 700 /etc/periodic/12h/certbot-renew-cron
RUN echo "0       6,18    *       *       *       run-parts /etc/periodic/12h" >> /etc/crontabs/root

CMD certbot certonly --non-interactive --agree-tos --email ${CONTACT_EMAIL} \
  --keep-until-expiring \
  --dns-route53 \
  -d ${DOMAIN} \
  -d *.${DOMAIN} \
  # Run cron for renew
  && crond -f -l 8 \
]
