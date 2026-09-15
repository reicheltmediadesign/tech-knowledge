# SSH

## Schlüsselpaar mit ssh-keygen erzeugen

RSA mit 4096 Bit und 512 KDF-Runden für die Passphrase:

```bash
ssh-keygen -b 4096 -a 512
```

Alternative mit Ed25519 (kürzere Schlüssel, heute Standardempfehlung):

```bash
ssh-keygen -t ed25519 -a 512
```

Schlüssel unter Windows im SSH-Agent hinterlegen: siehe [Git](git.md#ssh-key-unter-windows-einrichten).
