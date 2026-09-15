# Registry-Tweaks

## Computername im Windows Explorer anzeigen

Zeigt statt „Dieser PC“ den Computernamen an.

```
Key:      HKCR\CLSID\{20D04FE0-3AEA-1069-A2D8-08002B30309D}
Property: LocalizedString
Type:     REG_EXPAND_SZ
Value:    System: %COMPUTERNAME%
```

## Windows 11 Upgrade: fehlendes TPM 2.0/CPU-Minimum ignorieren

```ini
[HKEY_LOCAL_MACHINE\SYSTEM\Setup\MoSetup]
AllowUpgradesWithUnsupportedTPMOrCPU = [DWORD] 1
```

Wenn das nicht reicht, in einer Eingabeaufforderung mit Adminrechten:

```bat
reg.exe add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\AppCompatFlags\HwReqChk" /f /v HwReqChkVars /t REG_MULTI_SZ /s , /d "SQ_SecureBootCapable=TRUE,SQ_SecureBootEnabled=TRUE,SQ_TpmVersion=2,SQ_RamMB=8192,"
```
