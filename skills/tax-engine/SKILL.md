---
name: tax-engine
description: Primitives réutilisables pour le calcul et la gestion des taxes sans imposer de juridiction ni de taux.
---

# Skill `tax-engine`

Ce skill fournit un cadre de calcul fiscal. Les juridictions, taux, exemptions et règles applicables doivent venir du prompt ou de la configuration du projet.

## Principes

- Ne jamais inventer un taux de taxe.
- Ne jamais supposer un pays ou une juridiction fiscale.
- Distinguer les règles fiscales du simple affichage d'un prix.
- Les calculs définitifs doivent être effectués côté serveur.

## Concepts possibles

Selon le projet :

- taxe ;
- taux ;
- catégorie fiscale ;
- juridiction ;
- exemption ;
- prix TTC ;
- prix HT ;
- arrondi ;
- taxe sur livraison ;
- taxe par ligne ;
- taxe au niveau commande.

N'active que les concepts nécessaires au projet.

## Calcul

Le domaine doit définir explicitement :

- si les prix source sont TTC ou HT ;
- l'assiette ;
- le taux applicable ;
- l'ordre entre remise et taxe ;
- la stratégie d'arrondi ;
- les règles de localisation éventuelles.

Le skill ne choisit pas ces politiques.

## Snapshot de commande

Lorsqu'une commande est créée, conserve suffisamment d'informations pour que son calcul fiscal reste auditable même si les taux changent ensuite.

Selon le projet, cela peut inclure :

- taux appliqué ;
- montant de taxe ;
- base taxable ;
- catégorie fiscale ;
- juridiction.

## Back-office

Si la gestion des taxes est administrable, les écrans doivent refléter les concepts réellement retenus par le projet et effectuer une validation serveur.

Une suppression ne doit pas rendre incohérentes les commandes historiques.
