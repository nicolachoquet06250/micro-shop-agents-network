---
name: email-delivery
description: Règles communes pour les e-mails transactionnels.
---

# Skill `email-delivery`

- Transport côté serveur uniquement.
- Configuration par variables d'environnement.
- `.env.example` sans valeurs réelles.
- Séparer transport, templating et cas d'usage.
- Prévoir HTML + texte lorsque pertinent.
- Pièces jointes optionnelles si le besoin existe.
- Timeouts explicites.
- Gestion claire des erreurs fournisseur.
- Aucune donnée de paiement complète.
- Aucun token sensible non nécessaire dans le corps.
