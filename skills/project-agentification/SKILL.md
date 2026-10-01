---
name: project-agentification
description: Rend le dépôt généré autonome et exploitable par plusieurs orchestrateurs IA grâce à AGENTS.md, des adaptateurs d'orchestrateur et un réseau embarqué d'agents/skills.
---

# Skill `project-agentification`

Ce skill définit comment rendre une boutique générée **agentifiée** : tout orchestrateur IA doit pouvoir comprendre le dépôt, ses contraintes, ses commandes, son architecture et les workflows spécialisés sans dépendre du contexte de la conversation qui a créé le projet.

## Principe de source de vérité

Le fichier racine `AGENTS.md` est la source de vérité commune des instructions projet.

Les fichiers spécifiques aux orchestrateurs sont des adaptateurs. Ils doivent :

- pointer vers `AGENTS.md` ou en reprendre uniquement les invariants indispensables lorsque l'orchestrateur ne sait pas inclure un fichier externe ;
- ne pas introduire de règle métier contradictoire ;
- rester courts ;
- orienter l'orchestrateur vers les agents et skills embarqués ;
- être régénérés lorsque `AGENTS.md` change de manière structurante.

## Fichiers obligatoires à la racine du projet généré

Au minimum :

```text
AGENTS.md
CLAUDE.md
GEMINI.md
```

`AGENTS.md` doit contenir au minimum :

- objectif et périmètre du projet ;
- architecture et composants ;
- technologies réellement utilisées ;
- carte des principaux dossiers ;
- ownership de la base et des migrations ;
- commandes de développement, build, lint et tests ;
- conventions de code ;
- règles de sécurité ;
- règles métier non négociables ;
- règles de fidélité visuelle ;
- règles SEO lorsqu'un front-office public existe ;
- politique de secrets et variables d'environnement ;
- workflow attendu avant/après modification ;
- emplacement des agents, skills, contrats et artefacts ;
- règles de validation avant de considérer une tâche terminée ;
- routage avant/après modification vers `micro-shop-testing-engineer`, liens vers `.ai/testing/`, `TEST_PLAN` et `TESTING_REPORT`, commandes de tests unitaires/E2E et de couverture réellement disponibles ;
- boucle obligatoire de correction jusqu'à réussite de tous les tests requis, suite complète finale et seuil bloquant de couverture d'au moins 90 % selon `testing-engineer`.
- si MCP est activé : endpoint/transport, révision du protocole, emplacement de `MCP_SPEC`, commandes de test et règles d'autorisation à respecter.

Ne copie pas dans `AGENTS.md` toute la documentation du projet. Il doit rester suffisamment court pour être injecté fréquemment dans le contexte d'un agent.

## Adaptateurs d'orchestrateur obligatoires

Crée au minimum lorsque les dossiers concernés peuvent être versionnés :

```text
.github/copilot-instructions.md
.junie/AGENTS.md
.cursor/rules/00-project.mdc
.roo/rules/00-project.md
.windsurf/rules/00-project.md
```

Le projet peut également contenir des fichiers supplémentaires pour un orchestrateur explicitement demandé.

### GitHub Copilot

`.github/copilot-instructions.md` doit rappeler les invariants essentiels et demander de lire `AGENTS.md`. Des fichiers `.github/instructions/*.instructions.md` peuvent être générés pour des périmètres spécifiques si cela réduit le contexte et améliore la précision.

### Claude Code

`CLAUDE.md` doit orienter Claude vers :

- `AGENTS.md` ;
- `.agents/agents/` ;
- `.agents/skills/` ;
- `.agents/contracts/` ;
- `.ai/` pour les artefacts de projet.

Ne duplique pas l'intégralité des agents dans `CLAUDE.md`.

### Gemini

`GEMINI.md` suit le même principe d'adaptateur et référence la source de vérité `AGENTS.md`.

### Junie

`.junie/AGENTS.md` est généré à partir des règles de `AGENTS.md` nécessaires à Junie et doit indiquer que les agents/skills détaillés se trouvent dans `.agents/`.

### Cursor

`.cursor/rules/00-project.mdc` doit être une règle `alwaysApply` et diriger Cursor vers `AGENTS.md`. Les règles spécifiques à un sous-projet peuvent être ajoutées avec des globs ciblés.

### Roo Code

`.roo/rules/00-project.md` doit fournir les règles projet communes et l'emplacement du réseau d'agents/skills.

### Windsurf

`.windsurf/rules/00-project.md` doit fournir les règles projet communes et l'emplacement du réseau d'agents/skills.

### Codex et orchestrateurs compatibles AGENTS.md

Le `AGENTS.md` racine est le point d'entrée. Ajoute des `AGENTS.md` plus profonds uniquement lorsqu'un sous-arbre a des règles réellement spécifiques.

## Réseau embarqué obligatoire

La boutique générée doit embarquer **au minimum tous les agents, skills et contrats disponibles dans le réseau générateur au moment de la création ou de la mise à jour**.

Structure canonique :

```text
.agents/
├── README.md
├── manifest.yaml
├── agents/
│   └── *.agent.md
├── skills/
│   └── <skill>/SKILL.md
└── contracts/
    └── *.md
```

Ne maintiens pas une liste figée dans le code si le réseau source peut être inspecté. Découvre et copie tous les fichiers présents dans :

```text
agents/
skills/
contracts/
```

Ainsi, tout nouvel agent ou skill ajouté ultérieurement au générateur est automatiquement propagé dans les boutiques créées ou ré-agentifiées.

## Manifest d'agentification

`.agents/manifest.yaml` doit documenter au minimum :

```yaml
schema_version: 1
canonical_instructions: AGENTS.md
network:
  agents_dir: .agents/agents
  skills_dir: .agents/skills
  contracts_dir: .agents/contracts
adapters:
  - CLAUDE.md
  - GEMINI.md
  - .github/copilot-instructions.md
  - .junie/AGENTS.md
  - .cursor/rules/00-project.mdc
  - .roo/rules/00-project.md
  - .windsurf/rules/00-project.md
```

Le manifest peut aussi enregistrer les fichiers copiés et leur version/source lorsque cette information existe.

## Instructions par sous-projet

Pour une architecture multi-composants, ajoute des `AGENTS.md` locaux uniquement lorsqu'ils apportent des règles propres au composant.

Exemples :

```text
apps/storefront/AGENTS.md
apps/backoffice/AGENTS.md
services/api/AGENTS.md
services/mail/AGENTS.md
```

Ces fichiers héritent conceptuellement du `AGENTS.md` racine et ne répètent que :

- commandes spécifiques ;
- architecture locale ;
- conventions propres à la stack ;
- fichiers interdits ou zones sensibles ;
- validations locales obligatoires.

## Artefacts de projet

Les contrats et décisions produits pendant la génération restent disponibles dans `.ai/` selon `artifact-layout.md`.

Un agent reprenant le projet doit pouvoir reconstruire l'état pertinent à partir de :

1. `AGENTS.md` ;
2. `.agents/manifest.yaml` ;
3. `.agents/agents/` et `.agents/skills/` ;
4. `.ai/` ;
4. `.ai/`, y compris `mcp-spec.yaml` / `mcp-implementation-result.yaml` lorsque MCP est activé ;
5. le code et les configurations du dépôt.

## Synchronisation

Quand l'architecture, les commandes, l'ownership, les règles métier structurantes ou les workflows de validation changent :

1. mettre à jour `AGENTS.md` ;
2. mettre à jour les `AGENTS.md` locaux concernés ;
3. régénérer/synchroniser les adaptateurs ;
4. actualiser `.agents/manifest.yaml` ;
5. vérifier que les agents/skills embarqués couvrent toujours les workflows utilisés.

## Validation obligatoire

Avant de considérer l'agentification terminée, vérifier :

- présence de tous les fichiers obligatoires ;
- présence de tous les agents du réseau source dans `.agents/agents/` ;
- présence de tous les skills du réseau source dans `.agents/skills/` ;
- présence de tous les contrats du réseau source dans `.agents/contracts/` ;
- absence de contradiction manifeste entre `AGENTS.md` et ses adaptateurs ;
- chemins et commandes documentés réellement valides ;
- absence de secret réel dans les fichiers d'instructions ;
- absence de duplication massive inutile de contexte.
