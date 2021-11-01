FROM certbot/dns-route53
ENTRYPOINT []
VOLUME ["/etc/letsencrypt"]
ADD [ \
  "https://raw.githubusercontent.com/certbot/certbot/master/certbot-nginx/certbot_nginx/_internal/tls_configs/options-ssl-nginx.conf", \
  "/etc/letsencrypt/options-ssl-nginx.conf" \
]
ADD [ \
  "https://raw.githubusercontent.com/certbot/certbot/master/certbot/certbot/ssl-dhparams.pem", \
  "/etc/letsencrypt/ssl-dhparams.pem" \
]

# Setup renew cron
RUN echo "0 */12 * * * certbot renew" | crontab -

CMD certbot certonly --non-interactive --agree-tos --email ${CONTACT_EMAIL} \
  --keep-until-expiring \
  --dns-route53 \
  -d ${DOMAIN} \
  -d *.${DOMAIN} \
  # Sleep to keep conatiner running for renew cron
  && sleep infinity \
]
