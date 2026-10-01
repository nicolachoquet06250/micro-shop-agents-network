# Contrat d'agentification

## `AGENTIFICATION_SPEC`

Produit par `micro-shop-agentification-engineer` avant la finalisation de l'implémentation.

```yaml
schema_version: 1
canonical_instructions: AGENTS.md
adapters:
  - CLAUDE.md
  - GEMINI.md
  - .github/copilot-instructions.md
  - .junie/AGENTS.md
  - .cursor/rules/00-project.mdc
  - .roo/rules/00-project.md
  - .windsurf/rules/00-project.md
embedded_network:
  agents: all
  skills: all
  contracts: all
scoped_instruction_files: []
project_interfaces:
  mcp:
    enabled: false
    spec: .ai/mcp-spec.yaml
validation:
  require_manifest: true
  require_network_completeness: true
  require_adapter_consistency: true
```

## `AGENTIFICATION_RESULT`

Produit après matérialisation/synchronisation.

```yaml
canonical_file: AGENTS.md
adapters_created: []
scoped_agents_files: []
embedded:
  agents: []
  skills: []
  contracts: []
manifest: .agents/manifest.yaml
checks:
  required_files_present: false
  all_source_agents_copied: false
  all_source_skills_copied: false
  all_source_contracts_copied: false
  adapters_consistent: false
  no_secrets_detected: false
  mcp_documented_when_enabled: false
remaining_issues: []
```

## Règle de complétude

Le nombre de fichiers embarqués n'est pas codé en dur. Il est calculé à partir du réseau source disponible au moment de l'exécution. Tout agent, skill ou contrat ajouté au réseau avant la finalisation doit être inclus dans la boutique générée.
