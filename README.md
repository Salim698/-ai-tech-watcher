# 🗞️ AI Tech Watcher

Agent d'automatisation n8n qui surveille automatiquement plusieurs sources tech, analyse et résume les nouveautés avec l'IA (Gemini), classe leur importance, puis livre un rapport chaque matin sur **Telegram** et par **email**.

> 100% automatique. Aucune intervention manuelle une fois configuré.

## 📸 Aperçu

<!-- Remplace ces lignes par tes propres captures d'écran, glissées dans /screenshots -->
| Vue du workflow | Rapport reçu |
|---|---|
| ![Workflow](screenshots/workflow.png) | ![Rapport](screenshots/rapport.png) |

## ⚙️ Comment ça marche

```
Cron (quotidien)
   │
   ▼
8 flux RSS  (OpenAI, Hugging Face, Google AI, n8n, GitHub,
             The Hacker News, AWS, Google Cloud)
   │
   ▼
Nettoyage : déduplication + filtre "dernières 24h"
   │
   ▼
Gemini : résumé + classement d'importance (🔴 critique / 🟠 important / ⚪ mineur)
   │
   ▼
Génération du rapport (Markdown + HTML)
   │
   ├──▶ Telegram (découpé automatiquement si trop long)
   └──▶ Email (SMTP, HTML)
```

## 🧱 Stack technique

- [n8n](https://n8n.io) (self-hosted) — orchestration du workflow
- Flux RSS — collecte des sources
- [Gemini](https://ai.google.dev) — analyse et résumé IA
- API Telegram (Bot) — notification instantanée
- SMTP — envoi d'email HTML

## 🚀 Installation

### 1. Prérequis
- Une instance n8n (self-hosted ou cloud)
- Une clé API [Google AI Studio](https://aistudio.google.com) (gratuite)
- Un bot Telegram ([créé via @BotFather](https://t.me/BotFather))
- Un compte email avec accès SMTP (ex. Gmail + [mot de passe d'application](https://myaccount.google.com/apppasswords))

### 2. Importer le workflow
1. Télécharge [`workflow/AI_Tech_Watcher.json`](workflow/AI_Tech_Watcher.json)
2. Dans n8n : **⋯ (menu) → Import from File**

### 3. Configurer les identifiants
Dans le workflow importé, remplis :

| Nœud | Champ | Valeur |
|---|---|---|
| `Gemini – Analyse` | header `x-goog-api-key` | ta clé Google AI Studio |
| `Envoi Telegram` | `chatId` + credential bot | ton chat ID + token du bot |
| `Send an Email` | `fromEmail` / `toEmail` + credential SMTP | tes identifiants email |

### 4. Activer
Active le workflow. Il se déclenche automatiquement chaque jour à 8h (configurable dans le nœud `Cron`).

## 🎯 Problème résolu

Faire une veille technologique sérieuse demande de consulter quotidiennement plusieurs sources et de trier le bruit de l'important — un travail répétitif de 30 à 60 minutes par jour. Ce workflow ramène ce temps à quelques minutes de lecture d'un rapport déjà trié et résumé.

## 🔄 Adapter à d'autres cas d'usage

Le squelette (collecte → nettoyage → analyse IA → priorisation → notification) se réutilise tel quel pour :
- Veille concurrentielle (prix, stock)
- Veille immobilière (nouvelles annonces filtrées)
- Veille RH (offres d'emploi concurrentes)
- Veille réglementaire/juridique
- Veille de réputation de marque

Pour la démarche complète (problème résolu, défis techniques rencontrés, apprentissages), voir [`docs/Fiche_Projet.md`](docs/Fiche_Projet.md).

## 📺 Démo vidéo

<!-- Remplace par le lien de ta vidéo -->
🎥 [Voir la vidéo de construction complète](#)

## 📄 Licence

Ce projet est sous licence MIT — libre d'utilisation, de modification et de redistribution. Voir [`LICENSE`](LICENSE).

## 🙋 Contact

<!-- Ajoute tes liens (LinkedIn, portfolio, email...) -->
