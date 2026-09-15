# ACME-Zertifikat mit certbot und External Account Binding (EAB)

Für ACME-Anbieter, die eine Kontobindung per EAB verlangen (z. B. kommerzielle CAs). `KEY_ID`, `HMAC_KEY` und `SERVER_URL` stellt der Zertifikatsanbieter bereit.

```bash
apt install certbot python3-certbot-apache
certbot --apache \
    --agree-tos \
    --email <admin@example.com> \
    --eab-kid <KEY_ID> \
    --eab-hmac-key <HMAC_KEY> \
    --server <SERVER_URL> \
    --domain <DOMAIN>
```
