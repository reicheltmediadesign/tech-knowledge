# Linux-Snippets

## Schreibrechte für SQLite-Datenbanken im Webserver

Für SQLite reicht es nicht, nur die Datenbankdatei für den Webserver-Benutzer schreibbar zu machen: SQLite legt beim Schreiben neben der Datei `…-journal` bzw. im WAL-Modus `…-wal` und `…-shm` an. Deshalb muss auch das **Verzeichnis** für den Webserver-Benutzer schreibbar sein, sonst kommt „attempt to write a readonly database“, obwohl die Datei selbst beschreibbar ist.

Beispiel mit Webserver-Benutzer `www-data` (Debian/Ubuntu; unter FreeBSD z. B. `www`):

```bash
# Verzeichnis mit der Datenbank
chown -R www-data:www-data /var/www/meine-app/db
chmod 750 /var/www/meine-app/db
chmod 640 /var/www/meine-app/db/datenbank.sqlite

# Upload-Verzeichnis
chown www-data:www-data /var/www/meine-app/uploads
chmod 750 /var/www/meine-app/uploads
```

## Manuelle IP-Adresse einstellen (Debian, ifupdown)

```bash
vi /etc/network/interfaces
```

```
auto eth0
iface eth0 inet static
    address 192.168.2.249
    netmask 255.255.255.0
    gateway 192.168.2.254
    dns-nameservers 192.168.2.254 1.1.1.1
```

```bash
/etc/init.d/networking restart
```

## Windows-Partition mounten

```bash
sudo fdisk -l
```

Heißt die NTFS-Partition z. B. `sda3`:

```bash
sudo mkdir /media/OS
sudo mount -t ntfs -o nls=utf8,umask=0222 /dev/sda3 /media/OS
```
