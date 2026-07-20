# AI Tech Watcher — Fiche projet

## 📌 Description du projet

AI Tech Watcher est un agent d'automatisation qui surveille en continu 8 sources tech de référence (OpenAI, Hugging Face, Google AI, GitHub, AWS, n8n, The Hacker News, Google Cloud), identifie les publications des dernières 24h, les fait analyser et résumer par une IA, puis livre un rapport structuré et priorisé directement sur Telegram et par email — sans aucune intervention manuelle.

Le workflow tourne automatiquement chaque matin (Cron 8h) et suit un pipeline en 6 étapes : collecte multi-sources → nettoyage/déduplication → filtrage temporel → analyse IA → classement par importance → distribution multicanal.

## 🎯 Problème résolu

Faire une veille technologique sérieuse demande de consulter quotidiennement de multiples sources, de trier le bruit de l'information réellement importante, et de synthétiser — un travail répétitif qui prend facilement 30 à 60 minutes par jour pour quelqu'un qui veut rester à jour (développeur, dirigeant tech, consultant, agence).

AI Tech Watcher supprime cette charge manuelle : la personne reçoit chaque matin un rapport déjà trié (🔴 critique / 🟠 important / ⚪ mineur), déjà résumé, sans avoir à ouvrir un seul site. Le temps de veille passe de ~40 minutes à 2 minutes de lecture.

Au-delà du cas tech, le problème sous-jacent — *« je dois surveiller manuellement une source d'information changeante et en extraire ce qui compte »* — est un problème générique qui touche énormément de métiers (voir plus bas).

## 🛠️ Technologies utilisées

- **n8n** (self-hosted, v2.17.7) — orchestrateur du workflow, sans code
- **Flux RSS** — collecte des publications (8 sources)
- **Gemini (Google AI)** — analyse, résumé et classement d'importance des articles, avec sortie JSON structurée forcée
- **JavaScript (nœuds Code n8n)** — déduplication, filtrage temporel, parsing de la réponse IA, génération du rapport (Markdown + HTML), découpage anti-limite Telegram
- **API Telegram (Bot)** — notification instantanée
- **SMTP (Gmail, mot de passe d'application)** — envoi du rapport par email en HTML
- **Cron / Schedule Trigger** — exécution automatique quotidienne

## 🐛 Défis techniques rencontrés & solutions

| Défi | Solution |
|---|---|
| Quota Gemini bloqué à `limit: 0` (compte non validé) | Diagnostic de la cause réelle (pas un simple rate-limit) → bascule vers Groq comme fournisseur IA alternatif, gratuit et sans carte bancaire |
| Connexion Gmail OAuth2 invalide/expirée en self-hosted | Remplacement de l'authentification OAuth (fragile en self-hosted, tokens expirés en mode Test) par SMTP avec mot de passe d'application — solution plus stable pour un environnement auto-hébergé |
| Message Telegram rejeté (`message is too long`, limite 4096 caractères) | Découpage automatique du rapport en plusieurs messages, avec regroupement intelligent par blocs pour ne jamais couper un article en deux |
| Comprendre pourquoi peu d'articles ressortent malgré des flux RSS volumineux (jusqu'à 1000+ items par flux) | Identification que les flux RSS renvoient tout l'historique disponible, pas uniquement les nouveautés — le filtre 24h est le véritable point de contrôle du volume final |

Ces itérations illustrent une réalité importante : un workflow d'automatisation fonctionnel se construit par cycles de test-erreur-correction, pas en un seul passage.

## 📚 Ce que tu as appris

- Diagnostiquer une erreur d'API en lisant le détail (`limit: 0` ≠ un simple dépassement de quota) plutôt que de réagir au message générique
- Concevoir un pipeline robuste face à la panne : gestion d'erreur par nœud (`continueRegularOutput`) pour qu'une source RSS morte ne bloque pas tout le workflow
- Forcer une IA à produire une sortie exploitable (JSON strict) plutôt que du texte libre à parser à l'aveugle
- Anticiper les limites techniques des plateformes tierces (taille max de message Telegram, expiration des tokens OAuth) avant qu'elles ne cassent un système en production
- Choisir la solution la plus **fiable dans la durée** plutôt que la plus rapide à mettre en place (SMTP vs OAuth pour un usage self-hosted)
- Documenter et communiquer un processus technique, y compris ses échecs, comme preuve de compétence réelle (plutôt que montrer uniquement un résultat final lisse)

## 💰 Comment ce projet pourrait être vendu comme service

Le squelette du workflow (collecte → nettoyage → analyse IA → priorisation → distribution) est réutilisable tel quel pour de nombreux cas d'usage payants :

- **Veille concurrentielle pour commerçants/e-commerçants** : surveillance des prix et du stock des concurrents, alerte dès qu'un prix baisse
- **Veille immobilière** : nouvelles annonces multi-plateformes filtrées selon des critères précis (zone, budget, type de bien)
- **Veille RH/recrutement** : suivi des offres d'emploi concurrentes pour du benchmark salarial
- **Veille réglementaire/juridique** : suivi des évolutions légales dans un secteur donné, avec résumé exécutif
- **Veille de réputation** : surveillance des mentions de marque sur le web et réseaux sociaux, avec alerte sur sentiment négatif

**Modèle économique envisageable :**
- *Setup unique* : 300–1500€ selon complexité (nombre de sources, logique de filtrage, canaux de sortie)
- *Abonnement mensuel* : 50–300€/mois pour la maintenance, l'hébergement, et les ajustements de sources
- *Offre freemium* : version basique en démo gratuite (ce qui rend cette vidéo elle-même un outil commercial), version complète vendue sur devis

L'argument de vente clé : le client ne paie pas pour "un workflow n8n", il paie pour du temps récupéré et de meilleures décisions prises plus vite — c'est ce cadrage business qu'il faut mettre en avant, pas la mécanique technique.
