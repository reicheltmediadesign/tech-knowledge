# Vim

## Zeilen bearbeiten

`:v/^abc/s/^/xxx`
Fügt vor allen Zeilen, die nicht mit „abc“ beginnen, die Zeichenfolge „xxx“ ein.

`:g!/pattern/d`
Löscht alle Zeilen, die „pattern“ nicht enthalten. Ohne das `!` werden alle Zeilen gelöscht, die „pattern“ enthalten.

`:g/^/norm yyp`
Dupliziert jede Zeile im gesamten Dokument.

`ggVGu`
Macht alle Zeilen klein (lowercase), `ggVGU` macht sie groß.

`:g/^/m0`
Kehrt die Reihenfolge aller Zeilen um:

```
1A     5A
2A     4A
3A  →  3A
4A     2A
5A     1A
```

`:for i in range(1,255) | .put='10.0.0.'.i | endfor`
Fügt die IP-Adressen von 10.0.0.1 bis 10.0.0.255 ein (`range()` in Vim schließt das Ende mit ein).

## Suchen und Ersetzen

`:%s/alt="\(.*\)"/[\1]/`
Ersetzt `alt="EG110"` durch `[EG110]`.

`:%s/001 \(.*\)/|\1|/`
Ersetzt `001 12345678` durch `|12345678|`.

`/|\_s|`
Findet aufeinanderfolgende Zeilen, die mit `|` beginnen. `\_s` steht dabei für Whitespace oder Zeilenumbruch.

Beispiel: Der Befehl springt zum Übergang zwischen den beiden direkt aufeinanderfolgenden `|12345678|`-Zeilen:

```
|12345678|
029mA
|12345678|
|12345678|
029mA
|12345678|
029mA
```

`/<!--\_.\{-}-->`
Findet HTML-Kommentare, auch über mehrere Zeilen hinweg.

## Dateien

`:g/^/exe ".w ".line(".").".txt"`
Speichert jede Zeile in eine eigene Datei, benannt nach der Zeilennummer.

`'0`
Öffnet die zuletzt editierte Datei.

`:%!xxd`
Wandelt die aktuelle Datei in eine Hex-Ansicht um (mit `:%!xxd -r` geht es wieder zurück).

`:set nobomb`
BOM-Zeichen deaktivieren.

## Einfügemodus

`<C-P>`
Auto-Vervollständigung basierend auf dem Inhalt der Datei.
