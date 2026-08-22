# Test du plugin Comptallie — Julie et Méline via Skills

> Ce repo est de l'emballage pur : `.claude-plugin/marketplace.json` +
> `adapters/claude_plugin/` connectent le serveur MCP distant déjà en ligne
> (hébergé séparément, dans le repo privé `Comptallie_MCP`) et chargent les
> skills Julie et Méline via le mécanisme Plugins de Claude.ai. Aucune
> logique métier ici — en particulier, le `SKILL.md` de Julie ne contient
> volontairement AUCUN critère de jugement substantiel (ce qui compte comme
> urgent, ce qui doit être ignoré, comment moduler le ton) : ces critères
> vivent côté serveur privé et sont récupérés à l'exécution via le tool
> `obtenir_criteres_tri`. C'est pour ça que ce repo est public et l'autre ne
> l'est pas. Méline n'a pas d'équivalent (sa recherche est déterministe, pas
> un jugement métier à protéger).

## 1. Le repo est déjà public et poussé

Rien à faire ici — ce repo (`hugo-esposito-farese/comptallie-plugin`) est
déjà public et à jour sur `main`.

## 2. Ajouter le plugin dans Claude.ai

1. Ouvrir [claude.ai](https://claude.ai), aller dans **Customize** (menu de
   gauche) → onglet **Plugins**.
2. Dans la section **Personal plugins**, cliquer sur **"+"**, puis
   **"Add marketplace"**.
3. Choisir **"Add from a repository"** et coller l'URL de CE repo :
   `https://github.com/hugo-esposito-farese/comptallie-plugin`
4. Une fois la marketplace `comptallie-plugins` ajoutée, installer le
   plugin `comptallie` qui y apparaît.
5. Si l'installation indique `Run /reload-plugins to activate.`, c'est une
   invite propre à Claude Code — sur Claude.ai web, il n'y a rien de plus à
   faire, le plugin est actif dès l'installation confirmée.

## 3. Test simple — présentation de Julie

Dans une nouvelle conversation Claude.ai, écrire :

```
salut
```

ou

```
appelle Julie
```

**Attendu** : une présentation chaleureuse en 1re personne ("Salut, je suis
Julie, ton assistante pour trier et répondre à tes mails..."), avec les
capacités listées, et une question de confirmation explicite à la fin
("Veux-tu que je m'en occupe ?").

## 4. Test spécifique — le bug de personnage est-il résolu ?

Poser, dans la même conversation, l'une puis l'autre de ces deux questions :

```
tu es qui ?
```

```
tu peux faire autre chose ?
```

**Attendu, dans les deux réponses** :
- Julie répond toujours en 1re personne, dans son personnage.
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
l'exécution — jamais écrits dans ce repo). Répondre "oui" à la question de
confirmation de Julie pour déclencher la séquence complète, et vérifier
dans le résumé final que le tri distingue bien mails traités / ignorés —
signe que les critères ont été appliqués, même si le contenu exact des
critères n'est jamais visible dans ce repo.

Vérifier aussi qu'aucun mail n'est jamais envoyé, seulement des brouillons
créés.

## 6. Test simple — présentation de Méline

Dans une nouvelle conversation Claude.ai, écrire :

```
appelle Méline
```

**Attendu** : une présentation chaleureuse en 1re personne ("Salut, je suis
Méline, je peux rechercher des entreprises..."), avec les capacités listées,
et une question de confirmation explicite à la fin.

## 7. Test spécifique — recherche réelle

```
Méline, trouve-moi des entreprises créées en Île-de-France ces 30 derniers
jours
```

**Attendu** : Méline appelle `rechercher_entreprises` avec une zone (région
Île-de-France traduite en code INSEE `11`) et une période, sans jamais
demander à l'utilisateur de préciser un code technique. Si peu ou aucun
résultat n'est trouvé, elle doit l'expliquer simplement (l'API publique ne
trie pas par date de création, donc une recherche par période sur une zone
large peut manquer des créations récentes) — jamais comme si elle avait
échoué ou planté. Comme pour Julie : le mot "Claude" ne doit jamais
apparaître.
