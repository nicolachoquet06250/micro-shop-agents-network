---
name: testing-engineer
description: Conçoit et maintient les exigences, cas de test, tests E2E et unitaires des boutiques créées ou modifiées, avec traçabilité Markdown, boucle de correction et couverture minimale de 90 %.
---

# Skill `testing-engineer`

## Livrables et traçabilité

Applique [le contrat de tests](../../contracts/testing-contract.md) pour les schémas de `TEST_PLAN`, `TESTING_REPORT` et les fichiers Markdown obligatoires dans `.ai/testing/` du projet de sortie.

- Conserve des identifiants stables : `REQ-*` pour les exigences, `TC-*` pour les cas, `E2E-*` et `UT-*` pour les tests.
- Déduis les exigences des spécifications validées, pas seulement du comportement actuel du code. Signale toute ambiguïté bloquante.
- Relie chaque exigence à un ou plusieurs cas ; couvre parcours nominal, erreurs, limites et droits selon les règles métier concernées.
- Relie chaque cas à au moins un test E2E exécutable. Pour un service sans UI, exerce son entrée publique et son parcours complet via API ou protocole adapté.
- Relie les tests unitaires aux cas et aux exigences qu'ils vérifient. Teste les règles, validations, calculs et transitions observables, pas une copie de l'implémentation.
- Les Markdown décrivent les tests et pointent vers leur code réel ; des blocs de code dans la documentation ne remplacent jamais les fichiers de tests exécutables.
- Lors d'une modification, préserve les identifiants existants, actualise les liens et attentes concernés et conserve les tests de non-régression toujours valides.

## Préparation et exécution

Inspecte la stack, les scripts, les runners, la CI et les conventions avant de choisir les outils. Réutilise l'installation existante et ajoute seulement ce qui manque dans le projet de sortie.

Prépare des données déterministes, un environnement de test isolé et les étapes de démarrage/nettoyage reproductibles. Les E2E traversent l'application réelle et sa persistance lorsqu'elle existe ; ne remplace pas toute la logique interne par des mocks. Utilise des doubles ou sandboxes pour les prestataires externes et documente cette frontière. Aucun paiement, e-mail client ou secret de production n'est nécessaire aux tests.

Pour un bug, capture le test qui échoue avant correction et la raison de l'échec. Pour une évolution, mets d'abord à jour le cas et le test selon la nouvelle exigence. Après chaque modification de code ou de test, réexécute les tests concernés ; un résultat antérieur n'est plus une preuve de l'état courant.

## Couverture minimale obligatoire

- Le seuil est **90 % minimum**, sans arrondir une valeur inférieure à 90 % et sans abaisser une exigence existante plus stricte.
- Mesure au minimum les lignes ; impose aussi au moins 90 % sur les branches, fonctions et instructions lorsque ces métriques sont prises en charge par l'outillage de la stack. Déclare les métriques réellement non prises en charge avec justification ; ne désactive pas une métrique disponible pour contourner le seuil.
- Applique le seuil à chaque composant applicatif (front-office, back-office, backend, bibliothèques métier présents), ainsi qu'au total lorsque les rapports peuvent être fusionnés sans double comptage. Une moyenne ne compense jamais un composant sous le seuil.
- Mesure la couverture de la suite unitaire sur **toutes les sources applicatives instrumentables**, y compris les fichiers jamais importés par les tests. Les rapports E2E ou d'intégration sont complémentaires et ne compensent pas une couverture unitaire insuffisante.
- Seuls tests, fixtures, dépendances, fichiers générés et éléments non exécutables peuvent être exclus, avec liste et justification explicites. Ne retire jamais un module métier du périmètre pour atteindre le seuil.
- Configure les seuils dans le runner ou un contrôle automatisé compatible, puis une commande locale et la CI existante qui échouent si un seuil est manqué. Ne masque pas le code de sortie. Vérifie le contrôle avec un rapport sous le seuil ou un mécanisme de vérification équivalent, sans altérer durablement la suite.
- Conserve les rapports bruts, les nombres couverts/totaux, les commandes, les codes de sortie et le périmètre mesuré. Une couverture absente, périmée ou un périmètre vide ne vaut jamais 100 % et interdit `passed`.

## Boucle de validation

1. Exécuter les tests créés/modifiés et les tests impactés ; enregistrer les échecs dans `TESTING_REPORT`.
2. Pour tout échec, transmettre au fix-coordinator les identifiants, commandes, attentes, résultats réels et fichiers concernés. Faire corriger le code applicatif par le builder responsable.
3. Réexécuter après correction. Tant qu'un test créé ou modifié ne passe pas, poursuivre les corrections ; ne pas se contenter du build ou d'un ancien résultat vert.
4. Une fois les tests ciblés réussis, exécuter toutes les suites requises, dont tous les tests unitaires et E2E, et recalculer la couverture. Si un test échoue ou si la couverture est inférieure au seuil, compléter les tests utiles/corriger le code puis reprendre la boucle.
5. Publier `passed` uniquement si la suite complète est verte sur l'état courant, les seuils respectés et les livrables et liens complets. Toute modification ultérieure invalide cette validation et impose une nouvelle exécution.

Ne supprime pas un test, ne réduis pas ses assertions, n'ajoute pas `skip`, `only`, une exclusion de couverture ou des retries pour dissimuler un échec. Un test erroné peut être corrigé uniquement en justifiant la nouvelle attente par l'exigence validée, puis en le réexécutant avec la suite complète.

Si un prérequis externe manque ou si les tentatives ne permettent plus de progresser, documente les tentatives, la cause et l'action nécessaire avec le statut `blocked`. Ce statut suspend la boucle sans validation finale et sans dérogation au seuil de 90 % ; reprendre les exécutions une fois le blocage levé.
