# Organisation recommandée des artefacts

```text
.ai/
├── project-spec.yaml
├── technology-stack.yaml
├── architecture.md
├── implementation-plan.yaml
├── test-plan.yaml
├── testing-report.yaml
├── testing/
│   ├── exigences-metier.md
│   ├── cas-de-tests.md
│   ├── tests-e2e.md
│   ├── tests-unitaires.md
│   └── rapport-validation.md
├── design-system.yaml
├── page-layouts.yaml
├── data-model.yaml
├── commerce-domain.yaml
├── security-model.yaml
├── integrations.yaml
├── payment-spec.yaml
├── email-spec.yaml
├── media-spec.yaml
├── mcp-spec.yaml
├── mcp-implementation-result.yaml
├── seo-spec.yaml
├── seo-backoffice-request.yaml
├── agentification-spec.yaml
├── agentification-result.yaml
├── implementation-result.yaml
├── qa-report.yaml
├── visual-report.yaml
├── quality-report.yaml
├── security-review.yaml
├── seo-report.yaml
└── fix-plan.yaml
```

Ces fichiers peuvent être adaptés à l'orchestrateur :
- `.github`
- `.claude`
- `.junie`
- `.codex`
- `.roo`
- `.agents`
- `.ai`

L'orchestrateur doit conserver le même modèle logique même si les chemins physiques diffèrent.

## Livrables de tests obligatoires

Le répertoire canonique `.ai/testing/` se trouve dans le **projet de sortie**. Son contenu, `TEST_PLAN` et `TESTING_REPORT` sont définis dans [testing-contract.md](testing-contract.md). Les tests exécutables sont écrits dans les dossiers de tests conventionnels du projet et référencés depuis ces Markdown. Un éventuel changement de chemin physique doit être consigné dans `TEST_PLAN`, `TESTING_REPORT` et `AGENTS.md` en maintenant tous les liens.

## Agentification du dépôt généré

Les artefacts `.ai/` ne remplacent pas les instructions persistantes du dépôt. Toute boutique générée doit également contenir au minimum :

```text
AGENTS.md
CLAUDE.md
GEMINI.md
.github/copilot-instructions.md
.junie/AGENTS.md
.cursor/rules/00-project.mdc
.roo/rules/00-project.md
.windsurf/rules/00-project.md
.agents/manifest.yaml
.agents/README.md
.agents/agents/
.agents/skills/
.agents/contracts/
```

Le contenu détaillé et les règles de synchronisation sont définis par le skill `project-agentification`.
