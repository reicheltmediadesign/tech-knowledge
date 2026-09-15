# Batch-Snippets

## XAMPP: MySQL-Dump von jeder Datenbank erzeugen

Erzeugt für jede Datenbank (außer den Systemdatenbanken) eine eigene `.sql`-Datei im Unterordner `temp_mysqldump`.

```batch
@echo off
set MYSQLUSER=<benutzer>
set MYSQLPASS=<passwort>
md %cd%\temp_mysqldump
c:\xampp\mysql\bin\mysql.exe -u%MYSQLUSER% -p%MYSQLPASS% -s -N -e "SHOW DATABASES;" | for /F "usebackq" %%D in (`findstr /V "information_schema performance_schema phpmyadmin"`) do c:\xampp\mysql\bin\mysqldump.exe %%D -u%MYSQLUSER% -p%MYSQLPASS% --default-character-set=utf8mb4 > %cd%\temp_mysqldump\%%D.sql
```
