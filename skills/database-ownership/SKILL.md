---
name: database-ownership
description: Règles d'ownership, migrations et synchronisation lorsqu'une base est utilisée par plusieurs runtimes, ORM ou services.
---

# Skill `database-ownership`

Utilise ce skill lorsqu'une même base ou un même domaine de données est accessible via plusieurs services, langages ou ORM.

## Objectif

Éviter :

- deux sources de vérité concurrentes ;
- migrations incompatibles ;
- écritures contradictoires ;
- divergence de schéma ;
- transactions impossibles à garantir ;
- modèles métier dupliqués sans contrat.

## Décisions obligatoires

Le projet doit définir explicitement :

### Propriétaire du schéma

Un seul mécanisme doit être la source de vérité pour les migrations structurelles, sauf architecture explicitement conçue autrement.

Définir :

```yaml
schema_owner:
  service:
  migration_tool:
```

### Droits d'accès par composant

Pour chaque composant :

```yaml
component:
  read:
  write:
  migrate:
  owned_tables_or_domains:
```

Le skill ne choisit pas automatiquement qui doit écrire : cela dépend de l'architecture demandée.

### Frontières transactionnelles

Une opération exigeant une transaction atomique doit rester dans une couche capable de garantir cette atomicité.

Évite un workflow où deux services modifient indépendamment des tables qui doivent être validées dans la même transaction, sauf mécanisme distribué explicitement prévu.

## Plusieurs ORM sur la même base

Si plusieurs ORM existent :

- définir lequel migre ;
- définir si les autres sont lecture seule ou peuvent écrire ;
- éviter les migrations automatiques concurrentes ;
- documenter les différences de types entre moteurs/ORM ;
- prévoir une validation de drift du schéma.

## Dev / recette / production

Lorsque les moteurs diffèrent selon l'environnement :

- rester dans le sous-ensemble de fonctionnalités réellement compatible ou gérer explicitement les différences ;
- tester les migrations sur le moteur cible de production ;
- ne pas considérer un succès SQLite comme preuve suffisante de compatibilité avec un autre SGBD.

## Artefact attendu

Lorsque ce skill est activé, formaliser un `DATABASE_OWNERSHIP` ou intégrer l'équivalent dans `TECH_STACK`/`DATA_MODEL` avec :

- propriétaire du schéma ;
- outil de migration ;
- droits read/write/migrate ;
- frontières transactionnelles ;
- stratégie anti-drift ;
- règles de compatibilité inter-environnements.
