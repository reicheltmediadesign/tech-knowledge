# Windows-Einrichtung (OOBE) ohne Microsoft-Konto

Im OOBE-Bildschirm mit **Shift + F10** eine Eingabeaufforderung öffnen.

## Variante 1: lokales Konto

```bat
start ms-cxh:localonly
```

## Variante 2: BypassNRO

```bat
oobe\bypassnro
```

Falls das Skript nicht (mehr) vorhanden ist, vorher den Registry-Wert setzen:

```bat
reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\OOBE /v BypassNRO /t REG_DWORD /d 1
```

Alternativ:

```bat
cd %systemroot%\System32\oobe
msoobe /bypassnro
```
