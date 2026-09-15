# PowerShell: Active Directory

Benötigt das Modul `ActiveDirectory` (siehe [RSAT aktivieren](snippets.md#rsat-aktivieren)).

## Benutzer in alle Gruppen innerhalb einer OU aufnehmen

```powershell
Get-ADGroup -SearchBase "OU=Groups,DC=example,DC=com" -Filter * | ForEach-Object { Add-ADGroupMember -Identity $_ -Members "<username>" }
```
