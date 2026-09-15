# Python unter Windows unbeaufsichtigt installieren

Installiert Python für alle Benutzer, ergänzt `PATH`, installiert pip und den `py`-Launcher, ohne Startmenü-Verknüpfungen und Testsuite. Benötigt Adminrechte.

```bat
python-3.12.12-amd64.exe /quiet InstallAllUsers=1 PrependPath=1 Shortcuts=0 Include_test=0 Include_launcher=1 InstallLauncherAllUsers=1 Include_pip=1
```
