# Tastatur vom HP Elite X2 tippt nicht

- Modell Tablet: HP Elite X2 1012 G2
- Betriebssystem: Windows 10 22H2
- Modell Tastatur: HSTNN-D72K

**Problembeschreibung:** Das TouchPad der magnetischen Tastatur funktioniert, aber die Tastatur tippt nicht. 

## Mögliche Lösung

- `regedit.exe` als Administrator
- `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Class`
- Key: `{4d36e96b-e325-11ce-bfc1-08002be10318}` (Hinweis: es gibt viele Keysm die so ähnlich aussehen - darauf achten, dass er der erste Abschnitt `4d36e96b` mit `b` endet, in Property `Class` steht `Keyboard` als Wert)
- Property `UpperFilters` (REG_MULTI_SZ)
- In meinem Problemfall stand als Wert darin:
    ```
    SynTP
    kbdclass
    ```
- Richtig:
    ```
    kbdclass
    ```
- Neustart durchführen
- Tastatur funktioniert wieder

**Problem:** Der Wert `SynTP` hat dafür gesorgt, dass sich der Synaptics Touchpad-Treiber in den Keyboard-Stack eingemischt hat.

# Weitere mögliche Ursachen laut Internet-Recherche

Anmerkung: davon hat bei mir nichts gewirkt

## Anschlagverzögerung

- Eingabehilfen - Tastatur - Anschlagverzögerung
- kann aktiviert sein und für verwirrendes Verhalten sorgen

## Firmware-Update der Tastatur

- Tastatur abziehen
- Herunterfahren
- Einschalter für 30 Sekunden unterbrochen gedrückt halten
- Tastatur anstecken
- normal einschalten
- Gerät sollte Firmware-Update der Tastatur durchführen

## Verschmutzung

- Kontakte der Dock-Tastatur sowie des Tablets reinigen

usw...

# Keyboard on HP Elite X2 not typing

- Tablet Model: HP Elite X2 1012 G2
- OS: Windows 10 22H2
- Keyboard Model: HSTNN-D72K

**Problem Description:** The TouchPad on the magnetic keyboard works, but the keys do not register any input.

## Potential Solution

- Run `regedit.exe` as Administrator.
- Navigate to: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Class`
- Locate the Key: {`4d36e96b-e325-11ce-bfc1-08002be10318}` (Note: There are many similar-looking keys. Ensure the first segment `4d36e96b` ends with a `b`. The `Class` property should have the value `Keyboard`.)
- Look for the Property: `UpperFilters` (REG_MULTI_SZ).
- In my case, the value was:
    ```
    SynTP
    kbdclass
    ```
- Change it to the correct value:
    ```
    kbdclass
    ```
- Restart the device.

The keyboard should be working again.

**Root Cause:** The `SynTP` value caused the Synaptics Touchpad driver to interfere with the keyboard stack, blocking key inputs.

# Other possible causes (found online)

Note: None of these worked for my specific case, but they are common troubleshooting steps:

## Filter Keys

- Go to Accessibility > Keyboard > Filter Keys.
- This might be enabled, causing confusing behavior or ignored keystrokes.

## Keyboard Firmware Update

- Detach the keyboard.
- Shut down the device.
- Press and hold the Power button for 30 seconds continuously.
- Reattach the keyboard.
- Turn the device on normally.
- The system should initiate a keyboard firmware update.

## Physical Debris / Cleaning

- Clean the gold pogo-pin contacts on both the docking keyboard and the tablet using isopropyl alcohol.

tbc...
