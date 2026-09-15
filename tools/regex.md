# RegEx

Die Ersetzungen mit `$1` und `\L` funktionieren z. B. in VS Code und Notepad++ (dort `\L\1` bzw. `\L$1`).

## Drittes Semikolon in einer Zeile

Gruppe 1 enthält das dritte Semikolon:

```regex
^(?:[^;]*;){2}[^;]*(;)
```

## Datum konvertieren (TT.MM.JJJJ → JJJJ-MM-TT)

```regex
search:  (\d{1,2})\.(\d{1,2})\.(\d{4})
replace: $3-$2-$1
```

## Dezimalzahlen matchen

Ganze Zahlen oder Zahlen mit bis zu zwei Nachkommastellen, mit Punkt oder Komma als Trennzeichen:

```regex
\d+(?:[.,]\d{1,2})?
```

## Großbuchstaben durch Kleinbuchstaben ersetzen

```regex
search:  (\w)
replace: \L$1
```

## Text durch Markdown-Links bzw. Anker ersetzen

Beispiel-Text:

> siehe "Thema 1"

```regex
search:  siehe "(.)(.*)"
replace: siehe "[$1$2](#\L$1$2)"
```

Ergebnis:

> siehe "[Thema 1](#thema 1)"

Für gültige Anker müssen Leerzeichen noch durch `-` ersetzt werden (`#thema-1`).
