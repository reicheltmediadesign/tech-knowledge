# Windows: Zugriff wiederherstellen (Passwort zurücksetzen)

> Nur für eigene oder selbst verwaltete Geräte.

## Variante 1: SYSTEM-Konsole über die Bildschirmtastatur

> Funktioniert nur ohne BitLocker!

1. Im Anmeldebildschirm **Shift** gedrückt halten und *Neustart* anklicken, Windows 11 startet in den Recovery-Modus
2. Problembehandlung > Starteinstellungen > Frühen Start des Schutzes vor Schadsoftware *deaktivieren*
3. Problembehandlung > Eingabeaufforderung

```bat
c:
cd \Windows\System32
rename osk.exe osk.exe.bak
copy cmd.exe osk.exe
```

Mit dem nächsten Klick auf **Bildschirmtastatur** in den **Eingabehilfen** im Anmeldebildschirm startet eine Konsole mit SYSTEM-Rechten. Von dort aus lassen sich z. B. mit `net user` Passwörter zurücksetzen:

```bat
net user administrator NeuesPassw0rt
```

Danach die Änderung rückgängig machen (`osk.exe.bak` wieder zu `osk.exe` umbenennen).

## Variante 2: Abgesicherter Modus mit Eingabeaufforderung

- Neustart mit gedrückter Shift-Taste
- Erweiterte Optionen > Starteinstellungen > Abgesicherter Modus mit Eingabeaufforderung
- bei einer AD-Domäne: als lokaler Administrator mit dem LAPS-Passwort anmelden

```bat
net user administrator NeuesPassw0rt
net user administrator /active:yes
```
