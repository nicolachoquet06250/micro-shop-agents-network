---
name: promotion-engine
description: Modèle réutilisable pour promotions automatiques et codes promo, sans imposer les règles propres au projet.
---

# Skill `promotion-engine`

Ce skill fournit les primitives d'un moteur de promotions. Les règles concrètes d'un projet doivent provenir du prompt, de `PROJECT_SPEC` ou de `DOMAIN_MODEL`.

## Types de mécanismes

Distingue au minimum :

- **promotion automatique/spontanée** : appliquée sans saisie de code lorsque les conditions sont satisfaites ;
- **code promo** : avantage conditionné à un code fourni et validé.

Ne suppose pas que ces deux mécanismes sont cumulables ou exclusifs : cette règle appartient au projet.

## Dimensions configurables

Une règle peut, si le projet le demande, porter sur :

### Bénéfice

- pourcentage ;
- montant fixe ;
- prix fixe ;
- autre mécanisme explicitement demandé.

### Périmètre commercial

- produit ;
- variante ;
- catégorie ;
- panier ;
- autre scope explicitement défini.

### Audience

- tous les clients ;
- client(s) précis ;
- segment explicitement défini ;
- nouveaux clients selon la définition du projet ;
- autre audience issue du brief.

### Temporalité

- date/heure de début ;
- date/heure de fin ;
- activation/désactivation manuelle.

### Limitations

Si le prompt l'exige :

- limite globale d'utilisation ;
- limite par client ;
- minimum de panier ;
- quantité minimum ;
- contraintes de produits/catégories ;
- autres conditions métier.

## Éligibilité

Centralise la résolution dans une logique testable qui reçoit le contexte nécessaire, par exemple :

```text
resolveApplicableBenefits(customer, cart, products, context)
```

Cette logique doit vérifier uniquement les règles configurées dans le projet.

Ne duplique pas la résolution de promotion dans le front-office.

## Prix et sécurité

- Le client ne décide jamais du montant de remise final.
- Le serveur recalcule l'éligibilité et le montant lors du checkout.
- Une promotion affichée doit pouvoir être recalculée de manière déterministe côté serveur.
- Les règles privées ou personnalisées ne doivent pas être révélées à des clients non éligibles.

## Cumul et priorité

Le skill ne fixe aucune politique par défaut.

Le projet doit déterminer, lorsque pertinent :

- cumul promotion automatique + code promo ;
- cumul de plusieurs promotions ;
- cumul de plusieurs codes ;
- ordre d'application ;
- priorité ;
- meilleure remise ;
- exclusivité.

Si ces règles sont nécessaires mais absentes du brief, elles doivent être signalées comme décision métier manquante plutôt qu'inventées silencieusement.

## Persistance

Le modèle de données doit pouvoir représenter les scopes, audiences, dates, limites et états requis par le prompt sans multiplier des colonnes spécifiques sur `PRODUCT`.

## Back-office

Si les promotions/codes doivent être administrables, prévoir des opérations cohérentes de :

- création ;
- modification ;
- activation/désactivation ;
- suppression selon la politique du projet ;
- visualisation des règles et de leur portée.

Les champs exacts du formulaire dépendent du prompt projet.
