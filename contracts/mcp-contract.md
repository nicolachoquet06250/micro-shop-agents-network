# Contrat MCP

## Documentation officielle

- spécification canonique : https://modelcontextprotocol.io/specification
- révision stable connue lors de la rédaction : https://modelcontextprotocol.io/specification/2026-07-28
- SDK officiels : https://modelcontextprotocol.io/docs/sdk

La révision stable doit être revérifiée avant implémentation.

## `MCP_SPEC`

Produit par `micro-shop-mcp-server-engineer` pendant la conception.

```yaml
schema_version: 1
enabled: true
protocol:
  canonical_spec: https://modelcontextprotocol.io/specification
  target_revision:
  compatibility: []
implementation:
  backend_component:
  language:
  sdk:
  sdk_version:
  integration_mode: in_process
transport:
  type:
  endpoint:
auth:
  strategy:
  role_mapping: {}
capabilities:
  tools:
    - name:
      access:
      input_schema:
      output_schema:
      service_dependency:
  resources:
    - uri_or_template:
      access:
      mime_type:
      service_dependency:
  prompts:
    - name:
      access:
      arguments: []
commerce:
  purchase_via_mcp:
    enabled:
    authentication_required:
    guest_checkout_allowed:
    cart_tools: []
    checkout_tools: []
    order_tools: []
    high_level_purchase_tool:
    confirmation_strategy:
    checkout_handle_strategy:
    payment_provider:
    payment_flow:
    out_of_band_interaction:
    provider_verification:
    idempotency_strategy:
    order_finalization:

security:
  destructive_tools: []
  rate_limits: {}
  audit_required: []
testing:
  commands: []
  protocol_checks: []
assumptions: []
blocking_questions: []
```

## `MCP_IMPLEMENTATION_RESULT`

Produit après génération/intégration du serveur MCP.

```yaml
protocol_revision:
sdk:
transport:
endpoint:
capabilities:
  tools: []
  resources: []
  prompts: []
auth:
  strategy:
  verified_roles: []
files_added: []
files_modified: []
tests:
  commands: []
  passed: false
  conformance_verified: false
commerce_purchase:
  enabled: false
  tools: []
  authenticated_purchase_verified: false
  guest_purchase_blocked_when_required: false
  server_side_totals_verified: false
  explicit_confirmation_verified: false
  out_of_band_payment_verified: false
  provider_payment_verification_verified: false
  idempotent_finalization_verified: false
  end_to_end_purchase_verified: false
security_checks:
  cross_user_isolation: false
  admin_authorization: false
  secret_exposure: false
known_limitations: []
```

## Règles de handoff

- `MCP_SPEC` est transmis à `micro-shop-implementation-orchestrator`, `micro-shop-backend-builder`, `micro-shop-qa`, `micro-shop-security-review` et `micro-shop-agentification-engineer`.
- `MCP_IMPLEMENTATION_RESULT` est transmis aux agents de QA, qualité, sécurité, agentification et reporting final.
- Une modification de `SECURITY_MODEL`, `DOMAIN_MODEL`, `PAYMENT_SPEC` ou des services appelés par les capabilities MCP doit déclencher une vérification de cohérence de `MCP_SPEC`.
- Lorsque la boutique est transactionnelle et que MCP est activé, `MCP_SPEC` doit décrire explicitement le parcours d'achat MCP ou documenter une exclusion explicite du brief.
