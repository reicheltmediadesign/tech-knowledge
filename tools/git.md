# Git

## Branches löschen

### lokal

```bash
git branch -D branch_name
```

### remote

```bash
git push origin --delete branch_name
```

## SSH-Key unter Windows einrichten

- PowerShell als Administrator starten
- Private-Key-Datei (z. B. `id_ed25519` oder `id_rsa`) nach `$env:USERPROFILE\.ssh` kopieren
- Dienst `ssh-agent` automatisch starten lassen:

```powershell
Get-Service ssh-agent | Set-Service -StartupType Automatic -PassThru | Start-Service
```

- Privaten Schlüssel dem SSH-Agent bekannt machen:

```powershell
ssh-add $env:USERPROFILE\.ssh\id_rsa
```

Neuen Schlüssel erzeugen: siehe [SSH](ssh.md).

## git config

```bash
git config --global user.email "E-Mail-Adresse"
git config --global user.name "Vorname Nachname"
```

## git log pretty

```bash
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

Ausgabe von `git lg`:

```
* d2e81d6 - feat: Kalender - Standard-Ansicht ohne abgesagte Veranstaltungen (3 days ago) <Vorname Nachname>
```
