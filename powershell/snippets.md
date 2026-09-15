# PowerShell-Snippets

## Prompt bearbeiten

```powershell
code $PROFILE
```

Inhalt der `$PROFILE`-Datei:

```powershell
function prompt {
    $dateTime = Get-Date -Format "dd.MM.yyyy HH:mm:ss"
    $currentDirectory = $(Get-Location)
    $UncRoot = $currentDirectory.Drive.DisplayRoot

    Write-Host "$dateTime" -NoNewline -ForegroundColor White
    Write-Host " $UncRoot" -ForegroundColor Gray
    # Convert-Path wird für reine UNC-Pfade benötigt
    Write-Host "PS $(Convert-Path $currentDirectory)>" -NoNewline -ForegroundColor Yellow
    return " "
}
```

## GUI: TextBox mit AutoComplete

```powershell
Add-Type -AssemblyName System.Windows.Forms

$TextboxUser = New-Object System.Windows.Forms.TextBox
$TextboxUser.Location = New-Object System.Drawing.Size(10,40)
$TextboxUser.Size = New-Object System.Drawing.Size(200,20)
$TextboxUser.Name = 'TextBox_User'
$TextboxUser.AutoCompleteSource = 'CustomSource'
$TextboxUser.AutoCompleteMode = 'SuggestAppend'

# AutoComplete-Liste befüllen, z. B. aus den Dateinamen eines Ordners
(Get-ChildItem "\\server\freigabe\ordner").BaseName | ForEach-Object { [void]$TextboxUser.AutoCompleteCustomSource.Add($_) }
```

## Dateien anhand des Dateinamens gruppieren und zählen

Gruppiert nach den ersten vier Zeichen des Dateinamens:

```powershell
Get-ChildItem "C:\ordner\*.*" | Group-Object -Property { $_.Name.Substring(0,4) }
```

## Liste von CustomObjects anlegen

```powershell
$config = @()
$config += [PSCustomObject]@{ ComputerName = "PC-01"; UserName = "user01" }
$config += [PSCustomObject]@{ ComputerName = "PC-02"; UserName = "user02" }
```

## RSAT aktivieren

```powershell
# Verfügbare Module anzeigen
Get-WindowsCapability -Name RSAT* -Online | Select-Object -Property Name, State

# Typischerweise benötigte RSAT-Module
Add-WindowsCapability -Name "Rsat.DHCP.Tools~~~~0.0.1.0" -Online
Add-WindowsCapability -Name "Rsat.Dns.Tools~~~~0.0.1.0" -Online
Add-WindowsCapability -Name "Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0" -Online
Add-WindowsCapability -Name "Rsat.GroupPolicy.Management.Tools~~~~0.0.1.0" -Online
Add-WindowsCapability -Name "Rsat.ServerManager.Tools~~~~0.0.1.0" -Online
```

## Verknüpfung auf eine URL anlegen

Legt eine Verknüpfung auf dem öffentlichen Desktop an (für alle Benutzer, benötigt Adminrechte):

```powershell
$objShell = New-Object -ComObject ("WScript.Shell")
$objShortCut = $objShell.CreateShortcut("$($env:SystemDrive)\Users\Public\Desktop\Beispiel.lnk")
$objShortCut.TargetPath = "https://www.example.com"
$objShortCut.Arguments = ""
$objShortCut.IconLocation = "$($env:SystemRoot)\System32\imageres.dll,94"
$objShortCut.Description = "Öffnet example.com"
$objShortCut.Save()
```

## COM-Port eines USB-Seriell-Adapters (FTDI) auslesen

Gibt nur die Portnummer zurück (z. B. `3` für `COM3`):

```powershell
(Get-CimInstance Win32_PnPEntity | Where-Object { $_.Manufacturer -like "*FTDI*" -and $_.Description -like "*USB Serial Port*" }).Name -replace '.*\(COM([0-9]+)\)', '$1'
```

## Base64-String decodieren

```powershell
$Base64String = "Base64StringEinsetzen"
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($Base64String))
```

## .lnk-Verknüpfungen analysieren

```powershell
$WSShell = New-Object -ComObject WScript.Shell
$shortcutFiles = Get-ChildItem ".\*.lnk"
$results = foreach ($shortcutFile in $shortcutFiles) {
    $shortcut = $WSShell.CreateShortcut($shortcutFile.FullName)
    [PSCustomObject]@{
        Name       = $shortcutFile.Name
        TargetPath = $shortcut.TargetPath
        Arguments  = $shortcut.Arguments
    }
}
$results | Format-Table -Wrap -AutoSize
```

## Passwort erzeugen

> `Get-Random -Count` zieht ohne Zurücklegen, jedes Zeichen kommt also höchstens einmal vor. Für einfache Zwecke ausreichend; für Kryptografie besser einen Passwortmanager nutzen.

```powershell
# Beispiel: ksorFXdRMnmE
(-join ((65..90) + (97..122) | Get-Random -Count 12 | ForEach-Object {[char]$_}))

# Beispiel: rBamGjEqo5W9fgdC
(-join ((65..90) + (97..122) + (48..57) | Get-Random -Count 16 | ForEach-Object {[char]$_}))

# Beispiel: niTC-6Fz9-ozbR-BrVh
(1..4 | ForEach-Object { -join ((65..90) + (97..122) + (48..57) | Get-Random -Count 4 | ForEach-Object {[char]$_}) }) -join "-"
```
