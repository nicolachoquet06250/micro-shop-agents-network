---
name: payment-security
description: Invariants de sécurité applicables aux intégrations de paiement.
---

# Skill `payment-security`

- Conserver les secrets côté serveur.
- Ne jamais accepter le montant final du client comme vérité.
- Vérifier signatures et événements de webhooks.
- Utiliser l'idempotence sur les opérations sensibles.
- Stocker uniquement les identifiants nécessaires.
- Ne jamais journaliser des données de carte ou payloads sensibles complets.
- Gérer explicitement succès, refus, annulation et timeout.
- Utiliser les flux hébergés du provider lorsque l'architecture ne permet pas de backend sûr.
