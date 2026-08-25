# Test du plugin Comptallie Immobilier — via Skills

> Ce repo est de l'emballage pur : `.claude-plugin/marketplace.json` +
> `adapters/claude_plugin/` connectent le serveur MCP distant déjà en ligne
> (hébergé séparément, dans le repo privé `Comptallie_MCP`) et chargent les
> skills de la suite immobilier (gestion des mails, prospection
> d'entreprises, contexte) via le mécanisme Plugins de Claude.ai. Aucune
> logique métier ici — en particulier, le `SKILL.md` de gestion des mails ne
> contient volontairement AUCUN critère de jugement substantiel (ce qui
> compte comme urgent, ce qui doit être ignoré, comment moduler le ton) : ces
> critères vivent côté serveur privé et sont récupérés à l'exécution via le
> tool `obtenir_criteres_tri`. C'est pour ça que ce repo est public et
> l'autre ne l'est pas. La prospection d'entreprises n'a pas d'équivalent (sa
> recherche est déterministe, pas un jugement métier à protéger).

## 1. Le repo est déjà public et poussé

Rien à faire ici — ce repo (`hugo-esposito-farese/comptallie-plugin`) est
déjà public et à jour sur `main`. **Renommage GitHub en attente** : le
contenu correspond désormais à la suite immobilier uniquement (cf.
Comptallie_MCP/CLAUDE.md section 6bis) — le nom du repo lui-même
(`comptallie-plugin`) doit être changé en `comptallie-immobilier-plugin`
manuellement (Settings → General → Repository name), GitHub redirige
automatiquement l'ancienne URL après renommage.

## 2. Ajouter le plugin dans Claude.ai

1. Ouvrir [claude.ai](https://claude.ai), aller dans **Customize** (menu de
   gauche) → onglet **Plugins**.
2. Dans la section **Personal plugins**, cliquer sur **"+"**, puis
   **"Add marketplace"**.
3. Choisir **"Add from a repository"** et coller l'URL de CE repo (utiliser
   l'URL actuelle tant que le renommage GitHub n'est pas fait, cf. section 1
   — l'ancienne URL continuera de fonctionner par redirection après) :
   `https://github.com/hugo-esposito-farese/comptallie-plugin`
4. Une fois la marketplace `comptallie-immobilier-plugins` ajoutée,
   installer le plugin `comptallie-immobilier` qui y apparaît.
5. Si l'installation indique `Run /reload-plugins to activate.`, c'est une
   invite propre à Claude Code — sur Claude.ai web, il n'y a rien de plus à
   faire, le plugin est actif dès l'installation confirmée.

## 3. Test simple — présentation de l'agent de gestion des mails

Dans une nouvelle conversation Claude.ai, écrire :

```
/gestion-mail
```

ou

```
salut
```

**Attendu** : une présentation courte et directe en 1re personne ("Je
m'occupe de trier tes mails et préparer des brouillons de réponse..."),
sans prénom, avec les capacités listées, et une proposition immédiate de
période à couvrir.

## 4. Test spécifique — le bug de personnage est-il résolu ?

Poser, dans la même conversation, l'une puis l'autre de ces deux questions :

```
tu es qui ?
```

```
tu peux faire autre chose ?
```

**Attendu, dans les deux réponses** :
- L'agent répond toujours en 1re personne, dans son rôle.
- Le mot **"Claude" n'apparaît jamais**, sous aucune forme.
- Sur "tu peux faire autre chose ?" : une réponse du type "Je ne sais pas
  encore faire ça — je m'occupe uniquement du tri de mails et des
  brouillons", jamais une réponse générique de type assistant IA.

**Si le mot "Claude" apparaît dans une de ces deux réponses** : le bug
n'est pas résolu par ce canal non plus — signaler la réponse exacte.

## 5. Test spécifique — les deux tools de préparation

Depuis cette session, un tri de mails déclenche **deux** tools en même
temps avant toute analyse : `preparer_contexte_tri_mail` (contexte entité +
période) et `obtenir_criteres_tri` (critères de jugement, récupérés à
l'exécution — jamais écrits dans ce repo). Confirmer une période pour
déclencher la séquence complète, et vérifier dans le résumé final que le
tri distingue bien mails traités / ignorés — signe que les critères ont été
appliqués, même si le contenu exact des critères n'est jamais visible dans
ce repo.

Vérifier aussi qu'aucun mail n'est jamais envoyé, seulement des brouillons
créés.

## 6. Test simple — présentation de l'agent de prospection d'entreprises

Dans une nouvelle conversation Claude.ai, écrire :

```
/prospection-entreprises
```

**Attendu** : une présentation courte et directe en 1re personne ("Je
cherche des entreprises et leurs dirigeants comme contacts potentiels..."),
sans prénom, avec les capacités listées, et une proposition immédiate de
critères de recherche.

## 7. Test spécifique — recherche réelle

```
Trouve-moi des entreprises créées en Île-de-France ces 30 derniers jours
```

**Attendu** : l'agent appelle `rechercher_entreprises` avec une zone (région
Île-de-France traduite en code INSEE `11`) et une période, sans jamais
demander à l'utilisateur de préciser un code technique. Si peu ou aucun
résultat n'est trouvé, il doit l'expliquer simplement (l'API publique ne
trie pas par date de création, donc une recherche par période sur une zone
large peut manquer des créations récentes) — jamais comme si elle avait
échoué ou planté. Comme pour la gestion des mails : le mot "Claude" ne doit
jamais apparaître.
