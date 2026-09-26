# Mesurer ce que coûte la mémoire

*[English](MEASURE.md)*

Comment on a mesuré la mémoire le 26 septembre 2026, ce qu'on a trouvé, et comment refaire la même
mesure chez toi. C'est une seule mesure sur une seule mémoire : refais-la avant de t'y fier.

## Ce qu'on mesure

| Grandeur | Question |
|---|---|
| **Coût fixe** | Combien de tokens la mémoire ajoute-t-elle à chaque appel ? |
| **Exactitude** | Claude retrouve-t-il ce que seule la mémoire sait ? |
| **Coût d'une réponse complète** | Combien de tokens, et combien de corrections, jusqu'à ce que la réponse contienne tout ce qu'elle doit ? |

Une réponse moins chère mais fausse ou incomplète n'est pas une économie. Chaque comparaison
ci-dessous compte les tokens **et** vérifie la réponse.

## Les deux configurations

- **Avec mémoire** : une session normale.
- **Sans mémoire** : `CLAUDE_CODE_DISABLE_AUTO_MEMORY=1`, plus une interdiction de lire chaque dossier
  du système de mémoire (le dossier de mémoire, les archives, les boîtes), par exemple
  `--disallowedTools 'Read(<dossier de mémoire>/**)'`.

Dans les deux : le même dossier de travail et le même `CLAUDE.md`, le même modèle, des outils en
lecture seule (`--disallowedTools Write Edit NotebookEdit Bash Agent WebFetch WebSearch`), les hooks
coupés (`--settings '{"disableAllHooks": true}'`), et l'interdiction de lire ce qui donnerait la
réponse (les fichiers du banc eux-mêmes, les transcripts de sessions `~/.claude/projects/**/*.jsonl`,
et `~/.claude/file-history/**`, où Claude Code garde des copies des fichiers qu'il a modifiés, fiches
de mémoire comprises).

## Banc 1 — des questions

Quinze questions en trois familles, chacune posée trois fois par configuration, une session neuve
`claude -p "<question>" --output-format json` par essai :

| Famille | Où est la réponse | Ce qu'elle mesure |
|---|---|---|
| A | seulement dans la mémoire | ce qu'on perd sans mémoire |
| B | dans `CLAUDE.md`, chargé dans les deux cas | **le témoin** : les deux doivent réussir au même coût |
| C | trouvable en cherchant dans les fichiers | si la mémoire raccourcit la recherche |

Chaque question a une réponse de référence et un à trois mots-clés qu'une bonne réponse doit
contenir. Alterne l'ordre des deux configurations d'un essai à l'autre.

## Banc 2 — des tâches réelles

Trois tâches du travail courant, chacune notée contre une liste des points qu'une bonne réponse doit
contenir, en trois configurations : avec mémoire, sans mémoire mais avec une courte réexplication
écrite par l'humain à l'avance, et sans rien. Trois essais chacune.

Note **à l'aveugle** : copie les réponses sous des noms tirés au hasard, et fais-les noter par un
agent qui ne sait pas quelle configuration a produit quelle réponse, et qui cite la phrase qui prouve
chaque point.

## Banc 3 — les corrections jusqu'à une réponse complète

Reprends chaque session du banc 2 (`claude -p "<correction>" --resume <id de session>`). Après chaque
réponse, un juge liste les points manquants ; une correction qui les nomme est renvoyée ; on
recommence jusqu'à ce que rien ne manque, trois corrections au plus. Compte toute la conversation.

Valide d'abord le juge contre la notation à l'aveugle. Ne le garde que là où il est souvent d'accord
**et** où ses erreurs vont dans les deux sens : un juge qui rate des points dans une seule
configuration lui envoie des corrections inutiles et fait pencher le résultat.

## Une vraie conversation

La mesure la plus proche de l'usage réel : deux sessions interactives sur la même tâche, une avec
mémoire, une sans. L'humain converse comme d'habitude. Fixe la règle d'arrêt **avant** de commencer :
une liste écrite de ce que le résultat fini doit contenir, un vérificateur neutre qui ne répond que
« atteint » ou « pas atteint » après chaque réponse, et un plafond de messages.

## Compter

- Prends les tokens dans `modelUsage` de la sortie JSON (entrée, écritures de cache, lectures de
  cache, sortie), pas dans le seul transcript : une tentative terminée sans réponse apparaît dans le
  premier, pas toujours dans le second.
- Le `total_cost_usd` du JSON est une estimation faite par le client, au prix public.
- Dans une session interactive, additionne le `usage` de chaque message de l'assistant dans le
  transcript, une fois par identifiant de message.

## Les pièges rencontrés

- **Le changement de modèle.** Une session peut basculer sur un autre modèle en cours de route (on a
  vu des refus de sécurité suivis d'un repli). Elle mesure alors le repli, pas la mémoire : mets-la de
  côté et relance-la.
- **Les fuites.** Les interdictions de lecture s'appliquent à Grep et Glob « au mieux ». Cherche dans
  les transcripts toute lecture *aboutie* d'un chemin interdit ; une lecture refusée ne fausse rien.
- **L'apprentissage.** La seconde de deux vraies conversations profite de la première. Inverse l'ordre
  sur une deuxième tâche, ou prends deux tâches différentes de même difficulté.
- **Des corrections parfaites.** Des corrections écrites à partir de la liste sont plus précises que
  celles d'un humain, ce qui avantage la configuration sans mémoire.
- **Ne modifie jamais un script bash pendant qu'il tourne** : bash le lit au fur et à mesure.

## Ce qu'on a trouvé (26 septembre 2026, une mémoire d'environ 200 fiches)

| Mesure | Avec mémoire | Sans |
|---|---|---|
| Tokens ajoutés par appel (réponses en une étape) | environ +8 200 | — |
| Famille A, bonnes réponses | 15 / 15 | 2 / 14 |
| Corrections nécessaires, banc 3 (8 sessions par configuration) | 9 | 15 |
| Tokens jusqu'à une réponse complète, banc 3 | de 41 % de moins à 36 % de plus, selon la tâche | — |
| Vraie conversation, tokens | 1 357 536 | 1 773 474 |

Limites : une seule mémoire, des questions et des tâches écrites par son propriétaire, trois essais
par cas, une seule vraie conversation par configuration, et trois biais qui avantagent la mémoire
dans la vraie conversation (l'ordre, la liste collée un message plus tôt, un seul essai).

## Prochaine mesure

Avec un traducteur, une étape qui transforme la demande en consignes claires dès le départ, comparer
avec et sans mémoire : une mémoire déjà posée consomme-t-elle moins qu'une consigne transmise à
chaque fois ?
