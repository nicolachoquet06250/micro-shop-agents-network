---
name: commerce-model
description: Noyau métier et données communs aux micro-boutiques.
---

# Skill `commerce-model`

## Entités minimales

Selon le besoin :
- Customer
- Product
- ProductVariant
- Category
- Cart
- Order
- OrderItem
- Payment
- Delivery
- Image
- ImageGroup
- SiteContent

## Invariants

- montant de commande calculé côté serveur ;
- produit actif et disponible ;
- quantité valide ;
- statut de paiement distinct du statut de commande ;
- transitions d'état explicites ;
- historique suffisant pour auditer une commande ;
- références stables pour les paiements et webhooks.
