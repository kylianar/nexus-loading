# NEXUS — Loading Screen

Écran de chargement HTML pour le serveur GMOD Nexus (DarkRP).
Recrée fidèlement la maquette `nexus-loading-1920x1080.png` **en version vivante** :
barre de progression réelle, nombre de joueurs et nom de map injectés par GMOD.

> Charte Nexus (couleurs/polices) alignée sur `cl_theme.lua` / `nexus_ui`.

---

## 1. Tester en local (aperçu)

Double-clique sur `index.html` → il s'ouvre dans ton navigateur.
La barre se remplit toute seule jusqu'à ~90 % (mode démo). **En jeu**, c'est GMOD
qui pilote la vraie progression jusqu'à 100 %.

---

## 2. L'héberger (OBLIGATOIRE)

GMOD charge le loading screen depuis une **URL publique http(s)** : il ne peut PAS
lire un fichier local. Il faut donc mettre `index.html` en ligne.

### Option A — GitHub Pages (gratuit, recommandé)
1. Crée un dépôt GitHub (ex. `nexus-loading`).
2. Ajoute `index.html` à la racine, commit/push.
3. Repo → **Settings → Pages** → Source = branche `main`, dossier `/root` → Save.
4. Ton URL devient : `https://TONPSEUDO.github.io/nexus-loading/`

### Option B — Hébergement web classique
Upload `index.html` sur n'importe quel hébergeur (Netlify, Vercel, OVH, un VPS avec
nginx, etc.). Récupère l'URL finale du fichier.

### Option C — LAN / dev seulement
Sur la même machine/LAN tu peux servir le dossier :
```
cd D:\GMOD-PRIME\loading-screen
python -m http.server 8080
```
URL = `http://IP-LAN-DE-TA-MACHINE:8080/` (ex. `http://192.168.1.20:8080/`).
⚠️ `localhost`/`127.0.0.1` ne marche que pour un client sur la même machine.

---

## 3. Activer sur le serveur

Dans `serveur-gmod-prime/garrysmod/cfg/server.cfg`, ajoute :
```
sv_loadingurl "https://TONPSEUDO.github.io/nexus-loading/"
```
Puis relance le serveur (ou `changelevel`). À la prochaine connexion d'un joueur,
ton loading screen Nexus s'affiche.

> Note : le loading screen s'affiche pour les joueurs qui **se connectent**, pas
> pour l'hôte d'un listen server. Teste avec un 2ᵉ client.

---

## 4. Personnalisation

Tout est en haut du `<script>` dans `index.html` :

| Réglage           | Rôle                                                              |
|-------------------|-------------------------------------------------------------------|
| `CONNECT_ADDR`    | Adresse affichée sous « connect » (ex. `play.nexus-rp.gg`)        |
| `LIVE_COUNT_URL`  | URL JSON `{ "players": 64 }` pour le compteur joueurs en direct   |
| `BGM_URL`         | Musique de fond : `.mp3` à côté de `index.html`, ou URL absolue (`""` = aucune) |
| `BGM_VOLUME`      | Volume de la musique, de `0` à `1` (défaut `0.35`)               |

### Musique de fond
Mets le fichier (ex. `quartier-nord.mp3`) **à la racine du dépôt, à côté de `index.html`**,
puis commit/push : il sera servi par GitHub Pages comme `index.html`. La musique joue en
boucle pendant le chargement et s'arrête à l'entrée en jeu (la page se ferme).
⚠️ Héberger un titre commercial sur un dépôt **public** expose à un retrait DMCA — sinon
héberge le `.mp3` ailleurs (CDN/hébergeur perso) et mets l'URL absolue dans `BGM_URL`.

- Les textes (titre, accroche, jobs, astuce) se modifient directement dans le HTML.
- Couleurs = variables CSS `:root` (mêmes valeurs que la charte Nexus).

### Compteur joueurs « EN LIGNE »
L'API loading screen de GMOD fournit le **max** de joueurs (via `GameDetails`),
mais **pas** le nombre connecté en temps réel. Deux choix :
- Laisser `LIVE_COUNT_URL = ""` → on affiche `—` puis le max (honnête).
- Fournir un petit endpoint JSON (GameTracker, API Steam, ou un script perso) →
  le vrai nombre s'affiche et se rafraîchit toutes les 10 s.

---

## Hooks GMOD utilisés
`GameDetails(name, url, map, maxplayers, steamid, gamemode)` ·
`SetFilesTotal(n)` · `SetFilesNeeded(n)` · `DownloadingFile(file)` · `SetStatusChanged(s)`
