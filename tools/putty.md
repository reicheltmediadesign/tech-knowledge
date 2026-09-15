# PuTTY

## Solarized-Farbschema als Standard-Einstellung

Als `.reg`-Datei speichern und importieren. Setzt Schrift und Farben für die „Default Settings“, die neue Sitzungen übernehmen.

```reg
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\SOFTWARE\SimonTatham\PuTTY\Sessions\Default%20Settings]
"FullScreenOnAltEnter"=dword:00000001
"Font"="Consolas"
"FontIsBold"=dword:00000000
"FontCharSet"=dword:00000000
"FontHeight"=dword:0000000b
"FontQuality"=dword:00000003
"FontVTMode"=dword:00000004
"UseSystemColours"=dword:00000000
"TryPalette"=dword:00000000
"ANSIColour"=dword:00000001
"Xterm256Colour"=dword:00000001
"TrueColour"=dword:00000001
"BoldAsColour"=dword:00000000
"Colour0"="131,148,150"
"Colour1"="147,161,161"
"Colour2"="0,43,54"
"Colour3"="7,54,66"
"Colour4"="0,43,54"
"Colour5"="238,232,213"
"Colour6"="7,54,66"
"Colour7"="0,43,54"
"Colour8"="220,50,47"
"Colour9"="203,75,22"
"Colour10"="133,153,0"
"Colour11"="88,110,117"
"Colour12"="181,137,0"
"Colour13"="101,123,131"
"Colour14"="38,139,210"
"Colour15"="131,148,150"
"Colour16"="211,54,130"
"Colour17"="108,113,196"
"Colour18"="42,161,152"
"Colour19"="147,161,161"
"Colour20"="238,232,213"
"Colour21"="253,246,227"
```

Optional kann zusätzlich ein Standard-Schlüssel hinterlegt werden:

```reg
"PublicKeyFile"="C:\\Users\\<benutzer>\\.ssh\\id_rsa.ppk"
```
