# Comptallie — plugin Claude.ai, suite immobilier

Ce repo est l'**emballage public** du plugin Claude.ai pour la **suite
commerciale immobilier** de Comptallie (`core/catalog/suites.py`, id
`immobilier`, dans le repo privé `Comptallie_MCP`) : gestion des mails
(`/gestion-mail`), prospection d'entreprises (`/prospection-entreprises`) et
contexte (`/contexte`).

Il ne contient **aucune logique métier** : uniquement `.claude-plugin/marketplace.json`
et `adapters/claude_plugin/` (`plugin.json`, un `SKILL.md` par agent, `.mcp.json`
pointant vers le serveur MCP distant déjà en ligne). Toute la logique et les
critères de jugement vivent dans le repo privé, récupérés à l'exécution via
des tools dédiés (ex. `obtenir_criteres_tri`) — jamais copiés ici.

## Pourquoi un repo par suite, pas un repo unique

Le mécanisme Plugins de Claude.ai (Customize → Plugins → Add from repository)
exige un repo GitHub public par plugin. **Chaque suite commerciale a son
propre repo plugin public, cloisonné** : un client qui installe le plugin
d'une suite ne doit voir apparaître que les commandes de cette suite —
jamais celles d'une autre. Ce repo (`comptallie-immobilier-plugin`) n'expose donc QUE les skills de la
suite immobilier.

**Toute future suite (ex. "comptable") aura son propre repo plugin public
séparé**, généré à partir du même `core/` privé mais n'exposant que le
sous-ensemble de skills pertinent à cette suite — jamais un repo unique
fourre-tout regroupant plusieurs suites. Cf. `Comptallie_MCP/CLAUDE.md`
section 6bis pour le détail complet de ce principe.

## Nommage fonctionnel (phase de validation)

Les commandes de ce plugin sont nommées par fonction (`/gestion-mail`,
`/prospection-entreprises`) plutôt que par persona, le temps de valider que
chaque fonctionnalité individuelle est utile aux clients pilotes — l'approche
persona (agents nommés, cf. Limova) n'est pas abandonnée, seulement reportée
après cette phase de validation. Cf. `Comptallie_MCP/CLAUDE.md` section 6bis
et 7.2.

## Structure

```
.claude-plugin/marketplace.json     ← déclare ce plugin
adapters/claude_plugin/
  .claude-plugin/plugin.json        ← métadonnées du plugin
  .mcp.json                         ← pointe vers le serveur MCP distant (Railway)
  skills/
    comptallie/SKILL.md             ← message d'accueil de la suite
    contexte/SKILL.md               ← /contexte
    gestion-mail/SKILL.md           ← /gestion-mail
    prospection-entreprises/SKILL.md ← /prospection-entreprises
```

Voir `TEST_PLUGIN.md` pour le protocole de test après installation.
