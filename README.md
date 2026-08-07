# Blitz AIO — Installateurs

Ce dépôt ne contient que les **installateurs compilés** de Blitz AIO.
Le code source est privé.

## Télécharger

Prends le `.exe` de la dernière version dans l'onglet
[Releases](https://github.com/DJACKu/blitzaio-releases/releases/latest).

Une clé de licence est nécessaire pour utiliser l'application.

## Vérifier votre téléchargement

Chaque release publie le SHA-256 de l'installateur. Comparez-le avant
d'exécuter le fichier :

```powershell
Get-FileHash "Blitz-Setup-X.X.X.exe" -Algorithm SHA256

Si l'empreinte ne correspond pas à celle de la release, n'exécutez pas
le fichier et re-téléchargez-le depuis cette page.

Alerte antivirus

L'application n'est pas signée numériquement (un certificat de signature
coûte plusieurs centaines d'euros par an). Windows affichera donc
« Éditeur inconnu », et certains antivirus lèveront une détection
heuristique. C'est un faux positif — l'empreinte SHA-256 ci-dessus permet
de vérifier l'intégrité du fichier.
