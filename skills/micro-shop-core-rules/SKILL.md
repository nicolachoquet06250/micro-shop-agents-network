---
name: micro-shop-core-rules
description: Invariants communs à tous les agents de génération de micro-boutique.
---

# Skill `micro-shop-core-rules`

## Invariants

- Le projet est `local-only` par défaut.
- Toute boutique générée doit être agentifiée avant validation finale : `AGENTS.md` canonique, adaptateurs d'orchestrateur et réseau complet d'agents/skills/contrats embarqué selon `project-agentification`.
- Ne déclenche aucun hébergement ou déploiement public sans demande explicite.
- Ne commit aucun secret réel.
- N'expose aucun secret serveur côté client.
- Préserve le dépôt existant, son architecture, son gestionnaire de paquets et son lockfile.
- Limite les modifications au périmètre demandé.
- Toute création ou modification passe par `micro-shop-testing-engineer` : exigences/cas/tests documentés dans `.ai/testing/`, tests unitaires et E2E exécutables, correction du code jusqu'à réussite, puis suite complète verte et couverture unitaire d'au moins 90 % selon `testing-engineer`. Un blocage n'autorise aucune validation finale.
- Toute action UI doit fonctionner.
- Les validations serveur sont obligatoires pour toute donnée sensible.
- Les prix et totaux doivent être recalculés côté serveur lorsqu'un backend existe.
- Les erreurs utilisateur doivent rester compréhensibles sans divulguer d'informations internes.
- Les dépendances installées doivent utiliser une version explicite compatible.
- Les types associés doivent être ajoutés lorsque l'écosystème l'exige.
- Respecte les conventions de formatage du dépôt.
- Lorsque le projet l'impose, utilise CRLF pour les fichiers générés.
