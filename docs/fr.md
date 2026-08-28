# Blitz AIO - Guide Complet

> NFT Minting Bot pour Ethereum, Base & Sepolia (v1.11.26)

> 🇬🇧 English version (reference, up to date): [click here](#/)
> Cette version francaise date de la v1.11.26 — les nouveautes (chaine Robinhood, Bridge, alertes Drops, reprice Listing) sont documentees dans la version anglaise. Exception : le **Contrôle à distance Discord** (v1.11.39) est documenté en français tout en bas de ce guide.

---

## Table des Matieres

1. [Installation](#1-installation)
2. [Premier Lancement](#2-premier-lancement)
3. [Configuration RPC](#3-configuration-rpc)
4. [Gestion des Wallets](#4-gestion-des-wallets)
5. [Creer une Task de Mint](#5-creer-une-task-de-mint)
6. [OpenSea Drop Mode](#6-opensea-drop-mode)
7. [Gestion des Transactions](#7-gestion-des-transactions)
8. [Force Gas](#8-force-gas)
9. [Distributor & Recollector](#9-distributor--recollector)
10. [Eligibility Check & Approvals](#10-eligibility-check--approvals)
11. [Settings](#11-settings)
12. [Analytics & Gas Monitor](#12-analytics--gas-monitor)
13. [Privacy Mode](#13-privacy-mode)
14. [Discord Notifications](#14-discord-notifications)
15. [Scheduled Tasks](#15-scheduled-tasks)
16. [Profit Calculator](#16-profit-calculator)
17. [OpenSea Listing Tool](#17-opensea-listing-tool)
18. [Drop Discovery Tool](#18-drop-discovery-tool)
19. [Activity Feed](#19-activity-feed)
20. [Quick Task](#20-quick-task)
21. [Securite](#21-securite)
22. [Auto-Update](#22-auto-update)
23. [FAQ / Troubleshooting](#23-faq--troubleshooting)

---

## 1. Installation

### Prerequis
- Windows 10/11
- Connexion internet
- Un RPC Ethereum (Alchemy recommande, gratuit)

### Installation
1. Recevoir le fichier `Blitz Setup X.X.X.exe`
2. Lancer l'installer et suivre les etapes
3. Blitz se lance automatiquement apres l'installation
4. Les mises a jour se font automatiquement (bandeau dans l'app)

---

## 2. Premier Lancement

Au premier lancement, Blitz demande de creer un **mot de passe maitre**.

Ce mot de passe :
- Chiffre toutes vos cles privees (AES-256-GCM)
- Est requis a chaque ouverture de l'app
- Ne peut PAS etre recupere si oublie
- Minimum 6 caracteres

> **Important**: Si vous perdez ce mot de passe, vos cles privees sont perdues. Gardez une copie de vos cles privees en lieu sur.

### Le Dashboard (page d'accueil)

Apres login, vous arrivez sur le **Dashboard**. C'est votre vue d'ensemble en un coup d'oeil (refondu en v1.10.x).

#### Stat cards (responsive, cliquables)
| Card | Affiche | Click |
|---|---|---|
| **Wallets** | Nombre de wallets + total ETH balance | -> Wallets page |
| **Tasks Running** | Tasks actives `/` total | -> Tasks page |
| **Success Rate** | % de TX reussies sur tout l'historique | -> Analytics |
| **Gas Spent** | Total ETH brule en gas (tout historique) | -- |
| **Today** | Mints reussies aujourd'hui (+ count failed) | -- |

#### Active task banner
Si une task est en cours d'execution (`running`/`minting`), un bandeau vert pulsant apparait en haut avec le nom de la task + le status message courant. Click -> page Tasks.

#### Recent Transactions
Tableau des 20 dernieres TX avec:
- TX hash (click pour copier)
- Task name + Wallet name
- **When** (timestamp relatif: `2m ago`, `1h ago` - hover = date complete)
- Status badge (success / failed / partial)
- **Icone Etherscan** (network-aware: mainnet/base/sepolia)
- Filtres en chips: `all` / `success` / `failed`

#### Gas Trend
Mini-chart du gas mainnet sur les 16 dernieres minutes (samples toutes les 12s, persistes en localStorage).
- Current value affichee en grand (en bas-gauche, accent color)
- Min/max overlay
- Axe Y auto-range pour montrer les variations meme petites

#### Quick Actions
4 boutons: `+ Task` / `Quick Task` / `Browse Drops` / `+ Wallet`.

#### Last refresh
Petit timestamp en bas indiquant l'heure du dernier load (auto-refresh sur events `task-status`).

---

## 3. Configuration RPC

Blitz a besoin d'un RPC pour communiquer avec la blockchain. Supporte Ethereum Mainnet, Base L2, et Sepolia (testnet).

### Obtenir un RPC gratuit (Alchemy)
1. Creer un compte sur [alchemy.com](https://www.alchemy.com)
2. Creer une app sur **Ethereum Mainnet**
3. Copier l'URL HTTPS (ex: `https://eth-mainnet.g.alchemy.com/v2/votre-cle`)
4. Optionnel: copier aussi l'URL WSS pour le snipe mode

### Ajouter un RPC dans Blitz
1. Aller dans **Settings**
2. Cliquer sur **Add RPC**
3. Coller l'URL
4. Choisir le type: **HTTPS** (standard) ou **WSS** (pour le snipe mode)
5. Choisir le **network**: Ethereum ou Base
6. Cliquer sur **Test Connection** pour verifier
7. Cliquer sur **Add**

### RPCs par defaut
- **Base**: `https://mainnet.base.org` (pre-configure)
- **Sepolia**: `https://rpc.sepolia.org` (pre-configure)
- **Ethereum**: necessite un RPC externe (Alchemy recommande)

### Plusieurs RPCs
Vous pouvez ajouter plusieurs RPCs par network. Blitz selectionne le premier RPC actif pour chaque network.

### Drops Scan RPC dedie (optionnel, v1.11.20)
Le scan de la page **Drops** envoie de gros `eth_getLogs` qui peuvent saturer un RPC free-tier et ralentir tes mints (les deux se battent pour le meme RPC). Dans **Settings > Ethereum RPC**, le champ **Drops Scan RPC** permet de pointer le scan Drops vers un RPC separe. Laisser vide = le scan utilise le RPC principal (comportement par defaut). Recommande si tu utilises la page Drops pendant que des tasks tournent.

### Indicateur RPC
Le point colore en haut a droite de la fenetre indique l'etat de la connexion :
- Vert = connecte
- Rouge = deconnecte ou pas de RPC configure

---

## 4. Gestion des Wallets

### Wallet Groups
Les wallets sont organises en **groupes** (ex: "Main", "Burners", "WL Wallets").
- Cliquer sur un groupe dans le panneau gauche pour le selectionner
- **Create Group**: creer un nouveau groupe
- Supprimer un groupe deplace ses wallets vers "Main"

### Ajouter un Wallet
Cliquer sur le bouton **+** dans le header, puis:
- **Nickname**: nom pour identifier le wallet
- **Private Key**: cle privee du wallet (sera chiffree)
- **Group**: groupe dans lequel placer le wallet

### Creer des Wallets (Batch)
Cliquer sur l'icone "Create" dans le header:
- Entrer le nombre de wallets a generer
- Blitz cree des wallets aleatoires avec des cles privees uniques
- Les cles sont chiffrees et stockees localement

### Importer des Wallets (CSV)
Cliquer sur l'icone "Import" dans le header:
- Coller le contenu CSV
- Formats supportes:
  - `address,privateKey,nickname` (format Blitz/Breeze)
  - `nickname,privateKey`
  - `privateKey` (une cle par ligne)

### Exporter des Wallets (CSV)
Cliquer sur l'icone "Export" dans le header:
- Exporte tous les wallets du groupe selectionne
- Format: `address,privateKey,nickname`
- **Attention**: le fichier exporte contient les cles privees en clair!

### Verifier les Balances
Cliquer sur l'icone "Balance Check" dans le header pour actualiser les balances ETH de tous les wallets.

Si **Auto Refresh Balances** est active dans Settings, les balances sont actualisees automatiquement toutes les 60 secondes.

### ENS Resolution
Blitz resout automatiquement les noms ENS (ex: `vitalik.eth`) pour chaque wallet. Si un wallet a un nom ENS, il s'affiche en bleu a cote du nickname dans la table. En privacy mode, le nom ENS est masque.

### Delegations EIP-7702 (v1.11.21)
Depuis l'upgrade Pectra, un wallet EOA peut porter une **delegation EIP-7702** (du code attache, depuis un wallet "smart account" comme MetaMask Smart Account, Ambire, etc.). Les contrats de mint qui exigent un EOA pur rejettent ces wallets avec l'erreur `NotEOA`.

Cliquer sur l'icone **bouclier** dans le header de l'onglet Wallets pour:
- Scanner tous les wallets on-chain et detecter ceux qui sont delegues (badge rouge `7702` a cote de l'adresse).
- Revoquer la delegation des wallets selectionnes en un clic: Blitz envoie une transaction type-4 qui re-delegue vers l'adresse zero. Une fois confirmee, le wallet redevient un EOA pur.

Chaque wallet a revoquer a besoin d'un peu d'ETH pour le gas. Revoquer ne fait perdre aucun acces au wallet.

### OpenSea API Key par Wallet (facultatif, v1.9.15+)

Chaque wallet peut avoir sa **propre cle API OpenSea**. Utile pour bypass la limite OS (4 req/s par cle).

#### Pour quoi faire ?
- Quand 10 wallets minted un drop WL, ils doivent tous appeler l'API OS individuellement (signatures uniques par adresse)
- Avec 1 cle partagee: 4 req/s total = ~2.5s pour fetcher 10 wallets sequentiellement
- Avec 10 cles uniques: 40 req/s effectif = ~250ms total

#### C'est facultatif
Si tu ne set rien, le mint engine utilise:
1. La cle de la task (si tu en mets une dans le form OS Drop)
2. Sinon la cle globale (Settings > OpenSea API Key)

Per-wallet est **purement une optimisation throughput**. Sans, tout fonctionne normalement.

#### Set une cle par wallet
Click sur l'icone cle (a cote de Edit/Delete dans la table wallets). Si la cle est set, l'icone passe en vert. Modal demande la cle (password input, leave empty to clear).

#### Bulk paste (toolbar Wallets > icone cle)
Plus rapide pour set N cles d'un coup:
1. Colle une cle par ligne dans la textarea
2. Choisis le mode:
   - **In order**: chaque cle assignee au wallet correspondant (1er wallet -> 1ere cle, etc.). Si moins de cles que de wallets, le reste est inchange.
   - **Round-robin**: distribue les cles en boucle si tu en as moins que de wallets (ex: 3 cles / 12 wallets = 4 wallets par cle)
3. Click "Assign Keys"

#### Note importante
OpenSea **ne fournit pas** d'endpoint public pour generer des cles par programme. Tu dois les creer manuellement sur [docs.opensea.io](https://docs.opensea.io/reference/api-keys) (~5 cles par compte OS).

---

## 5. Creer une Task de Mint

### Creer une Task
1. Aller dans **Tasks**
2. Cliquer sur le bouton **+** (rond bleu)
3. Configurer via les 4 onglets:

### Onglet Default
| Champ | Description |
|-------|-------------|
| **Wallet Group** | Le groupe de wallets qui mint |
| **Function Name** | Le nom de la fonction du contrat (ex: `mint`, `publicMint`, `0xa0712d68`) |
| **Wallet(s)** | "All wallets in group" ou un wallet specifique |
| **Function Parameters** | Parametres de la fonction (ex: `2` pour mint 2 NFTs). Exclure le montant payable |
| **Gas Price Method** | Rapid / Custom / Custom+Priority / Manual (voir ci-dessous) |
| **Transaction Cost** | Montant en ETH a envoyer avec la TX (prix du mint) |
| **Contract Address** | Adresse du smart contract |

> Note: Le network (Ethereum/Base/Sepolia) est defini par le **task group**, pas par la task individuelle.

#### Gas Price Method en detail (v1.11.14+)

| Methode | Comportement |
|---|---|
| **Rapid** | `maxFee = 2 × base fee`, `priorite (tip) = base fee`. Agressif et competitif pour un mint, le tip suit le marche. |
| **Custom** | Tu entres un seul `Max Fee` (GWEI). La **priorite est automatiquement egale au Max Fee** - le tip effectif vaut `maxFee - base fee`, ce qui garantit l'inclusion tant que `maxFee > base fee`. |
| **Custom+Priority** | Tu entres `Max Fee` ET `Priority Fee` separement (controle total EIP-1559). |
| **Manual** | Gas limit fixe manuelle en plus du gas price. |

> Avant v1.11.14, "Custom" sans priorite laissait le tip au defaut ethers (~1 gwei), ce qui faisait perdre les courses de mint. Corrige : le tip suit maintenant le Max Fee.

### Onglet Advanced
| Champ | Description |
|-------|-------------|
| **Flashbots** | Toggle pour envoyer via Flashbots relay (Ethereum uniquement, TX privee) |
| **Schedule Start** | Date/heure pour auto-start de la task (countdown visible dans la colonne STATUS) |
| **Dead Blocks** | Nombre de blocks a attendre apres detection avant d'envoyer (protection anti-bot) |
| **Send on Pending** | Envoyer des que la TX est detectee en pending (plus rapide, plus risque) |
| **Spam Mode** | Re-envoyer la TX en boucle (avec interval et max retries) |
| **Custom ABI** | ABI JSON du contrat (optionnel, pour les fonctions complexes) |

### Onglet Flip (Snipe Mode)
| Champ | Description |
|-------|-------------|
| **Flip Mode** | Activer le mode snipe mempool |
| **Gas Limit Method** | Auto ou Manual |
| **Contract Owner** | Adresse du deployer qui va activer la vente |
| **Flip Function** | Nom de la fonction qui active le mint (ex: `flipSaleState`) |
| **Flip Value** | Valeur attendue (`*` = wildcard) |
| **Hybrid Mode** | Retry auto si le snipe echoue |

### Actions sur les Tasks
- **Play** (vert): lancer la task
- **Start All**: lancer toutes les tasks du groupe
- **Clone**: dupliquer une task avec tous ses parametres (nom + " (copy)")
- **Etherscan**: visible quand une task est terminee (success/partial/failed) avec un txHash - ouvre la page Etherscan de la TX
- **Batch Edit**: selectionner plusieurs tasks (checkboxes), un bouton "Batch Edit (N)" apparait pour modifier gas/contract en masse

### Lancer une Task
- Cliquer sur le bouton **Play** vert a cote de la task
- Ou cliquer sur **Start All** (play vert dans le header) pour lancer toutes les tasks

### Task Groups (Network par groupe)
Organisez vos tasks en groupes (ex: "Azuki Drop", "Free Mints").
- Cliquer sur **Create task group** dans le panneau gauche
- Choisir le **network** du groupe: **Ethereum** (bleu accent), **Base** (bleu #0052FF), ou **Sepolia** (jaune testnet)
- Toutes les tasks du groupe heritent du network choisi
- Les groupes sont sauvegardes entre les sessions
- Badge couleur a cote du nom du groupe indique le network

### Task Presets
Sauvegardez vos configurations de task frequentes:
- Dans le modal de creation, cliquer **Save Preset** apres avoir configure
- Les presets apparaissent comme badges cliquables pour pre-remplir le formulaire
- Supprimer un preset avec le bouton X

---

## 6. OpenSea Drop Mode

Pour les drops qui passent par OpenSea (la majorite des gros drops). Refondu en **streaming pipeline** depuis v1.9.15.

### Configuration
1. **API Key OpenSea**: Settings > OpenSea API Key (cle globale). Optionnel: cles par wallet pour bypass rate limits.
2. Creer une task > onglet **OpenSea Drop**

### Champs OpenSea
| Champ | Description |
|-------|-------------|
| **Collection Slug** | Le slug de la collection (ex: `pudgypenguins`). URL: `opensea.io/collection/slug` |
| **OpenSea API Key** | Cle API par task (optionnel, sinon utilise la cle globale ou per-wallet) |
| **Mint Price Override** | Optionnel. Le prix est auto-detecte depuis le contrat via `mintPrice()` / `price()` / `cost()` etc. Set seulement si l'auto-detection echoue. |
| **Quantity per Wallet** | Nombre de NFTs a mint par wallet (1-100) |
| **Wallet Group** | Le groupe de wallets qui mint. Inclut `All Wallets (N)` (v1.11.3+) pour creer une task sur tous les wallets cross-groupe. |
| **Gas Price Method** | Rapid, Custom, Custom+Priority |

### Comment ca marche (streaming pipeline)

Pour chaque wallet, un pipeline independant tourne en parallele:
1. **Poll OS API** `POST /drops/{slug}/mint` (throttle par cle a ~4 req/s)
2. **Direct probe** `estimateGas` sur le contrat tourne en parallele (court-circuit le poll si le contrat accepte)
3. Des qu'un wallet a sa TX data (200) -> envoie la TX **immediatement** sans attendre les autres
4. Pendant ce temps, les autres wallets continuent leurs polls/sends

**Pas de barriere de sync**: le wallet le plus rapide envoie en premier, sans etre bloque par les autres.

### Direct Mode (toujours actif)
Depuis v1.9.15, le direct mode tourne **toujours en parallele de l'API OS**:
- ABI fetched depuis Etherscan (auto, cache 1h)
- Prix auto-detecte depuis le contrat
- `estimateGas` race a ~200ms pour catch le drop des qu'il accepte
- **Public mint**: tous les wallets armes en local (sans API call) -> latence ~0ms
- **WL mint**: detection puis OS API pour les signatures par wallet

Le toggle "Direct Mode" de l'ancienne version est supprime (toujours ON est strictement mieux).

### Phase 422 - Wallet pas WL (fix v1.9.15)
Avant: si le wallet de test (premier de la liste) n'etait pas sur la WL, OS retournait 422 et le bot pollait a l'infini.

Maintenant: 422 = "drop est live, juste ce wallet pas eligible" -> bascule immediatement en fetch per-wallet. Les wallets WL'd recoivent 200 et minent, les autres failed proprement.

### Avantages
- Pas besoin de connaitre le contract address, l'ABI, ou la fonction de mint
- Gestion auto des Merkle proofs pour les allowlists
- Stage selection automatique
- Direct mode = bypass complet de l'OS API pour les public mints

### Erreurs courantes
| Erreur | Signification |
|--------|---------------|
| 409 | Le drop n'est pas encore actif (pas commence, termine, ou en pause) |
| 422 | Drop est live, mais ce wallet pas eligible (allowlist, max, etc.) - les autres wallets tentent quand meme |
| 404 | Slug incorrect, collection pas trouvee |
| 401 | Cle API invalide |
| 429 | Rate limit OS atteint - le bot backoff 2-3s automatiquement |

### Resilience RPC (v1.11.1+)
Quand plusieurs tasks tournent en parallele sur le meme contrat, le RPC peut etre sature (429 Alchemy CU/s, etc.). Le bot gere maintenant ca proprement:

- **JSON-RPC batching desactive** (`batchMaxCount: 1`) sur le provider ethers. Avant: ethers regroupait les appels concurrents en un seul HTTP, un 429 sur le batch faisait crash **toutes** les tasks en vol avec "could not coalesce error". Maintenant: chaque requete est isolee, un 429 ne contamine plus que la task qui l'a declenche.
- **Direct prober - backoff exponentiel** sur 429: 200ms -> 400 -> 800 -> 1600 -> 4000ms (cap), puis redescend par moitie sur les succes. Ralentit au lieu de pilonner.
- **Direct prober - exit propre** des que tous les wallets de la task ont fini (succes ou echec). Avant: le prober tournait 1h apres le mint reussi, bloquant la finalisation de la task et consommant le budget RPC pour les tasks suivantes.
- **Receipt wait resilient**: `txResponse.wait()` peut 429 pendant le polling du receipt alors que la TX est deja on-chain. Le bot bascule alors sur un polling manuel `getTransactionReceipt` avec backoff (1.5s -> 8s) pendant 5min. Plus de mint reussi marque "failed" a cause d'un 429.
- **Candidate pruning resilient**: au setup, le pruning des candidats mint retry 4x avec backoff sur 429 au lieu de drop les candidats valides juste parce que le RPC chouinait.

Si tu as encore des 429 frequents avec beaucoup de tasks paralleles: upgrade ton plan RPC (Alchemy Growth, etc.) ou reduis le nombre de tasks concurrentes sur le meme contrat.

### Throughput multi-cles
| Setup | Throughput WL fetch |
|---|---|
| 1 cle globale, 10 wallets | ~4 req/s -> 2.5s pour 10 wallets |
| 10 cles per-wallet, 10 wallets | ~40 req/s -> 250ms pour 10 wallets |

Pour gas wars sur WL: les per-wallet keys font la difference.

---

## 7. Gestion des Transactions

### Status des Tasks
| Status | Description |
|--------|-------------|
| **Waiting** | Task configuree, prete a etre lancee |
| **Scheduled** | Task programmee, countdown visible, auto-start a l'heure |
| **Running** | Task en cours d'execution |
| **Minting** | TX envoyees, en attente de confirmation |
| **Success** | Toutes les TX ont reussi |
| **Partial** | Certaines TX ont reussi, d'autres ont echoue |
| **Failed** | Toutes les TX ont echoue |

### Cancel TX
Quand une task est en status **Minting** (TX pending dans le mempool):
1. Cliquer sur le bouton **X** (cancel) dans la colonne ACTIONS
2. Blitz envoie une TX vide au meme nonce avec +30% de gas
3. Les miners remplacent la TX pending par la TX cancel
4. Resultat: la TX originale est annulee, vous payez juste le gas de la TX cancel

**Quand utiliser Cancel TX:**
- Vous avez envoye une TX avec trop peu de gas et elle est bloquee
- Le mint a ete revele comme un scam
- Vous avez change d'avis

### Speed Up TX
Quand une task est en status **Minting**:
1. Cliquer sur le bouton **>>** (speed up) dans la colonne ACTIONS
2. Blitz re-envoie la meme TX au meme nonce avec +50% de gas
3. Les miners prennent la TX avec le gas le plus eleve
4. Resultat: votre TX est confirmee plus rapidement

**Quand utiliser Speed Up:**
- Votre TX est stuck (gas trop bas pour la congestion actuelle)
- Vous voulez etre inclus dans le prochain block

---

## 8. Force Gas

Le champ **Force Gas** dans le header de la page Tasks permet d'overrider le gas de TOUTES les tasks en une fois.

### Utilisation
- Entrer une valeur unique: `50` (max fee = 50 GWEI)
- Entrer un format EIP-1559: `50/3` (max fee = 50, priority fee = 3 GWEI)
- Cliquer sur le bouton rouge **Force Gas**

Utile quand le gas spike pendant un mint et que vous voulez ajuster toutes les tasks rapidement.

---

## 9. Distributor & Recollector

Accessibles depuis le sous-menu **Wallets** dans la sidebar.

### Distributor (1-to-many)
Envoyer de l'ETH depuis un wallet source vers plusieurs wallets.
1. Selectionner le **wallet source** (celui qui a l'ETH)
2. Selectionner les **wallets de destination**
3. Entrer le **montant par wallet** (en ETH)
4. Cliquer sur **Distribute**

Cas d'usage: alimenter vos wallets de mint avant un drop.

### Recollector (many-to-1)
Recuperer l'ETH de plusieurs wallets vers un seul wallet.
1. Selectionner le **wallet de destination**
2. Selectionner les **wallets sources**
3. Cliquer sur **Recollect**

Chaque wallet source envoie `balance - gasCost` vers le wallet de destination.

Cas d'usage: apres un mint, recuperer l'ETH restant de vos burners.

---

## 10. Eligibility Check & Approvals

### Eligibility Check (Tools > Eligibility) - multi-phase + token-gate pre-check (v1.11.5+)
Verifier si vos wallets sont eligibles pour **toutes** les phases d'un drop OpenSea, **avant** que la phase ne demarre quand possible.

1. Entrer le **Drop Slug** (ex: `the-florentines`)
2. Entrer votre **API Key** OpenSea
3. Choisir **Wallet Group** (incl. `All Wallets` pour cross-groupe) ou wallets individuels
4. Cliquer **Check**

#### Comment ca marche (v1.11.5)
1. **Enumeration des stages** via `GET /api/v2/drops/{slug}` - recupere uuid, label ("FCFS: BAYC, Chimpers, …"), prix, start/end, max/wallet pour chaque phase. Source de verite pour l'ordre, les noms et le timing.
2. **Phase active** -> probe `POST /drops/{slug}/mint` par wallet -> vrai 200 (eligible) / 422 (pas eligible). Verite cote OS.
3. **Token-gated stages** (FCFS-style WL avec liste de collections):
   - **On-chain d'abord** : lit SeaDrop `getEnumeratedAllowedTokens` + `getTokenGatedDrop`, match les stages par `start_time` (+-60s), check `balanceOf` par wallet.
   - **Fallback heuristique** : si on-chain ne renvoie rien, parse le label "FCFS: name1, name2, …", resoud chaque nom via `/collections/{slug}` puis `/search?query=…`, check `balanceOf` -> cellule marquee `(heuristic)`.
4. **Public future** -> derive ✓ Eligible (pending phase) - ouverte a tous a l'ouverture.
5. **WL signed-mint sans liste dans le label** (ex: GTD personnel) -> ⌛ "Check when live" (l'API OS publique ne permet pas de pre-checker, l'endpoint que le frontend OS utilise n'est pas documente).

Une fois `stage_uuid` rejete par OS (`400 "Unknown field"` confirme sur tous les variants), le module bascule en mode `os-api-single` automatiquement : 1 seul probe par wallet pour la phase active, statuts derives pour les autres. Visible dans le banner ("Source: os-api-single · stage_uuid ignored").

#### Streaming progress (v1.11.5)
- Pas de cache : chaque re-check est frais.
- Barre de progression `done/total` cellules pendant le scan.
- Cellules remplies en live (pastille "...Probing" pulsante -> remplie au fur et a mesure).
- Logs cote main process (`npm run dev` terminal) :
  - `[eligibility] on-chain: found N token-gated stage(s) for SLUG with M gate contract(s)`
  - `[eligibility] resolved "NAME" -> SLUG -> 0x...`
  - `[eligibility] label-heuristic: phase "..." -> 7/12 gates resolved`

#### Lecture des resultats
- **Banner source** :
  - Vert "Drop is LIVE - current phase: X" = drop actif, vrai status par wallet pour la phase active
  - Bleu "Drop configured but not currently active" = drop pas demarre, statuts derives
  - Rouge "Drop not found" = slug incorrect
- **Phase strip** en haut : une carte par stage avec badge type, statut Live/Upcoming/Ended, prix, max/wallet, timing relatif.
- **Stats** : Checked / Any Phase / On Allowlist / Public Only / Not Eligible
- **Matrice wallet x phase** : pastilles ✓ (eligible) / ✓ pending (eligible quand la phase ouvre) / ✕ (pas eligible) / ⌛ (checkable when live) / ◔ (not started) / – (ended) / ? (unknown). Le texte secondaire indique la raison ("Holds 1 gate NFT(s): 0x8fe1...9cd8 (heuristic)" par exemple).

Pratique pour: voir avant le drop quels wallets sont sur la FCFS via NFT holdings (BAYC, Mfers, FWOG, etc.), savoir lequel utiliser pour la WL et lequel garder pour la public, valider que l'API key est OK pour le drop.

#### Limitations connues (v1.11.5)
- Les WL signed-mint personnelles (genre "GTD" sans liste dans le label) ne sont pas pre-checkables - l'endpoint OS frontend utilise n'a pas encore ete identifie (inspection DevTools Network requise).
- Le fallback label-heuristique resoud ~60% des noms sur Florentines FCFS (7/12) - les noms qui ne matchent pas le slug OS direct (Chimpers, Mundos, Fauvtoshi, Fugz, GVC) restent non resolus. Workaround temporaire : si tu sais qu'un wallet est WL via une de ces collections, fais confiance au mint plutot qu'au pre-check.
- ~2-3s de latence par stage token-gated a resoudre via labels (12 noms x 200ms sequentiel pour rester sous la rate-limit OS 4 req/s).

### Approvals (Tools > Approvals)
Donner l'approbation `setApprovalForAll` a un operateur (OpenSea Seaport, Blur) pour plusieurs wallets en batch.

1. Entrer le **Contract Address** de la collection NFT
2. Choisir l'**operateur** (Seaport 1.6 ou Blur) ou entrer une adresse custom
3. Selectionner les wallets
4. Cliquer **Approve**

---

## 11. Settings

| Setting | Description |
|---------|-------------|
| **Ethereum RPC** | Liste des RPCs configures. Ajouter/supprimer/tester |
| **OpenSea API Key** | Cle API globale pour le mode OpenSea Drop |
| **Etherscan API Key** | Cle API pour le Profit Calculator (scanne l'historique TX) |
| **Discord Webhook** | URL webhook Discord pour notifications de tasks (success/fail) |
| **Default Gas Strategy** | Max Fee et Priority Fee par defaut pour les nouvelles tasks |
| **Mempool Sniper Mode** | Active l'ecoute du mempool via WSS (necessite un RPC WSS) |
| **Auto Refresh Balances** | Actualise les balances des wallets automatiquement (toutes les 60s) |
| **Quick Task Settings** | Wallet group, wallets, gas method, auto-start par defaut. Si gas method = Custom/Custom+Priority, les champs **Max Fee / Priority Fee** apparaissent et sont appliques aux tasks Quick (v1.11.17) |
| **Export/Import Config** | Export JSON (sans private keys) / Import avec merge |
| **Logs** | Bouton Open Log Folder pour acceder aux fichiers de log |

---

## 12. Analytics & Gas Monitor

### Analytics
- **Total Mints**: nombre total de TX envoyees
- **Success Rate**: pourcentage de TX reussies
- **Total Spent**: estimation du gas depense
- **Avg Cost**: cout moyen par TX
- **Graphique 7 jours**: activite de mint sur la derniere semaine

### Gas Monitor
- **Gas Cards**: prix low/current/high en GWEI
- **Historique 30 blocks**: graphique du gas price sur les 30 derniers blocks
- **EIP-1559**: Base Fee, Max Fee, Priority Fee actuels
- **Gas Estimator**: calculer le cout en ETH avec custom gwei, nombre de wallets, gas used
- Actualisation automatique toutes les 15 secondes

---

## 13. Privacy Mode

Le bouton **eye** dans la barre du haut permet d'activer/desactiver le mode privacy.

### Ce qui est masque
- **Wallets**: adresses (seuls les 6 premiers caracteres restent visibles), balances, noms ENS
- **Tasks**: contract addresses
- **Wallet Groups**: balances totales dans le panneau gauche

### Utilisation
- Cliquer sur l'icone **eye** dans la Topbar (a cote de l'heure)
- L'icone devient bleu/accent quand le mode privacy est actif
- Re-cliquer pour desactiver

Utile pour partager votre ecran ou prendre des screenshots sans exposer vos adresses et balances.

---

## 14. Discord Notifications

Recevez une notification Discord quand une task se termine (success, failed, ou partial).

### Configuration
1. Creer un **webhook Discord** dans votre serveur (Parametres du salon > Integrations > Webhooks)
2. Copier l'URL du webhook
3. Aller dans **Settings** > **Discord Webhook**
4. Coller l'URL et sauvegarder

### Format des notifications
- **Embed** avec titre "Blitz - Task Success/Failed/Partial"
- Couleur: vert (success), jaune (partial), rouge (failed)
- Champs: Task ID, Status, Details (message d'erreur), TX Hash
- Timestamp automatique

Seuls les statuts finaux sont envoyes (pas les statuts intermediaires comme "running" ou "minting").

---

## 15. Scheduled Tasks

Programmez une task pour qu'elle demarre automatiquement a une heure precise.

### Configuration
1. Creer ou editer une task
2. Aller dans l'onglet **Advanced**
3. Remplir le champ **Drop Time** avec la date et l'heure du mint
4. Optionnel: configurer **Start Before** (en secondes) pour lancer la task X secondes avant le drop
5. Creer la task

### Comportement
- La task est creee avec le status **Scheduled**
- Un **countdown** s'affiche dans la colonne STATUS (ex: "2h 15m 30s (-5s)")
- Le countdown se met a jour chaque seconde
- Le `-Xs` indique le decalage "Start Before" configure
- Quand le timer atteint 0 (moins le decalage), la task demarre automatiquement
- Si l'app est fermee et rouverte apres l'heure prevue, la task se lance immediatement

### Start Before
Le champ **Start Before** permet de lancer la task quelques secondes avant l'heure du drop. Utile pour que la TX soit dans le mempool au moment exact du mint.
- Valeur par defaut: 5 secondes
- Exemple: Drop a 18:00:00, Start Before = 5s → task demarre a 17:59:55

Utile pour les mints qui commencent a une heure precise (ex: 18h UTC). Configurez tout a l'avance et laissez Blitz lancer au bon moment.

---

## 16. Profit Calculator

Calculez vos profits et pertes (PnL) sur vos trades NFT et ERC-20/memecoins, et generez une success card partageable.

### Acceder au Profit Calculator
**Tools > Profit Calculator** dans la sidebar.

### Prerequis
- **Etherscan API Key**: Aller dans **Settings** > **Etherscan API Key** et entrer votre cle
  - Obtenir une cle gratuite sur [etherscan.io/apis](https://etherscan.io/apis)
  - Limite: 5 requetes/seconde sur le tier gratuit

### Utilisation
1. Selectionner les **wallets** a scanner (par groupe ou individuellement)
2. Entrer le **contract address** du token/NFT
3. Choisir le type: **NFT** (ERC-721) ou **ERC-20** (coins/memecoins)
4. Cliquer **Calculate**

### Metriques Affichees

| Metrique | Description |
|----------|-------------|
| **Invested** | Total ETH depense pour acheter |
| **Secured** | Total ETH recu des ventes |
| **Realized P/L** | Profit/perte realise (Secured - Invested pour la portion vendue) |
| **Holding** | Valeur estimee des tokens/NFTs encore possedes (prix plancher OpenSea pour NFTs, DexScreener pour coins) |
| **Unrealized P/L** | Profit/perte non-realise (valeur holding - cout d'acquisition restant) |
| **Total P/L** | Realized + Unrealized |
| **ROI %** | Return on Investment en pourcentage |

### Position OPEN vs CLOSED
- **OPEN** (bleu): Vous possedez encore des tokens/NFTs de cette collection
- **CLOSED** (gris): Tout a ete vendu, position terminee

### Success Card
Apres le calcul, une **success card** est generee automatiquement:
- Image 800x600px avec vos stats de PnL
- Format `$SYMBOL` pour les coins, nom de collection pour les NFTs
- Badge OPEN/CLOSED, ROI %, profit pill vert/rouge

### Background Custom
- Cliquer sur **Change BG** pour choisir une image de fond personnalisee
- L'image est **sauvegardee automatiquement** (persistance via localStorage)
- Cliquer **Reset BG** pour revenir au fond par defaut
- Pas besoin de re-uploader a chaque session

### Telecharger la Card
Cliquer sur le bouton **Download** pour sauvegarder la success card en PNG.

### Detection NFT
- Blitz scanne les Transfer events ERC-721 via l'API Etherscan
- Detecte les achats/ventes en ETH natif ET en WETH (ventes OpenSea via Seaport)
- Prix plancher via l'API OpenSea V2

### Detection ERC-20/Memecoins
- Blitz scanne les transfers de tokens ERC-20
- Detecte les ventes via les transactions internes (ETH recu)
- Prix actuel via l'API DexScreener
- Affichage adapte pour les micro-caps (notation subscript pour les prix tres petits)

---

## 17. OpenSea Listing Tool

Listez et delistez vos NFTs directement sur OpenSea depuis Blitz.

### Acceder au Listing Tool
**Tools > Listing** dans la sidebar.

### Prerequis
- **OpenSea API Key**: configuree dans Settings ou disponible dans le champ global
- **RPC Ethereum**: pour les approvals et le delist (transactions on-chain)

### Lister des NFTs

1. Entrer le **Contract Address** de la collection et cliquer **Load**
2. La collection s'affiche avec le nom, slug, OS Fee (%) et Royalty (%)
3. Selectionner un **Wallet Group** (defaut: `All Wallets (N)` depuis v1.11.3, scan cross-groupe) et cliquer **Scan NFTs**
4. Les NFTs de vos wallets apparaissent dans la grille
5. Selectionner les NFTs a lister (cliquer dessus, ou All/None)
6. Configurer dans le panneau droit:

| Champ | Description |
|-------|-------------|
| **Price per NFT** | Prix de vente en ETH |
| **Ladder Listing** | Active le prix incrementiel (chaque NFT est plus cher que le precedent) |
| **Increment** | Augmentation de prix par NFT (ex: 0.01 ETH) |
| **Duration** | Duree du listing (1h a 1 mois) |
| **Royalties** | Toggle on/off (grise si enforced on-chain) |

7. Le **summary** affiche: nombre d'items, total, OS fee, royalties, et **You receive** (total net)
8. Cliquer **List X NFTs**

### NFTs deja listes
- Au scan, Blitz detecte automatiquement les NFTs deja listes sur OpenSea
- Ils apparaissent **en premier** dans la grille avec un **badge vert** affichant le prix
- Le label sous le token ID affiche "Listed" en vert

### Ouvrir un NFT sur OpenSea (v1.11.18)
Chaque vignette de la grille a un petit **bouton logo OpenSea** en bas a droite. Click -> ouvre la page OpenSea du NFT (`opensea.io/assets/ethereum/{contract}/{tokenId}`) dans le navigateur. Le click sur le logo n'active pas la selection de la carte.

### Delister des NFTs

1. Selectionner les NFTs listes (badge vert)
2. Le bouton rouge **Delist X NFTs** apparait
3. Cliquer pour envoyer une transaction on-chain de cancel (Seaport)
4. Le badge vert disparait apres le delist

### Approval automatique
Blitz verifie l'approval Seaport Conduit avant de lister. Si un wallet n'a pas approuve la collection, l'approval est envoyee automatiquement avant le listing.

### Ladder Listing
Le ladder listing permet de lister plusieurs NFTs avec des prix incrementiels:
- **Start Price**: prix du premier NFT
- **Increment**: augmentation par NFT suivant
- Exemple: Start 0.05, Increment 0.01 → 0.05, 0.06, 0.07, 0.08...
- L'apercu du range s'affiche sous le champ increment

### Wallet Chips
Si le groupe contient plusieurs wallets, des **chips** apparaissent pour filtrer par wallet. Cliquer sur un chip pour ne scanner que ce wallet.

---

## 18. Drop Discovery Tool

Disponible depuis Sidebar > **Tools > Drop** (v1.9.15+).

L'outil combine **3 sources de discovery** pour trouver les drops OpenSea en cours et a venir, avec un filtre OS-only qui elimine les fausses detections (Uniswap V4 NFT positions, ENS, etc.).

### Les 3 sources

| Source | Trouve quoi | Coverage |
|---|---|---|
| On-chain mint scan | Drops **en cours de mint** (Transfer events from `0x0`) | ~100% des drops live |
| SeaDrop monitor | Drops **upcoming** (configures mais pas demarres) | ~70-80% des drops OS |
| OS Featured | Drops mis en avant par OS | ~3 drops gates |

Combine: **~95% de coverage** des drops OS public.

### Filtre OS-only (kill false positives)
Chaque contrat candidat est verifie via `POST /api/v2/drops/{slug}/mint` avec un sentinel:
- `200` -> drop live
- `422` -> drop live avec phase restrictive (allowlist)
- `409` -> drop configure, pas demarre
- `404` -> pas un drop OS -> **exclu** du feed

C'est pour ca que tu ne vois plus de "Uniswap V4 Positions NFT" ou autres NFT spam dans le feed.

### UI - Onglets

**Drop Feed**: la grille des drops detectes
- Header avec filtres: All / Minting / Upcoming / OS Featured / Hidden
- Selector de scan range: ~40min / ~1.7h / ~3.5h / ~7h (blocs scannees on-chain)
- Bouton Scan pour refresh manuel (auto-refresh toutes les 2min sinon)
- Source status en bas: dots de couleur indiquant la sante de chaque source

**Lookup**: verifier un drop precis par slug
- Tape un slug OS (ex: `pudgypenguins`)
- Click "Check Drop"
- Voir le status: Live / Not started / No drop page / Active (restricted)

### Badges sur les cards

| Badge | Signification |
|---|---|
| `OS` (orange) | Drop featured par OpenSea (`/api/v2/drops`) |
| `OS ✓` (vert) | Drop verifie via probe mint endpoint (= drop OS legitime) |
| `SeaDrop` (violet) | Catched via on-chain SeaDrop event monitor (= upcoming drop config detecte tot) |
| `Minting` (vert pulsant) | Phase active maintenant |
| `Upcoming` (violet) | Configure, pas demarre |

### Progress bar
Lue directement depuis le contrat on-chain:
- **Current minted**: `totalSupply()` / `totalMinted()` / etc.
- **Max supply**: `maxSupply()` / `MAX_SUPPLY` / etc.
- Code couleur: vert <70%, orange 70-90%, **rouge >=90%** (bold)
- Si l'une des deux infos n'est pas dispo on-chain, **pas de bar** (mieux qu'un 100% menteur)

### Tri intelligent
1. Featured drops en haut
2. Live drops par mint progress DESC (les plus proches du sold-out remontent)
3. Upcoming drops par startTime ASC (le plus proche en premier)
4. Reste par recent TX count

### Hide / Unhide
Bouton **✕** en overlay top-right de chaque card. Click -> la card glisse vers la section Hidden.
- Le filtre "Hidden (N)" apparait dans la barre des qu'il y a des drops hidden
- Dans Hidden, le bouton devient **↩** pour unhide individuel
- Bouton "Unhide all" pour reset
- **Persiste entre restarts**: le contrat reste blackliste meme apres redemarrage

### + Create Task (bridge vers Tasks)
Bouton sur chaque card (highlighted accent si le drop est live, muted sinon):
1. Click -> Blitz navigate automatiquement vers la page Tasks
2. La modal de creation s'ouvre, pre-remplie avec:
   - `mode: opensea`
   - `openseaSlug: <slug du drop>`
   - `contract: <contract address>`
   - `name: Mint <collection name>`
3. Tu finalises (wallet group, gas, schedule) et tu valides

Workflow complet: discovery -> task creation en 2 clicks.

### SeaDrop monitor en detail
Le monitor scanne les events `PublicDropUpdated` sur le contrat SeaDrop 1.0 (`0x00005EA00Ac477B1030CE78506496e8C2dE24bf5`). Ces events contiennent:
- `startTime` / `endTime` du drop
- `mintPrice` (en wei)
- `maxTotalMintableByWallet`
- `feeBps`

Donc des qu'un projet **configure** son drop sur SeaDrop (souvent plusieurs heures/jours avant le start), Blitz le voit dans la section Upcoming avec countdown.

**Limite**: ~70-80% des drops OS public passent par SeaDrop. Les 20-30% restants (custom contracts, OS Studio nouveau systeme) apparaissent dans le feed des qu'ils mintent (via on-chain scan) avec quelques blocs de latence.

---

## 19. Activity Feed

Onglet **Feed** (v1.11.25+): l'activite de tous tes wallets agregee en un seul flux trie par date. Listings, ventes, achats, mints, offres, transferts de NFT et swaps de tokens.

### Lancer un scan

Le refresh est **manuel** (chaque wallet x chaine coute des requetes API). Avant de cliquer **Refresh**, definis le perimetre:
- **Groupe de wallets** a scanner (ou All Wallets)
- **Chaines**: cases Ethereum / Base
- **Historique** (menu deroulant 7 / 30 / 90 jours, defaut 30): profondeur remontee au **premier** scan d'un wallet uniquement

Une barre de progression affiche `done/total` pendant le scan.

### Fenetre glissante

Le premier scan d'un wallet remonte la profondeur choisie. Les scans suivants **repartent la ou le precedent s'est arrete** — l'historique n'est donc paye qu'une fois par wallet, les refresh suivants sont rapides et ne coutent que le nouveau. Les events s'accumulent dans un cache local (plafond 500) au lieu de repartir de zero.

Si le message **"historique partiel — relance Refresh"** apparait, une page de resultats etait pleine: relance simplement Refresh pour recuperer la suite.

Le bouton **Clear** vide le cache et les curseurs: c'est ce qu'il faut utiliser pour forcer un rescan complet depuis zero.

### Types d'events

| Badge | Signification |
|---|---|
| `Listed` | NFT mis en vente |
| `Sold` / `Bought` | Vente / achat de NFT |
| `Mint` | Mint recu |
| `Offer in` / `Offer out` | Offre recue / faite |
| `Cancelled` | Ordre annule |
| `NFT in` / `NFT out` | Transfert de NFT entrant / sortant |
| `Swap` | Swap de tokens (ex: `1.5 ETH -> 4200 USDC`) |
| `Token in` / `Token out` | Transfert de token sans contrepartie |

Onglets **All / NFT / Tokens** pour filtrer, plus une recherche par collection, token ou wallet. Chaque ligne est cliquable vers OpenSea ou Etherscan.

### D'ou viennent les donnees

Deux sources complementaires, scannees en parallele:
- **OpenSea** pour tout ce qui touche aux NFT. C'est la **seule** source possible pour les listings: les ordres Seaport sont off-chain, ils n'existent dans aucun bloc.
- **Etherscan** pour les swaps ERC-20, qu'OpenSea ne voit pas du tout.

**Filtre anti-spam**: un token qui arrive sans contrepartie, dans une TX que tu n'as pas initiee, est un airdrop non sollicite — il est coupe avant d'atteindre le feed. Tes vrais achats passent, puisqu'ils sont toujours inities par ton wallet.

**Limite**: Etherscan V2 en gratuit ne couvre qu'Ethereum. Sur Base, la partie NFT (OpenSea) fonctionne, mais les swaps de tokens n'apparaissent pas.

---

## 20. Quick Task

Accessible via **Ctrl+Shift+Q** ou icone Quick dans le header Tasks. Cree une task depuis une TX existante (URL Etherscan ou hash).

### Configuration prerequise (Settings > Quick Task)
- **Default Wallet Group**: groupe a utiliser
- **Default Wallets**: wallets specifiques dans ce groupe (cocher dans le multi-select)
- **Default Gas Method**: rapid / custom / customPriority. Avec custom/customPriority, des champs **Max Fee / Priority Fee** apparaissent et sont reellement appliques aux tasks creees (corrige en v1.11.17 - avant, ce reglage etait ignore).
- **Auto Start**: toggle pour lancer la task immediatement apres creation

### Utilisation
1. Copie une TX hash OU une URL Etherscan/Basescan d'un mint
2. Ouvre Quick Task (Ctrl+Shift+Q)
3. Paste dans le champ - **la validation se fait automatiquement** (plus de bouton "Parse TX" depuis v1.11.17)

Blitz va:
1. **Auto-detecter la chain** (essaie mainnet / base / sepolia)
2. **Fetcher l'ABI** du contrat via Etherscan V2 pour decode propre des args
3. **Afficher un preview**: contract, function signature complete, args decodes, value, from, chain detectee, qualite du decode (badges vert/orange/rouge)

### Badges qualite

| Badge | Signification |
|---|---|
| `ABI decoded` (vert) | Contract verifie sur Etherscan, decode complet |
| `Known selector` (orange) | Selector matche une liste hardcoded (mint/claim/etc.), decode partiel |
| `Raw selector only` (rouge) | Contract non verifie, fallback raw hex - le bot enverra la calldata brute |

Dans tous les cas, la task peut etre creee. Le hex selector raw est supporte (`buildTransaction` encode via `AbiCoder.defaultAbiCoder`).

### Creation
Click "Create Task":
- 1 task creee **par wallet selectionne dans Settings** (matches le pattern standard "1 task = 1 wallet")
- Tasks attribuees au groupe de tasks actuellement actif (pas orphan)
- `network` = chain detectee de la TX (pas du groupe actif)
- Si `autoStart: ON`, les tasks demarrent immediatement

Toast de confirmation: "Quick task created for N wallet(s) in 'Group X'".

### Cas d'usage
- Tu vois un degen mint un nouveau drop en live, tu paste sa TX -> Blitz cree 10 tasks identiques avec tes wallets
- Tu veux re-mint exactement la meme fonction qu'une TX historique
- Tu testes une fonction custom: paste la TX, le bot encode tes args proprement

---

## 21. Securite

### Chiffrement
- Toutes les cles privees sont chiffrees avec **AES-256-GCM**
- La cle de chiffrement est derivee du mot de passe maitre via **PBKDF2** (600 000 iterations, SHA-512)
- Le mot de passe maitre n'est JAMAIS stocke - seul son hash est conserve
- Comparaison du hash en temps constant (timing-safe) pour prevenir les timing attacks

### Stockage Local
- Les donnees sont stockees localement dans `electron-store` (fichier JSON chiffre)
- Aucune donnee n'est envoyee a un serveur externe
- Les cles privees ne quittent jamais votre machine (sauf pour signer les TX envoyees au RPC)

### Bonnes Pratiques
- Utiliser un mot de passe maitre fort et unique
- Ne JAMAIS partager votre fichier de donnees (`blitz-data.json`)
- Garder une copie de vos cles privees dans un endroit securise
- Utiliser des wallets dedies pour le minting (pas votre wallet principal)
- Tester d'abord sur Sepolia (testnet) avant Mainnet

---

## 22. Auto-Update

Blitz se met a jour automatiquement quand une nouvelle version est disponible.

### Comment ca marche
1. Au lancement, l'app verifie si une nouvelle version existe sur le serveur (apres 10s)
2. Si une update est trouvee, un **bandeau bleu** apparait sous la barre du haut: "Update vX.X.X available"
3. Cliquer **Download** pour telecharger la mise a jour en arriere-plan
4. Une fois telecharge: "Update ready - restart to install"
5. Cliquer **Restart Now** pour installer et relancer l'app
6. Le bandeau peut etre ferme avec le bouton **x** si vous voulez updater plus tard

### Verifier manuellement
L'update est automatique, mais elle se verifie aussi a chaque lancement de l'app.

---

## 23. FAQ / Troubleshooting

### "No RPC configured"
Vous n'avez pas ajoute de RPC ou aucun RPC n'est actif. Allez dans Settings > Add RPC.

### "Master password not set"
L'app n'est pas deverrouillee. Redemarrez et entrez votre mot de passe.

### Ma TX est bloquee (pending depuis longtemps)
Le gas est probablement trop bas. Utilisez le bouton **Speed Up** pour re-envoyer avec +50% de gas, ou **Cancel** pour annuler.

### "Wallet not in allowlist" (OpenSea)
Votre wallet n'est pas sur la whitelist du drop. Verifiez avec le projet que votre adresse est bien sur la liste.

### "Drop is not currently active" (OpenSea)
Le mint n'a pas encore commence ou est deja termine. Attendez l'heure de debut ou verifiez la page OpenSea de la collection.

### "Contract not live after 5 minutes" / la task reste sur "Waiting for contract to go live"
En mode manuel, Blitz fait un `estimateGas` en boucle jusqu'a ce que le contrat accepte le mint. Tant que ca revert, ce message s'affiche. Causes possibles :
- **Le drop n'est pas encore ouvert** -> attendre est normal.
- **Erreur permanente** (wallet a deja minte, sold out, max atteint) -> depuis v1.11.13 Blitz **decode le revert** et echoue immediatement avec la vraie raison (ex: "Cannot mint: this wallet has already minted") au lieu d'attendre 5 min.
- **Prix de mint incorrect** -> le champ Transaction Cost doit valoir exactement le prix du contrat.
- **RPC sature** (Alchemy free tier) -> le message devient "RPC slow - estimateGas timed out". Utilise un RPC payant ou attends que la charge baisse.

Les details (code de revert, nombre de tentatives) sont ecrits dans `blitz.log` (Settings > Open Log Folder) depuis v1.11.12+.

### Une task affiche "Failed" mais le mint a reussi on-chain
Bug corrige en v1.11.16 (`provider is not defined`) : le mint partait et reussissait, mais le suivi du receipt plantait et marquait la task "Failed" a tort. Mets a jour vers v1.11.16+. Si tu vois encore un faux "Failed", verifie le tx hash sur Etherscan avant de re-minter.

### Windows ou mon antivirus signale Blitz comme un virus

C'est un faux positif, mais il est attendu — voici pourquoi, honnetement.

Blitz n'est pas **signe numeriquement** (un certificat de signature de code coute plusieurs
centaines d'euros par an et demande une entite legale). Sans signature, Windows affiche
"Editeur inconnu" et les antivirus n'ont aucune reputation sur laquelle s'appuyer: chaque
nouvelle version est un binaire inconnu de plus.

S'ajoute le comportement de l'app, qui ressemble beaucoup a celui d'un voleur de cles quand on
le regarde sans contexte: elle chiffre et stocke des cles privees, contacte de nombreux
endpoints, et telecharge puis execute un nouveau binaire a chaque mise a jour. Les moteurs
heuristiques declenchent la-dessus.

**Installer malgre l'alerte:**
1. SmartScreen -> "Informations complementaires" -> "Executer quand meme"
2. Si Defender met le fichier en quarantaine: Securite Windows > Protection contre les virus >
   Historique > restaurer le fichier, ou ajouter une exclusion sur le dossier d'installation

**Verifier que ton fichier est le bon**: chaque release publie le SHA-256 de l'installeur.
Compare-le avant d'executer:
```powershell
Get-FileHash "Blitz-Setup-X.X.X.exe" -Algorithm SHA256
```
Si le hash ne correspond pas a celui de la release, **ne lance pas le fichier** — telecharge-le
a nouveau depuis la page de releases officielle.

> Ne desactive pas ton antivirus entierement. Une exclusion ciblee sur le dossier Blitz suffit.

### L'app ne demarre pas
- Lancer en tant qu'administrateur si necessaire
- Verifier qu'aucun antivirus ne bloque l'application (voir la question precedente)
- Reinstaller depuis le dernier Setup .exe

### Les balances ne s'affichent pas
- Verifier que votre RPC est fonctionnel (test dans Settings)
- Cliquer sur le bouton Balance Check dans la page Wallets
- Activer Auto Refresh Balances dans Settings

### Comment trouver les parametres d'un mint?
1. Aller sur **Etherscan** > adresse du contrat > onglet **Contract** > **Read Contract**
2. Chercher les fonctions de mint (`mint`, `publicMint`, `claim`, etc.)
3. Verifier le prix dans `mintPrice` ou `PRICE` (en wei, diviser par 10^18 pour ETH)
4. Le nombre max par wallet est souvent dans `maxMintPerWallet` ou `MAX_PER_TX`
5. Pour l'ABI: onglet Contract > ABI, copier le JSON

### Comment trouver le slug OpenSea?
L'URL de la collection est `opensea.io/collection/slug`. Le slug est la derniere partie de l'URL (ex: `pudgypenguins`, `azuki`, `boredapeyachtclub`).

### Raccourcis Clavier

| Raccourci | Action |
|-----------|--------|
| Ctrl+Shift+S | Start All Tasks |
| Ctrl+Shift+X | Stop All Tasks |
| Ctrl+Shift+Q | Quick Task |
| Ctrl+1 | Dashboard |
| Ctrl+2 | Tasks |
| Ctrl+3 | Wallets |
| Ctrl+4 | Settings |

---

## Contrôle à distance Discord (nouveau en v1.11.39)

Pilotez Blitz depuis votre téléphone : un bot Discord personnel tourne **dans l'app** et n'obéit qu'à votre compte. Le PC doit faire tourner Blitz (et être déverrouillé pour tout ce qui signe des transactions). Rien ne transite par un serveur tiers : le bot tourne chez vous.

### Setup (une fois, ~5 minutes)

**1. Créer le bot**
- Allez sur [discord.com/developers/applications](https://discord.com/developers/applications) → **New Application** → nommez-la (ex. « Blitz Remote ») → Create
- Menu de gauche → **Bot** → **Reset Token** → **copiez le token tout de suite** (il ne s'affiche qu'une fois — perdu ? refaites Reset)
- Aucun intent privilégié nécessaire : laissez *Presence*, *Server Members* et *Message Content* **désactivés**

**2. L'inviter sur votre serveur**
- Utilisez un **serveur privé où vous êtes seul** (créez-en un si besoin) — les réponses affichent vos noms de tasks et nombres de wallets
- Menu de gauche → **OAuth2** → *URL Generator* → cochez les scopes **`bot`** et **`applications.commands`** (aucune permission supplémentaire)
- Ouvrez l'URL générée dans le navigateur → choisissez votre serveur → Autoriser

**3. Récupérer votre User ID**
- Discord → Paramètres → **Avancés** → activez le **Mode développeur**
- Clic droit sur votre propre profil n'importe où → **Copier l'identifiant de l'utilisateur**

**4. Brancher dans Blitz**
- **Settings → Discord Bot Token** (collez le token) + **Discord User ID** (collez l'ID)
- Le bot se connecte en quelques secondes, sans redémarrer. Tapez `/` sur votre serveur : les commandes Blitz apparaissent (jusqu'à 1 min la première fois)

### Commandes

| Commande | Effet |
|---|---|
| `/status` | Résumé du moteur : tasks par statut, état verrouillé/déverrouillé |
| `/tasks` | Liste des tasks récentes avec leurs ids |
| `/osmint slug` | Crée + lance une task mode OpenSea. Le `slug` s'autocomplète depuis votre onglet Drops (drops live en premier). Groupe de wallets choisi automatiquement via vos tags de chaîne ; `qty` prend par défaut le max par wallet du drop |
| `/quicktask tx` | Quick Task depuis un hash de TX : parse le mint (chaîne auto-détectée), crée une task par wallet du groupe et les lance |
| `/start id` / `/stop [id]` | Démarre / arrête une task — l'`id` s'autocomplète avec les noms. `/stop` sans id arrête tout |

Le flux nominal fait deux taps : `/osmint` → choisir le drop dans la liste → envoyer. Les résultats arrivent par votre webhook Discord comme d'habitude.

**Sécurité** : le bot n'obéit qu'à un seul Discord User ID ; tout autre compte reçoit « Not authorized ». Le token du bot donne le contrôle complet de votre moteur — traitez-le comme une clé privée (ne le partagez jamais). Les commandes qui signent sont refusées tant que l'app est verrouillée.

### Dépannage

| Symptôme | Solution |
|---|---|
| Les commandes n'apparaissent pas en tapant `/` | Attendez ~1 min après la première connexion ; vérifiez que l'URL d'invitation avait **les deux** scopes `bot` et `applications.commands` (ré-invitez sinon) |
| Le bot est hors ligne | Token erroné ou app fermée — recollez le token dans Settings (un Reset Token invalide l'ancien) |
| « Not authorized. » | Le User ID dans Settings ne correspond pas à votre compte — recopiez-le avec le Mode développeur |
| « App is locked » | Déverrouillez Blitz sur le PC (mot de passe maître) |
| `/osmint` dit « No OpenSea API key » | Renseignez d'abord votre clé API OS dans Settings |
