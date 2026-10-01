---
name: mcp-server-engineering
description: Conçoit et implémente un serveur Model Context Protocol conforme, intégré au backend existant quelle que soit sa technologie.
---

# Skill `mcp-server-engineering`

Ce skill définit les règles réutilisables pour concevoir, implémenter et valider un serveur **Model Context Protocol (MCP)** intégré au serveur de la boutique.

## Documentation officielle

Source de vérité du protocole :

- spécification canonique : https://modelcontextprotocol.io/specification
- révision stable au moment de la rédaction : https://modelcontextprotocol.io/specification/2026-07-28
- SDK officiels : https://modelcontextprotocol.io/docs/sdk

Avant toute implémentation, vérifie la révision stable courante dans la documentation canonique. N'épingle la révision `2026-07-28` que si elle est toujours la révision stable appropriée ou si le projet exige explicitement cette compatibilité.

## Principes

- Le serveur MCP est intégré au backend réel de la boutique lorsque l'architecture le permet.
- Ne crée pas un second serveur métier ni une duplication de la logique e-commerce.
- Les tools MCP appellent les mêmes services/domaines que l'API ou le back-office.
- Les resources MCP lisent les mêmes sources de vérité que l'application.
- Les prompts MCP ne doivent pas contenir de secret ni contourner les autorisations.
- Le choix du langage et de l'implémentation MCP suit `TECH_STACK` ; le skill ne force jamais Node.js.
- Préfère un SDK MCP officiel et stable pour la technologie retenue lorsqu'il existe et qu'il supporte la révision ciblée.
- Si aucun SDK officiel adapté n'est disponible, implémente le protocole à partir de la spécification officielle sans inventer de méthodes propriétaires présentées comme MCP.

## Transport

Pour un serveur de boutique déjà exposé en HTTP, privilégie le transport HTTP prévu par la révision MCP ciblée et intègre un endpoint dédié, généralement `/mcp`, sauf contrainte du projet.

`stdio` peut être ajouté pour un usage local/outillage lorsque cela apporte une vraie valeur, mais il ne remplace pas automatiquement l'intégration HTTP d'un serveur distant.

Documente explicitement :

- transport(s) activé(s) ;
- chemin/commande ;
- révision MCP ciblée ;
- stratégie de compatibilité avec d'anciennes révisions si elle existe.

## Capacités MCP

N'expose que les capacités utiles au projet :

- **tools** : actions ou opérations exécutables ;
- **resources** : contexte/données adressables ;
- **prompts** : workflows ou templates réutilisables.

Ne crée pas artificiellement les trois catégories si le projet n'en a pas besoin.

Chaque tool doit avoir :

- un nom stable et non ambigu ;
- une description opérationnelle ;
- un schéma d'entrée strict ;
- un résultat structuré lorsque pertinent ;
- des erreurs déterministes ;
- une politique d'autorisation explicite ;
- des tests positifs et négatifs.

Chaque resource doit définir :

- son URI ou template d'URI ;
- son type/MIME lorsque pertinent ;
- les règles d'accès ;
- la stratégie de pagination ou limitation si le volume peut être important.

## Intégration métier

Un tool MCP ne réimplémente pas :

- calcul de prix ;
- promotions ;
- taxes ;
- stock ;
- commandes ;
- paiement ;
- permissions.

Il appelle les services métier existants.

Une opération sensible doit conserver exactement les mêmes invariants que son équivalent REST/GraphQL/back-office.

## Commerce et achat via MCP

Si MCP est activé sur une boutique qui permet des achats, le serveur MCP doit permettre d'effectuer le parcours d'achat, sauf exclusion explicite du brief.

Le parcours MCP réutilise les mêmes services que le storefront et ne crée jamais une seconde logique de panier/checkout.

### Capacités minimales

Selon les fonctionnalités réelles du projet, couvre au minimum :

```text
catalog.search_products
catalog.get_product

cart.get
cart.add_item
cart.update_item
cart.remove_item

checkout.prepare
checkout.apply_promo_code
checkout.start_payment
checkout.get_status

orders.get_my_order
orders.list_my_orders
```

Les noms sont indicatifs ; les responsabilités sont obligatoires lorsque correspondantes au projet.

Un tool haut niveau `checkout.purchase` peut être ajouté lorsque la révision MCP/SDK permet de gérer proprement les étapes interactives. Il orchestre les services existants sans dupliquer les calculs métier.

### `checkout.prepare`

Doit recalculer côté serveur :

- produits et variantes ;
- quantités et disponibilité ;
- prix ;
- promotions ;
- codes promo ;
- taxes ;
- livraison ;
- total final.

Il retourne un snapshot/récapitulatif de checkout avec un handle contrôlé par le serveur.

### `checkout.start_payment`

- exige une identité client authentifiée lorsque le projet interdit le checkout invité ;
- exige que le checkout soit encore valide ;
- invalide/recalcule si prix, stock, promotion ou taxe ont changé ;
- crée l'intention/order chez le fournisseur de paiement ;
- retourne ou déclenche uniquement le mécanisme d'approbation prévu par le fournisseur.

### Interaction de paiement sécurisée

Les données sensibles de paiement ne doivent pas transiter par le modèle ni par les champs ordinaires du protocole.

Lorsqu'une étape web est requise, utilise le mécanisme sécurisé supporté par la révision MCP/SDK ciblée, par exemple une URL elicitation / Multi Round-Trip Request lorsqu'elle est normative et disponible, ou un lien d'approbation hors bande contrôlé par le serveur.

Le protocole MCP transporte uniquement les handles, statuts, URLs d'approbation appropriées et résultats métier non sensibles nécessaires au workflow.

### Finalisation

La finalisation d'achat doit :

1. reprendre un handle de checkout contrôlé par le serveur ;
2. vérifier l'état réel auprès du fournisseur de paiement ;
3. capturer/confirmer côté serveur lorsque le provider le requiert ;
4. exécuter la transaction de commande avec idempotence ;
5. persister les snapshots économiques nécessaires ;
6. décrémenter/réserver le stock selon `DOMAIN_MODEL` ;
7. déclencher les événements/e-mails applicables ;
8. retourner la commande créée/finalisée.

Ne fais jamais confiance à :

```text
paid=true
payment_success=true
amount=<valeur client>
customer_id=<compte arbitraire>
```

comme preuves ou autorités métier.

### Authentification

Lorsque le checkout invité est interdit :

- `cart.*`, `checkout.*` et les commandes personnelles sont protégés ;
- l'identité acheteur provient du contexte d'authentification effectif ;
- un tool ne prend pas d'identifiant client permettant de changer arbitrairement d'acheteur ;
- le panier anonyme n'est pas convertible en commande sans authentification selon les règles du projet.

### Confirmation humaine

Un achat réel ne doit pas être déclenché sur une instruction ambiguë.

Avant la phase irréversible, retourne le récapitulatif final et requiers une confirmation explicite par le mécanisme d'interaction supporté par le client/protocole.

## Authentification et autorisation

- Ne considère jamais les métadonnées déclaratives d'un client comme une identité de sécurité.
- Réutilise ou adapte le modèle d'identité et de rôles défini par `SECURITY_MODEL`.
- Vérifie les autorisations **dans chaque opération MCP concernée**, pas uniquement au niveau de l'interface cliente.
- Un utilisateur ne peut lire ou modifier que les données auxquelles il a déjà droit dans la boutique.
- Les tools administratifs exigent explicitement un rôle/permission d'administration.
- Aucun tool d'écriture ne doit devenir public par défaut.
- Aucun secret, token, mot de passe, donnée de carte ou configuration interne ne doit être retourné par une resource ou un tool.

Lorsque la révision MCP et l'architecture utilisent les mécanismes d'autorisation définis par la spécification, implémente-les conformément à la documentation officielle au lieu d'inventer un protocole d'authentification MCP parallèle.

## Sécurité des tools

Évite par défaut les tools génériques dangereux tels que :

```text
execute_sql
execute_shell
read_any_file
write_any_file
http_request_any_url
```

S'ils sont exceptionnellement requis par le brief, ils doivent être bornés, autorisés explicitement et audités.

Préfère des tools métier étroits :

```text
catalog.list_products
catalog.get_product
orders.list_my_orders
orders.get_my_order
orders.cancel_my_order
admin.products.update
admin.promotions.create
```

Les noms exacts dépendent du projet.

## Données personnalisées

Les données personnalisées restent personnalisées via MCP :

- promotions privées ;
- commandes ;
- profil client ;
- prix réservés ;
- données administratives.

Ne transforme pas le serveur MCP en canal permettant de contourner le cloisonnement utilisateur.

## Observabilité

Prévois lorsque pertinent :

- logs techniques sans données sensibles ;
- correlation/request ID ;
- métriques de latence et d'erreur ;
- audit des mutations sensibles ;
- limites de taille et de durée.

## Validation

Valide au minimum :

1. démarrage/intégration du serveur MCP ;
2. compatibilité avec la révision déclarée ;
3. découverte/capabilities attendues ;
4. listing et appel des tools exposés ;
5. lecture des resources exposées ;
6. récupération des prompts exposés ;
7. validation des schémas d'entrée ;
8. erreurs protocolaires ;
9. authentification et rôles ;
10. isolation entre utilisateurs ;
11. absence de fuite de secrets ;
12. build et tests du backend ;
13. parcours d'achat MCP complet lorsque la boutique est transactionnelle ;
14. recalcul serveur des prix/promotions/taxes ;
15. impossibilité d'acheter pour un autre utilisateur ;
16. confirmation explicite avant paiement réel ;
17. redirection/interaction de paiement hors bande sans fuite de credentials ;
18. vérification réelle du paiement côté fournisseur avant création/finalisation de la commande ;
19. idempotence des appels de finalisation et absence de double commande/double capture.

Utilise les tests/conformance tools officiels disponibles pour la révision et le SDK choisis lorsqu'ils existent.
