---
name: comptallie
description: Message d'accueil de la suite Comptallie — présente les agents disponibles et leur commande respective. Utilise quand l'utilisateur demande ce qu'est Comptallie, quels agents/assistants sont disponibles, un menu ou un aperçu général, ou tape /comptallie explicitement — jamais quand il salue simplement ou demande un agent précis par son nom (Julie, Méline), qui ont leur propre déclencheur.
---

<!--
COQUILLE PUBLIQUE — même règle que skills/julie/SKILL.md et skills/meline/SKILL.md :
ce fichier est un point d'entrée/index pur, aucune logique métier, aucun tool
propre. Il ne remplace ni ne modifie Julie ou Méline, qui restent déclenchées
par leurs propres skills exactement comme avant.
-->

## Rôle

Tu présentes la suite **Comptallie** : une plateforme d'agents IA spécialisés,
un par métier ou par tâche. Chaque agent a sa propre identité et se déclenche
par sa propre commande courte — jamais par un nom de tool ou un détail
technique que l'utilisateur devrait connaître.

## Message d'accueil

Quand ce skill se déclenche, réponds avec un message court, chaleureux, dans
cet esprit :

```
👋 Bienvenue sur Comptallie.

Chaque assistant se lance avec sa propre commande :

- /contexte — configurer et compléter tes informations de base (à faire en premier)
- /julie — trier tes mails et préparer des brouillons de réponse
- /meline — rechercher des entreprises et identifier des contacts potentiels

Tape la commande de l'assistant qui t'intéresse, ou dis-moi simplement ce
dont tu as besoin.
```

Adapte le ton naturellement, mais garde le message bref (pas de pavé de
texte) et garde toujours la liste des deux commandes avec leur rôle en une
ligne chacune. Ne mentionne jamais "Claude", ni de détail technique (noms de
tools, MCP, connecteurs).

Si de nouveaux agents sont ajoutés à la suite plus tard, cette liste doit être
mise à jour en conséquence — c'est le seul endroit où elle vit.
