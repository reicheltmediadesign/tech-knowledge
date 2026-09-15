# 📋 Nextcloud Installation - Step by Step Guide

## Server-Informationen
- **Domain:** `nextcloud.example.com`
- **Datenbank-User:** `dbnextcloud`
- **Datenbank-Passwort:** `%%%PW%%%` (vor der Installation einsetzen!)
- **Admin-User:** `admin`
- **Admin-Passwort:** `%%%ADMIN_PW%%%` (vor der Installation einsetzen!)

Alle Vorkommen von `nextcloud.example.com` und `admin@example.com` durch die eigenen Werte ersetzen.

---

## ✅ Voraussetzungen

- Ubuntu 24.04 LTS Server
- SSH-Zugriff als root oder mit sudo
- Domain zeigt auf VPS-IP
- Port 80 und 443 sind freigegeben

---

# 🚀 INSTALLATION

## SCHRITT 1: System aktualisieren

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y curl wget gnupg2 ca-certificates apt-transport-https
```

---

## SCHRITT 2: Apache, MariaDB und PHP installieren

```bash
sudo apt install -y \
    apache2 \
    mariadb-server \
    libapache2-mod-php \
    php-gd \
    php-mysql \
    php-curl \
    php-mbstring \
    php-intl \
    php-gmp \
    php-xml \
    php-imagick \
    php-zip \
    php-opcache \
    php-apcu \
    bzip2 \
    unzip
```

---

## SCHRITT 3: Apache-Module aktivieren

```bash
sudo a2enmod rewrite
sudo a2enmod headers
sudo a2enmod env
sudo a2enmod dir
sudo a2enmod mime
sudo a2enmod setenvif
sudo a2enmod ssl
sudo systemctl restart apache2
```

---

## SCHRITT 4: MariaDB-Datenbank einrichten

```bash
sudo mysql << 'EOF'
-- Neuen Datenbank-User erstellen
CREATE USER 'dbnextcloud'@'localhost' IDENTIFIED BY '%%%PW%%%';

-- Datenbank erstellen
CREATE DATABASE IF NOT EXISTS nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

-- Alle Privileges geben
GRANT ALL PRIVILEGES ON nextcloud.* TO 'dbnextcloud'@'localhost';

-- Änderungen speichern
FLUSH PRIVILEGES;

-- Bestätigung
SELECT user FROM mysql.user WHERE user='dbnextcloud';

QUIT;
EOF
```

**Hinweis:** `%%%PW%%%` durch ein sicheres Passwort ersetzen!

---

## SCHRITT 5: Nextcloud herunterladen

```bash
cd /tmp
wget https://download.nextcloud.com/server/releases/latest.tar.bz2
wget https://download.nextcloud.com/server/releases/latest.tar.bz2.sha256

# Integrität prüfen
sha256sum -c latest.tar.bz2.sha256
```

Sollte ausgeben: `latest.tar.bz2: OK`

---

## SCHRITT 6: Nextcloud entpacken und installieren

```bash
tar -xjf latest.tar.bz2

# In Apache document root kopieren
sudo cp -r nextcloud /var/www/

# Berechtigungen setzen
sudo chown -R www-data:www-data /var/www/nextcloud
sudo chmod -R 755 /var/www/nextcloud
```

---

## SCHRITT 7: Let's Encrypt Zertifikat beantragen

Der `standalone`-Modus braucht Port 80 exklusiv. Da Apache seit Schritt 3 läuft, wird Apache über Hooks kurz gestoppt und danach wieder gestartet. Die Hooks werden in der Renewal-Konfiguration gespeichert und gelten auch für die automatische Erneuerung.

```bash
# Certbot installieren
sudo apt install -y certbot

# Zertifikat beantragen
sudo certbot certonly --standalone \
    -d nextcloud.example.com \
    --non-interactive \
    --agree-tos \
    --email admin@example.com \
    --pre-hook "systemctl stop apache2" \
    --post-hook "systemctl start apache2"
```

**Hinweis:** Sollte ohne Fehler durchlaufen!

---

## SCHRITT 8: Apache VirtualHost für HTTP erstellen

```bash
sudo tee /etc/apache2/sites-available/nextcloud.conf > /dev/null << 'EOF'
<VirtualHost *:80>
    ServerName nextcloud.example.com
    ServerAdmin admin@example.com
    
    DocumentRoot /var/www/nextcloud
    
    # HTTP zu HTTPS umleiten
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
    
    <Directory /var/www/nextcloud>
        Require all granted
        AllowOverride All
        Options FollowSymLinks MultiViews
    </Directory>
    
    <IfModule mod_dav.c>
        Dav off
    </IfModule>
    
    ErrorLog ${APACHE_LOG_DIR}/nextcloud-error.log
    CustomLog ${APACHE_LOG_DIR}/nextcloud-access.log combined
</VirtualHost>
EOF
```

---

## SCHRITT 9: Apache VirtualHost für HTTPS erstellen

```bash
sudo tee /etc/apache2/sites-available/nextcloud-ssl.conf > /dev/null << 'EOF'
<VirtualHost *:443>
    ServerName nextcloud.example.com
    ServerAdmin admin@example.com
    
    DocumentRoot /var/www/nextcloud
    
    # SSL-Zertifikat (fullchain.pem enthält bereits die Zwischenzertifikate)
    SSLCertificateFile /etc/letsencrypt/live/nextcloud.example.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/nextcloud.example.com/privkey.pem
    
    # SSL-Sicherheit
    SSLEngine on
    SSLProtocol -all +TLSv1.2 +TLSv1.3
    SSLCipherSuite HIGH:!aNULL:!MD5
    SSLHonorCipherOrder on
    
    # Security Headers
    Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
    Header always set X-Content-Type-Options "nosniff"
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-XSS-Protection "1; mode=block"
    Header always set Referrer-Policy "no-referrer-when-downgrade"
    
    <Directory /var/www/nextcloud>
        Require all granted
        AllowOverride All
        Options FollowSymLinks MultiViews
        
        # Pretty URLs
        RewriteEngine On
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteRule ^(.*)$ index.php/$1 [L]
    </Directory>
    
    <IfModule mod_dav.c>
        Dav off
    </IfModule>
    
    ErrorLog ${APACHE_LOG_DIR}/nextcloud-ssl-error.log
    CustomLog ${APACHE_LOG_DIR}/nextcloud-ssl-access.log combined
</VirtualHost>
EOF
```

---

## SCHRITT 10: VirtualHosts aktivieren

```bash
sudo a2ensite nextcloud.conf
sudo a2ensite nextcloud-ssl.conf

# Konfiguration prüfen
sudo apache2ctl configtest

# Sollte ausgeben: "Syntax OK"
```

Falls Fehler auftreten → Check den Output!

---

## SCHRITT 11: Apache neustarten

```bash
sudo systemctl restart apache2
```

---

## SCHRITT 12: Nextcloud per CLI initialisieren

```bash
sudo -u www-data php /var/www/nextcloud/occ maintenance:install \
  --database "mysql" \
  --database-name "nextcloud" \
  --database-user "dbnextcloud" \
  --database-pass "%%%PW%%%" \
  --admin-user "admin" \
  --admin-pass "%%%ADMIN_PW%%%" \
  --data-dir "/var/www/nextcloud/data"
```

**Wichtig:** Platzhalter mit echten Werten ersetzen!

---

## SCHRITT 13: Nextcloud-Konfiguration anpassen

```bash
# Trusted Domain setzen
sudo -u www-data php /var/www/nextcloud/occ config:system:set trusted_domains 0 --value="nextcloud.example.com"

# CLI-URL setzen
sudo -u www-data php /var/www/nextcloud/occ config:system:set overwrite.cli.url --value="https://nextcloud.example.com"

# .htaccess aktualisieren für Pretty URLs
sudo -u www-data php /var/www/nextcloud/occ maintenance:update:htaccess
```

Upload-Größe und Memory Limit sind PHP-Einstellungen und lassen sich nicht über `occ` setzen. Sie werden in der `php.ini` von Apache angepasst (Ubuntu 24.04: PHP 8.3):

```bash
sudo sed -i \
    -e 's/^memory_limit = .*/memory_limit = 512M/' \
    -e 's/^upload_max_filesize = .*/upload_max_filesize = 512M/' \
    -e 's/^post_max_size = .*/post_max_size = 512M/' \
    /etc/php/8.3/apache2/php.ini

sudo systemctl restart apache2
```

---

## SCHRITT 14: Cron-Job für Nextcloud einrichten

```bash
# Crontab für www-data öffnen
sudo crontab -u www-data -e

# Folgende Zeile hinzufügen (drücke INSERT):
*/5 * * * * php -f /var/www/nextcloud/cron.php

# Speichern und beenden (ESC + :wq + ENTER)
```

---

## SCHRITT 15: Let's Encrypt Auto-Renewal konfigurieren

```bash
# Timer aktivieren
sudo systemctl enable certbot.timer
sudo systemctl start certbot.timer

# Status prüfen
sudo systemctl status certbot.timer

# Test der automatischen Erneuerung
sudo certbot renew --dry-run
```

---

## ✅ SCHRITT 16: Überprüfung und Test

```bash
# Nextcloud Status prüfen
sudo -u www-data php /var/www/nextcloud/occ status

# Sollte ausgeben:
# - installed: true
# - version: X.X.X
# - versionstring: Nextcloud X.X.X

# Datenbank-Verbindung prüfen
sudo -u www-data php /var/www/nextcloud/occ db:add-missing-indices

# System-Überprüfung
sudo -u www-data php /var/www/nextcloud/occ maintenance:repair

# Zertifikat prüfen
openssl x509 -in /etc/letsencrypt/live/nextcloud.example.com/fullchain.pem -text -noout | grep -A 2 "Validity"

# Apache-Module prüfen
sudo apache2ctl -M | grep ssl

# Fehlerlog prüfen
sudo tail -20 /var/log/apache2/nextcloud-ssl-error.log
```

---

## 🌐 SCHRITT 17: Browser-Test

Öffne deinen Browser und navigiere zu:

```
https://nextcloud.example.com
```

Du solltest auf das Nextcloud-Login weitergeleitet werden.

**Login-Daten:**
- Benutzer: `admin`
- Passwort: `%%%ADMIN_PW%%%` (dein gesetztes Admin-Passwort)

---

---

# 📚 ZUSÄTZLICHE ADMIN-AUFGABEN

## Weitere Benutzer erstellen

### Per CLI

```bash
sudo -u www-data php /var/www/nextcloud/occ user:add benutzername
# Du wirst aufgefordert ein Passwort zu setzen
```

### Im Web-Interface

1. Melde dich als Admin an
2. Gehe zu **Einstellungen** (oben rechts)
3. Klick auf **Benutzerverwaltung**
4. Klick **Neuer Benutzer**

---

## Datenbank-Backups einrichten

```bash
# Backup-Verzeichnis erstellen
sudo mkdir -p /backup/nextcloud
sudo chown www-data:www-data /backup/nextcloud

# Backup-Script erstellen
sudo tee /usr/local/bin/nextcloud-backup.sh > /dev/null << 'EOFSCRIPT'
#!/bin/bash
BACKUP_DIR="/backup/nextcloud"
DATE=$(date +%Y%m%d_%H%M%S)

# Nextcloud in Maintenance-Mode
sudo -u www-data php /var/www/nextcloud/occ maintenance:mode --on

# Datenbank sichern
sudo mysqldump -u dbnextcloud -p%%%PW%%% nextcloud > $BACKUP_DIR/db_$DATE.sql

# Nextcloud-Dateien sichern
tar -czf $BACKUP_DIR/nextcloud_files_$DATE.tar.gz /var/www/nextcloud/data

# Aus Maintenance-Mode raus
sudo -u www-data php /var/www/nextcloud/occ maintenance:mode --off

# Alte Backups löschen (älter als 30 Tage)
find $BACKUP_DIR -type f -mtime +30 -delete

echo "Backup erstellt: $DATE"
EOFSCRIPT

sudo chmod +x /usr/local/bin/nextcloud-backup.sh
```

**Cron-Job für tägliche Backups um 2 Uhr morgens:**

```bash
sudo crontab -e

# Folgende Zeile hinzufügen:
0 2 * * * /usr/local/bin/nextcloud-backup.sh >> /var/log/nextcloud-backup.log 2>&1
```

---

## Wichtige regelmäßige Wartung

```bash
# Datenbank-Indizes optimieren (monatlich)
sudo -u www-data php /var/www/nextcloud/occ db:add-missing-indices

# Datenbank bereinigen (wöchentlich)
sudo -u www-data php /var/www/nextcloud/occ maintenance:repair

# Nextcloud aktualisieren (regelmäßig prüfen)
sudo -u www-data php /var/www/nextcloud/occ upgrade

# Cache leeren (wenn nötig)
sudo -u www-data php /var/www/nextcloud/occ maintenance:cache:clear-all
```

---

## Strato HiDrive als externen Speicher einbinden

**Achtung:** Die folgenden Daten sind spezifisch für Strato HiDrive konfiguriert!

### Schritt 1: External Storage App aktivieren

```bash
sudo -u www-data php /var/www/nextcloud/occ app:enable files_external
```

### Methode A: Über Web-Interface (empfohlen)

1. Melde dich als **Admin** in Nextcloud an
2. **Einstellungen** (oben rechts) → **Administration**
3. Linkes Menu: **Externe Speicher**
4. Klick **+ Neuer Speicherort**

Trage folgende Werte ein:

```
Ordnername:           /nextcloud
Speichertyp:          WebDAV
Konfiguration:
  URL:                https://webdav.hidrive.strato.com/
  Entfernter Unterordner: users/<hidrive-user>/nextcloud
Authentifizierung:
  Anmeldename:        <hidrive-user>
  Passwort:           %%%HIDRIVE_PW%%%
  ✓ Authentifizierung erforderlich
```

5. Speichern → Sollte grün werden ✅

### Methode B: Via CLI

```bash
sudo -u www-data php /var/www/nextcloud/occ files_external:create \
    "/nextcloud" \
    "dav" \
    "password::password" \
    -c "host=https://webdav.hidrive.strato.com/" \
    -c "root=users/<hidrive-user>/nextcloud" \
    -c "username=<hidrive-user>" \
    -c "password=%%%HIDRIVE_PW%%%" \
    -c "secure=true"
```

### Wichtige Hinweise zu Strato HiDrive:

- **URL:** `https://webdav.hidrive.strato.com/` (mit .com, nicht .de!)
- **Benutzer:** Dein Strato-Benutzername
- **Passwort:** Dein HiDrive-Passwort
- **Pfad:** `users/<hidrive-user>/nextcloud` - `<hidrive-user>` durch deinen Benutzernamen ersetzen
- **Ordnername:** `/nextcloud` - Im Nextcloud-Interface sichtbar unter diesem Namen

### Überprüfung

```bash
# Liste der externen Speicher
sudo -u www-data php /var/www/nextcloud/occ files_external:list

# Verbindung testen
sudo -u www-data php /var/www/nextcloud/occ files_external:verify
```

Nach dem Speichern sollte `/nextcloud` im linken Menu deiner Nextcloud sichtbar sein mit Dateien aus HiDrive!

---

## Nextcloud-Apps installieren

```bash
# Verfügbare Apps auflisten
sudo -u www-data php /var/www/nextcloud/occ app:list

# App installieren (z.B. Kalender)
sudo -u www-data php /var/www/nextcloud/occ app:install calendar

# App aktivieren
sudo -u www-data php /var/www/nextcloud/occ app:enable calendar

# App deaktivieren
sudo -u www-data php /var/www/nextcloud/occ app:disable calendar

# App deinstallieren
sudo -u www-data php /var/www/nextcloud/occ app:remove calendar
```

---

## Desktop und Mobile Clients

**Desktop Client:** https://nextcloud.com/install/#install-clients

**Mobile Apps:**
- iOS: App Store → "Nextcloud"
- Android: Google Play Store → "Nextcloud"

**Setup:**
- Server: `https://nextcloud.example.com`
- Benutzer: `admin` (oder dein Benutzername)
- Passwort: Dein Nextcloud-Passwort

---

# 🔧 TROUBLESHOOTING

## Problem: "Trusted Domain"

```bash
sudo -u www-data php /var/www/nextcloud/occ config:system:set trusted_domains 0 --value="nextcloud.example.com"
```

## Problem: Admin-Passwort zurücksetzen

```bash
sudo -u www-data php /var/www/nextcloud/occ user:resetpassword admin
```

## Problem: Maintenance Mode aktivieren/deaktivieren

```bash
# Aktivieren
sudo -u www-data php /var/www/nextcloud/occ maintenance:mode --on

# Deaktivieren
sudo -u www-data php /var/www/nextcloud/occ maintenance:mode --off
```

## Problem: Datenbank-Benutzer ändern

```bash
# Neuen User erstellen
sudo mysql << 'EOF'
CREATE USER 'neuer_user'@'localhost' IDENTIFIED BY 'neues_passwort';
GRANT ALL PRIVILEGES ON nextcloud.* TO 'neuer_user'@'localhost';
FLUSH PRIVILEGES;
QUIT;
EOF

# Nextcloud-Config anpassen
sudo -u www-data php /var/www/nextcloud/occ config:system:set dbuser --value="neuer_user"
sudo -u www-data php /var/www/nextcloud/occ config:system:set dbpassword --value="neues_passwort"

# Testen
sudo -u www-data php /var/www/nextcloud/occ status
```

---

## Problem: Apache Fehler prüfen

```bash
# SSL-Error Log
sudo tail -50 /var/log/apache2/nextcloud-ssl-error.log

# Access Log
sudo tail -50 /var/log/apache2/nextcloud-ssl-access.log

# Apache neustarten
sudo systemctl restart apache2

# Status prüfen
sudo systemctl status apache2
```

---

## Problem: Zertifikat erneuern (manuell)

```bash
# Die in Schritt 7 gespeicherten Hooks stoppen und starten Apache automatisch
sudo certbot renew --force-renewal --cert-name nextcloud.example.com
```

---

# 📋 CHECKLISTE

- [ ] System aktualisiert
- [ ] Apache, MariaDB, PHP installiert
- [ ] Apache-Module aktiviert
- [ ] Datenbank-User `dbnextcloud` erstellt
- [ ] Let's Encrypt Zertifikat besorgt
- [ ] VirtualHosts konfiguriert
- [ ] Nextcloud installiert und initialisiert
- [ ] Konfiguration angepasst
- [ ] Cron-Job eingerichtet
- [ ] Auto-Renewal konfiguriert
- [ ] Browser-Test erfolgreich
- [ ] Admin-Login funktioniert
- [ ] Backup-Script eingerichtet

---

# 🔐 SICHERHEITS-TIPPS

```bash
# Config-Datei nur für www-data lesbar
sudo chmod 640 /var/www/nextcloud/config/config.php

# Starkes Passwort generieren
openssl rand -base64 32

# Firewall (UFW) prüfen
sudo ufw status
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# SSH Hardening: Root-Login verbieten
# Vorher sicherstellen, dass ein anderer Benutzer mit sudo-Rechten per SSH einloggen kann!
# (Ubuntu-Standard ist "#PermitRootLogin prohibit-password", daher allgemeines Muster)
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo sshd -t
sudo systemctl restart ssh
```

---

**Installation abgeschlossen! 🎉**

Bei Fragen oder Problemen: `sudo -u www-data php /var/www/nextcloud/occ status`
