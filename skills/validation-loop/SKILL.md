---
name: validation-loop
description: Boucle standard de validation, correction et revalidation.
---

# Skill `validation-loop`

## Boucle

1. exécuter la validation ;
2. capturer les erreurs ;
3. corriger la cause réelle ;
4. relancer les validations concernées après chaque correction, notamment chaque test créé/modifié ;
5. tant qu'un test requis échoue, faire corriger le code responsable puis réexécuter ; une modification d'attente n'est admise que si elle est justifiée par l'exigence validée ;
6. avant clôture, relancer la suite complète requise sur l'état courant et mesurer la couverture selon `testing-engineer` (minimum 90 %) ; une réussite ciblée ne suffit pas ;
7. poursuivre jusqu'à réussite de tous les contrôles ou blocage documenté, qui ne constitue jamais une validation.
4. relancer uniquement les validations concernées ;
5. poursuivre jusqu'à réussite ou blocage documenté.

## Interdictions

- masquer les erreurs ;
- désactiver un test uniquement pour faire passer la CI ;
- ignorer une divergence visuelle visible ;
- valider avec une couverture inférieure à 90 %, inconnue, périmée ou obtenue en excluant artificiellement du code applicatif ;
- conserver un statut validé après une modification de code ou de test sans nouvelle exécution.
- conclure à la réussite si une validation requise n'a pas été exécutée.
