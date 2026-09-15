# SQL-Snippets

## Duplikate gruppieren

Findet alle Kombinationen aus `objektname` und `inventarnummer`, die mehrfach vorkommen:

```sql
SELECT
    objektname, inventarnummer, COUNT(*)
FROM
    bestand
GROUP BY
    objektname, inventarnummer
HAVING
    COUNT(*) > 1
```
