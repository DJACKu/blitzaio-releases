# Blitz AIO — Installers

> NFT minting bot for Ethereum, Base & Robinhood Chain.
> 📖 **Full user guide: [djacku.github.io/blitzaio-releases](https://djacku.github.io/blitzaio-releases/)** (English + Français)

This repository only contains the **compiled installers** of Blitz AIO.
The source code is private.

## Download

Grab the `.exe` of the latest version from the
[Releases](https://github.com/DJACKu/blitzaio-releases/releases/latest) tab.

A license key is required to use the application.

## Verify your download

Every release publishes the installer's SHA-256. Compare it before running
the file:

```powershell
Get-FileHash "Blitz-Setup-X.X.X.exe" -Algorithm SHA256
```

If the hash doesn't match the one in the release, don't run the file —
download it again from this page.

## Antivirus warning

The application is not code-signed (a signing certificate costs several
hundred euros per year). Windows will show "Unknown publisher", and some
antiviruses will raise a heuristic detection. It's a false positive — the
SHA-256 hash above lets you verify the file's integrity.

---

## 🇫🇷 Français

Ce dépôt ne contient que les **installateurs compilés** de Blitz AIO.
Le code source est privé.

📖 **Guide complet : [djacku.github.io/blitzaio-releases](https://djacku.github.io/blitzaio-releases/#/fr)**

**Télécharger** : prends le `.exe` de la dernière version dans l'onglet
[Releases](https://github.com/DJACKu/blitzaio-releases/releases/latest).
Une clé de licence est nécessaire pour utiliser l'application.

**Vérifier votre téléchargement** : chaque release publie le SHA-256 de
l'installateur (`Get-FileHash "Blitz-Setup-X.X.X.exe" -Algorithm SHA256`).
Si l'empreinte ne correspond pas, n'exécutez pas le fichier.

**Alerte antivirus** : l'application n'est pas signée numériquement.
Windows affichera « Éditeur inconnu » et certains antivirus lèveront une
détection heuristique. C'est un faux positif — vérifiez l'empreinte SHA-256.
