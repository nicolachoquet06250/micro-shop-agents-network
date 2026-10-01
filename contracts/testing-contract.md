# Contrat de tests

`micro-shop-testing-engineer` possède les documents et artefacts de tests. Les chemins ci-dessous sont relatifs à la racine du projet de sortie. Les tests exécutables restent dans les dossiers conventionnels de sa stack.

## Documents Markdown obligatoires

| Fichier | Contenu minimal |
| --- | --- |
| `.ai/testing/exigences-metier.md` | ID `REQ-*`, source dans les spécifications, règle métier, critères d'acceptation observables, priorité, hypothèses et cas associés. |
| `.ai/testing/cas-de-tests.md` | ID `TC-*`, exigences liées, scénario, préconditions, données, étapes, résultats attendus et IDs E2E/UT associés. |
| `.ai/testing/tests-e2e.md` | ID `E2E-*`, cas et exigences liés, parcours complet, environnement, frontières simulées, fixtures, assertions, fichier exécutable, nom du test, commande et dernier résultat. |
| `.ai/testing/tests-unitaires.md` | ID `UT-*`, cas et exigences liés, unité/règle vérifiée, données et limites, assertions, fichier exécutable, nom du test, commande et dernier résultat. |
| `.ai/testing/rapport-validation.md` | État du code testé, date, suites/commandes exécutées, codes de sortie, décomptes, échecs, couverture par composant/métrique, exclusions motivées, preuves brutes, historique des corrections et statut final. |

Chaque exigence a au moins un cas et chaque cas au moins un E2E. Les liens doivent fonctionner dans les deux sens : exigence → cas → tests et test → cas → exigence. Les références aux tests doivent identifier un fichier et un test réels, avec le même ID dans le nom du test ou ses métadonnées/commentaires.

Avant écriture des tests, un chemin prévu porte explicitement le statut `planned` ; il ne constitue pas une preuve d'exécution. Les quatre premiers documents sont créés lors de la conception puis complétés après chaque lot. Le rapport de validation conserve les tentatives, y compris les échecs, et distingue l'état courant des résultats historiques.

## `TEST_PLAN`

Produit avant chaque lot de création, évolution ou correction ; consommé par l'implementation-orchestrator, les builders, QA et code-quality. Stockage : `.ai/test-plan.yaml`.

```yaml
schema_version: 1
owner: micro-shop-testing-engineer
change_type: creation # creation | modification | bugfix
documents_dir: .ai/testing
requirements: [] # IDs REQ et références aux sources validées
cases: [] # IDs TC, exigences et IDs E2E/UT associés
components: [] # sources, outils, fichiers de tests, métriques, exclusions motivées
commands: [] # répertoire de travail, commande, prérequis, type de suite
coverage:
  minimum_percent: 90
  scope: all_instrumentable_application_sources
  basis: unit_tests
  per_component: true
  required_metrics: [lines]
  additional_metrics: [] # branches, functions, statements selon support réel
  enforcement_command: null # commande qui échoue sous le seuil
affected_tests: [] # tests créés/modifiés et tests de non-régression concernés
blocking_questions: []
```

Les tableaux vides et valeurs `null` de cet exemple sont à remplir à partir du projet réel avant l'exécution. Conserver tout seuil existant supérieur à 90 %. La politique complète est définie dans [le skill testing-engineer](../skills/testing-engineer/SKILL.md).

## `TESTING_REPORT`

Produit après chaque tentative ; consommé par QA, code-quality, fix-coordinator, router et final-reporter. Stockage : `.ai/testing-report.yaml`.

```yaml
schema_version: 1
status: pending # pending | failed | blocked | passed
tested_state: null # commit + empreinte du diff ou empreinte des fichiers testés
executed_at: null
documents_dir: .ai/testing
runs: [] # suite, cwd, commande, code de sortie, passed/failed/skipped, preuve brute
coverage: [] # composant, métrique, couverts, total, pourcentage, seuil, rapport brut
traceability:
  all_requirements_have_cases: false
  all_cases_have_e2e: false
  all_tests_resolve_to_files: false
checks:
  markdown_complete: false
  all_created_and_modified_tests_pass: false
  full_required_suite_passes: false
  no_required_test_skipped: false
  coverage_thresholds_pass: false
  coverage_enforcement_verified: false
  evidence_matches_current_state: false
failures: [] # IDs TC/E2E/UT, attendu, obtenu, reproduction, fichiers, propriétaire
blockers: [] # prérequis, tentatives et action permettant la reprise
```

`passed` exige tous les contrôles et la traçabilité à `true`, des exécutions et mesures présentes, aucun échec/blocage, et au moins 90 % pour chaque métrique requise et chaque composant. Une réussite ciblée ne remplace pas la suite complète finale. `failed`, `blocked`, un rapport manquant ou périmé interdisent la validation finale.

## Circuit de correction

`TESTING_REPORT` en échec → `micro-shop-fix-coordinator` → `FIX_PLAN` → `micro-shop-implementation-orchestrator` → builder concerné → `micro-shop-testing-engineer`.

Chaque tâche de `FIX_PLAN` référence les exigences/cas/tests en échec, la cause, le propriétaire, les fichiers, la commande de reproduction et les validations à relancer. Le testing-engineer corrige les tests uniquement si leur attente contredit l'exigence validée ; les builders corrigent les défauts applicatifs. La boucle continue jusqu'à réussite de tous les tests requis et des seuils, ou blocage explicite non assimilé à une réussite.
