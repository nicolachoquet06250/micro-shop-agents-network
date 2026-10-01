# Contrats d'échange entre agents

Le système évite de transmettre l'intégralité du contexte conversationnel.

Chaque agent consomme et produit des artefacts structurés.

## Artefacts standards

```text
PROJECT_SPEC
TECH_STACK
IMPLEMENTATION_PLAN
TEST_PLAN
DESIGN_SYSTEM
PAGE_LAYOUTS
DATA_MODEL
DOMAIN_MODEL
SECURITY_MODEL
INTEGRATION_PLAN
PAYMENT_SPEC
EMAIL_SPEC
MEDIA_SPEC
MCP_SPEC
MCP_IMPLEMENTATION_RESULT
SEO_SPEC
SEO_BACKOFFICE_REQUEST
AGENTIFICATION_SPEC
AGENTIFICATION_RESULT
IMPLEMENTATION_RESULT
QA_REPORT
TESTING_REPORT
VISUAL_REPORT
QUALITY_REPORT
SECURITY_REVIEW
SEO_REPORT
FIX_PLAN
```

## Règles

- un artefact validé est la source de vérité de son domaine ;
- un agent ne réécrit pas un artefact qui n'appartient pas à son domaine ;
- une modification structurante doit être explicitement répercutée aux artefacts dépendants ;
- une valeur manquante reste `null`/`UNSPECIFIED` plutôt que d'être inventée ;
- les hypothèses sont listées séparément.

## Exemple de handoff

```yaml
from: micro-shop-technology-architect
to: micro-shop-project-planner
artifacts:
  - PROJECT_SPEC
  - TECH_STACK
assumptions:
  - "Le paiement réel utilisera un flux hébergé."
blocking_questions: []
```

Le format détaillé de `AGENTIFICATION_SPEC` et `AGENTIFICATION_RESULT` est défini dans `agentification-contract.md`.

Le format de `TEST_PLAN`, `TESTING_REPORT`, les livrables Markdown et le circuit de correction sont définis dans [testing-contract.md](testing-contract.md). Le plan précède chaque lot ; le rapport est actualisé après chaque exécution et conditionne la validation finale.
Le format détaillé de `MCP_SPEC` et `MCP_IMPLEMENTATION_RESULT` est défini dans `mcp-contract.md`.
