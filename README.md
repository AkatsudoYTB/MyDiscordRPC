<div align="center">

# 🎮 MyDiscordRPC

**Affiche tout ce que tu fais — sites web et applis PC — sur ton profil Discord. Automatiquement.**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
![Windows](https://img.shields.io/badge/Windows-10%20%2F%2011-0078D6?logo=windows&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![Discord](https://img.shields.io/badge/Discord-Rich%20Presence-5865F2?logo=discord&logoColor=white)

🇫🇷 Français · [🇬🇧 English](README.en.md)

<!-- 👉 Ajoute ici une capture / un GIF de ta présence Discord + de l'extension : -->
<!-- ![Aperçu](docs/screenshot.png) -->

</div>

---

Affiche automatiquement sur ton profil **Discord** ce que tu fais dans ton
**navigateur** (YouTube, SoundCloud, n'importe quel site) **et** dans tes
**applications Windows** (FL Studio, VLC, Vocaloid, cmd…), avec le vrai logo,
le titre, une barre de temps, etc.

- 100 % **gratuit** et **open source** (MIT)
- **Aucune collecte de données**, tout reste local (sauf ce qui va à Discord)
- Windows 10 / 11

> Ce guide est **pour les débutants** : chaque étape est expliquée. Prends ton
> temps, il n'y a rien de compliqué.

---

# 📖 Sommaire

1. [C'est quoi, comment ça marche ?](#1-cest-quoi)
2. [Étape 1 — Installer Python](#2-python)
3. [Étape 2 — Télécharger MyDiscordRPC](#3-telecharger)
4. [Étape 3 — Installer l'application (dépendances)](#4-dependances)
5. [Étape 4 — Créer ton application Discord](#5-discord)
6. [Étape 5 — Installer Cloudflared (pour les logos d'applis)](#6-cloudflared)
7. [Étape 6 — Régler `settings.json`](#7-settings)
8. [Étape 7 — Lancer l'application](#8-lancer)
9. [Étape 8 — Installer l'extension navigateur](#9-extension)
10. [Étape 9 — Régler les options dans l'extension](#10-options)
11. [Problèmes fréquents](#11-problemes)

---

<a name="1-cest-quoi"></a>
## 1. C'est quoi, comment ça marche ?

MyDiscordRPC a **deux parties** qui fonctionnent ensemble :

- **Une extension** pour ton navigateur (Chrome/Edge) : elle voit sur quel site
  tu es, quelle vidéo tu regardes, etc.
- **Une application** sur ton PC : elle voit quelle appli Windows est ouverte, et
  elle parle à Discord pour afficher ta présence.

```
Navigateur (extension) ─┐
                        ├─► Application PC ─► Discord
Applications Windows ───┘
```

Tu dois donc installer **les deux**. C'est ce que ce guide explique.

---

<a name="2-python"></a>
## 2. Étape 1 — Installer Python

**C'est quoi Python ?** C'est un logiciel gratuit et **officiel**, très utilisé,
qui permet de faire tourner des programmes (dont MyDiscordRPC). Ce n'est pas un
virus : il est développé par une fondation reconnue et téléchargé par des
millions de personnes. Tu peux l'installer en confiance.

1. Va sur le site officiel : **https://www.python.org/downloads/**
2. Clique sur le gros bouton **« Download Python 3.x.x »** (la dernière version).
3. Ouvre le fichier téléchargé.
4. **TRÈS IMPORTANT** : sur le premier écran de l'installateur, coche la case en
   bas **« Add python.exe to PATH »** (Ajouter au PATH). ✅
   *(Sans ça, les commandes ne marcheront pas.)*
5. Clique sur **« Install Now »** et laisse faire.

**Vérifier que ça marche** : ouvre le menu Démarrer, tape `cmd`, ouvre l'**Invite
de commandes**, et tape :
```
python --version
```
Tu dois voir `Python 3.x.x`. Si tu vois une erreur, réinstalle en cochant bien
« Add to PATH ».

---

<a name="3-telecharger"></a>
## 3. Étape 2 — Télécharger MyDiscordRPC

1. Sur la page GitHub du projet, clique sur le bouton vert **« Code »** →
   **« Download ZIP »**.
2. Décompresse le ZIP où tu veux, par exemple sur le Bureau. Tu obtiens un
   dossier `MyDiscordRPC` avec dedans `desktop-app`, `browser-extension`, etc.

*(Si tu connais Git : `git clone <url-du-repo>`.)*

---

<a name="4-dependances"></a>
## 4. Étape 3 — Installer l'application (dépendances)

On va préparer l'application PC. Ouvre l'**Invite de commandes** dans le dossier
`desktop-app` :
- ouvre le dossier `MyDiscordRPC\desktop-app` dans l'explorateur,
- clique dans la barre d'adresse, tape `cmd` et appuie sur Entrée.

Puis tape ces commandes **une par une** :

```bash
python -m venv .venv
```
```bash
.venv\Scripts\pip install -r requirements.txt
```

La première crée un « environnement » isolé, la seconde installe les morceaux
nécessaires (Discord, détection Windows…). Attends que ça finisse.

---

<a name="5-discord"></a>
## 5. Étape 4 — Créer ton application Discord

**Pourquoi ?** Discord a besoin de savoir « qui » affiche ta présence. Pour ça,
on crée une petite « application » Discord (gratuit, 2 minutes) et on récupère
son **numéro (Application ID)**.

> ℹ️ **Pas besoin de se connecter/OAuth** : le Rich Presence local n'a besoin que
> de ce numéro. (PreMiD paraît « sans création d'appli » parce que **ses
> créateurs ont déjà créé les applications et fournissent les numéros**. Si tu
> distribues ce projet, tu peux faire pareil : mets ton numéro par défaut dans
> `desktop-app/config/default_settings.json` (champ `client_id`) → les gens qui
> téléchargent n'auront **rien à créer**.)

Étapes :
1. Va sur **https://discord.com/developers/applications** et connecte-toi.
2. En haut à droite, clique **« New Application »**.
3. Donne un nom (ce nom s'affichera **en gros** sur ton profil, ex. `MyDiscordRPC`
   ou ton pseudo), coche les conditions, valide.
4. Tu arrives sur **« General Information »**. Repère **« Application ID »** : un
   long nombre. Clique **« Copy »** pour le copier.
5. Garde-le de côté, on le colle à l'étape 6 (ou plus tard dans l'extension).

*(C'est tout : pas de bot, pas de secret, pas d'autorisation.)*

---

<a name="6-cloudflared"></a>
## 6. Étape 5 — Installer Cloudflared (pour les logos d'applis)

**À quoi ça sert ?** Pour afficher le **logo de tes applis PC** (FL Studio, VLC…)
sur Discord, il faut que Discord puisse « aller chercher » l'image. Cloudflared
crée un petit lien public temporaire vers ton PC pour ça. C'est **gratuit et sans
compte**.

> Cette étape est **optionnelle** : sans elle, tout marche, mais les applis PC
> s'afficheront **sans logo** (juste le nom + le projet).

1. Va sur **https://github.com/cloudflare/cloudflared/releases** (dernière version).
2. Télécharge le fichier **`cloudflared-windows-amd64.exe`**
   (`amd64` = PC Windows 64 bits, le cas normal).
3. Crée un dossier **`C:\cloudflared`**.
4. Mets le fichier dedans et **renomme-le** simplement `cloudflared.exe`
   (donc au final : **`C:\cloudflared\cloudflared.exe`**).

*(On indiquera ce chemin à l'étape suivante.)*

---

<a name="7-settings"></a>
## 7. Étape 6 — Régler `settings.json`

Au **premier lancement** (étape 7), un fichier `desktop-app\config\settings.json`
est créé. Ouvre-le avec le Bloc-notes et renseigne :

```json
{
  "discord": { "client_id": "COLLE_TON_APPLICATION_ID_ICI" },
  "icons": {
    "tunnel": "cloudflared",
    "cloudflared_path": "C:\\cloudflared\\cloudflared.exe"
  }
}
```

- `client_id` : le numéro copié à l'étape 4.
- `cloudflared_path` : le chemin de l'étape 5 (garde les **doubles antislashs** `\\`).

Enregistre. *(Tu peux aussi régler l'ID Discord directement depuis l'extension —
voir étape 9 — sans toucher au fichier.)*

---

<a name="8-lancer"></a>
## 8. Étape 7 — Lancer l'application

1. **Lance Discord** (l'appli de bureau, pas la version navigateur — elle doit
   tourner).
2. Dans l'Invite de commandes (dossier `desktop-app`), tape :
```bash
.venv\Scripts\python.exe run.py
```
3. Laisse cette fenêtre **ouverte** (elle fait tourner l'app). Tu dois voir des
   lignes comme `Connecté à Discord`, `Serveur WebSocket local…`, et si
   cloudflared est configuré `URL publique des icônes (Cloudflare) : https://…`.

Pour arrêter : `Ctrl + C` dans la fenêtre.

---

<a name="9-extension"></a>
## 9. Étape 8 — Installer l'extension navigateur

1. Ouvre ton navigateur et va sur **`chrome://extensions`** (ou
   **`edge://extensions`** pour Edge).
2. En haut à droite, active le **« Mode développeur »**.
3. Clique **« Charger l'extension non empaquetée »**.
4. Sélectionne le dossier **`browser-extension`** (celui qui contient
   `manifest.json`).
5. L'icône MyDiscordRPC (carré violet ▶) apparaît. Les **options s'ouvrent
   automatiquement** la première fois.

*(Après chaque mise à jour du code, reviens sur `chrome://extensions` et clique le
bouton ⟳ sur MyDiscordRPC.)*

---

<a name="10-options"></a>
## 10. Étape 9 — Régler les options dans l'extension

Clique l'icône de l'extension → **⚙ Paramètres**. Tu as plusieurs onglets :

- **Guide** : un rappel des étapes.
- **Discord** : colle ton **Application ID** ici (si tu ne l'as pas mis dans
  `settings.json`) → **Enregistrer**. Le badge en haut passe au **vert** quand
  l'app est connectée.
- **Indicateur** : la petite popup sur les pages — activer/désactiver, position
  (6 choix), animation, durée, **son** (dont un son perso par fichier). Elle
  apparaît à l'ouverture d'un site et au changement d'onglet.
- **Sites** : afficher les sites en **domaine** (`youtube.com`), en **nom**
  (`YouTube`) ou en **chemin complet** ; + une **liste de sites à ne jamais
  afficher**.
- **Thèmes** : coller un **thème** (JSON) partageable pour tout personnaliser.

**Choisis ta langue** en haut à droite (English / Français).

---

<a name="11-problemes"></a>
## 11. Problèmes fréquents

| Souci | Solution |
|---|---|
| `python n'est pas reconnu` | Réinstalle Python en cochant **« Add to PATH »**. |
| Badge rouge « App non connectée » | L'app (`run.py`) tourne-t-elle ? Discord est-il lancé ? Bon Application ID ? |
| Rien sur Discord | Ouvre ton profil Discord (clique ton avatar) ; active *Paramètres → Confidentialité de l'activité → Partager mon activité*. |
| Pas de logo d'appli | Cloudflared est-il lancé ? Regarde la ligne `URL publique des icônes` dans les logs. |
| L'extension ne réagit plus après mise à jour | `chrome://extensions` → ⟳ sur MyDiscordRPC. |
| Le bouton « Regarder sur YouTube » | Normal : Discord **cache tes propres boutons**, seuls tes amis les voient. |

---

## Pour aller plus loin

- Afficher **« YouTube » en gros** (au lieu du nom de ton app) :
  [docs/04-nom-par-plateforme.md](docs/04-nom-par-plateforme.md)
- Détection des applications Windows (+ logos) :
  [docs/05-applications-windows.md](docs/05-applications-windows.md)
- Options & thèmes de l'extension :
  [docs/06-options-extension.md](docs/06-options-extension.md)
- Architecture technique : [docs/01-architecture.md](docs/01-architecture.md)

## Licence

MIT — voir [`LICENSE`](LICENSE).
