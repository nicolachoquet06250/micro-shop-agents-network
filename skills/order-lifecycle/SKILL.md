---
name: order-lifecycle
description: Cadre réutilisable pour états, transitions, historique et actions d'une commande.
---

# Skill `order-lifecycle`

Ce skill fournit les primitives d'un cycle de vie de commande. Les états exacts et les transitions autorisées dépendent du prompt projet.

## Séparer les dimensions

Lorsque le projet le justifie, ne confonds pas :

- statut métier de la commande ;
- statut du paiement ;
- statut de préparation ;
- statut d'expédition/livraison ;
- statut de remboursement.

Un seul champ générique ne suffit pas toujours.

## Machine d'état

Les transitions doivent être explicites et testables.

Exemple de principe :

```text
current_state + action + context -> next_state | refusal
```

Le skill ne fixe pas la liste universelle des états.

## Actions

Selon le prompt, une commande peut supporter :

- validation ;
- paiement ;
- préparation ;
- expédition ;
- livraison ;
- annulation ;
- remboursement ;
- autre transition métier.

Chaque action doit vérifier les préconditions serveur avant mutation.

## Historique

Conserve un historique des transitions importantes avec au minimum :

- commande ;
- ancien état si pertinent ;
- nouvel état ;
- date ;
- origine/auteur lorsque nécessaire ;
- motif lorsque le projet le demande.

## Annulation

La possibilité d'annuler, ses conditions, ses conséquences sur le stock, le paiement et le remboursement doivent provenir du prompt ou du domaine du projet.

Ne déduis pas une politique universelle d'annulation.

## Snapshot

Une commande doit conserver les informations commerciales nécessaires à son historique :

- lignes ;
- quantités ;
- prix unitaires appliqués ;
- remises appliquées ;
- taxes appliquées ;
- autres montants nécessaires au projet.

Les modifications ultérieures d'un produit ne doivent pas réécrire l'historique économique d'une commande existante.
