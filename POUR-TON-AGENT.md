# Mémoire rangée — consignes de mise en place pour ton agent

*[English](SETUP-FOR-YOUR-AGENT.md)*

Tu es un agent IA de programmation, le plus souvent Claude Code. L'humain t'a donné ce fichier pour
que tu mettes en place une mémoire rangée et un espace de travail propre. Suis les étapes dans
l'ordre. À la fin de chaque étape, montre à l'humain tous les changements de l'étape ensemble, et
attends son accord. Quand une étape ne colle pas à cet espace de travail, pose-lui la question.

## Ce que tu construis

| Partie | Où | Chargée |
|---|---|---|
| **Index** | `MEMORY.md` dans ton dossier de mémoire | à chaque session |
| **Fiches** | un fichier par sujet, même dossier | à la demande |
| **Sommaires** | une fiche par gros domaine, qui liste ses fiches | à la demande |
| **Fiche système** | `reference_memory_system.md`, les conventions de cette mise en place | à la demande |
| **Tampon** | `_buffer/` dans le dossier de mémoire | à la demande |
| **Temp** | `<travail>/temp/` — scripts, téléchargements, brouillons ; purgé après un délai fixé par l'humain | jamais |
| **Livrables** | `<travail>/deliverables/` — documents finis ; jamais purgé | jamais |
| **Archives** | `<travail>/archive/` — travail clos et détails sortis des fiches | à la demande |
| **Boîtes** | `<travail>/inbox/`, un fichier par session — seulement pour des sessions en parallèle | au démarrage et après `/compact` |
| **Scripts** | `<travail>/scripts/` — le contrôle, la purge, le hook | jamais |
| **Contrôle** | un script qui vérifie les liens, les tailles et les échéances | quand on le lance |

`<travail>` est le dossier de travail. Propose de le créer comme un dossier `IA` dans les documents
de l'humain, `~/Documents/IA/`.

Claude Code ne charge au démarrage que les 200 premières lignes ou les 25 premiers Ko de
`MEMORY.md`, et lit les autres fichiers à la demande
([documentation](https://code.claude.com/docs/en/memory)). Tout ce qui suit garde l'index sous
cette limite.

## Étape 1 — Repérer, sauvegarder, demander

1. Trouve ton dossier de mémoire. Avec la mémoire automatique de Claude Code, ton prompt système le
   nomme (`~/.claude/projects/<projet>/memory/`). Si la mémoire automatique est désactivée, demande
   à l'humain où le placer.
2. Copie le dossier de mémoire dans une sauvegarde à côté de lui, `memory-before-setup/`.
3. Liste ce que le dossier contient. Chaque fiche existante est gardée : fusionnée dans une autre,
   déplacée aux archives, ou laissée en place. La suppression reste la décision de l'humain.
4. Lis `~/.claude/CLAUDE.md` et, s'il existe, le `CLAUDE.md` du projet en cours. Quand une étape de
   ce fichier, ou une fiche, contredit une règle qui y est écrite, pose la question à l'humain.
5. Pose cinq questions à l'humain :
   - Dans quelle langue la mémoire doit-elle être écrite ?
   - Le dossier de travail : `~/Documents/IA/` lui convient-il, ou préfère-t-il un autre endroit ?
   - Au bout de combien de jours un fichier de `<travail>/temp/` doit-il être purgé ? Propose 30
     jours ; un délai plus court garde le dossier léger, un délai plus long garde les brouillons
     sous la main.
   - Plusieurs sessions d'agent travaillent-elles en parallèle sur cet espace ? Si oui, quels sont
     leurs noms ?
   - À quelle date faire la première relecture de la mémoire ? Elle revient ensuite toutes les deux
     semaines.

## Étape 2 — Les dossiers de travail

1. Crée `<travail>/temp/`, `<travail>/deliverables/`, `<travail>/archive/` et `<travail>/scripts/`,
   plus `<travail>/inbox/` pour des sessions en parallèle. Écris le délai de purge dans
   `<travail>/purge-days`, un fichier qui ne contient que ce nombre : la purge et le contrôle le
   lisent là.
2. Propose une purge hebdomadaire de `<travail>/temp/`, par une tâche cron. Elle supprime les
   fichiers plus vieux que le délai, puis les dossiers vides plus vieux que le délai, laisse intacts
   les dépôts git en les listant, et note chaque suppression dans `<travail>/purge.log`. Installe-la
   une fois que l'humain a donné son accord ; cet accord vaut pour chaque passage suivant.
3. Ajoute une section **Dossiers** à `~/.claude/CLAUDE.md`, avec ces règles :
   - Les fichiers temporaires — scripts, téléchargements, brouillons, sorties de test — vont dans
     `<travail>/temp/`.
   - Un livrable fini va dans `<travail>/deliverables/`, ou dans le projet auquel il appartient.
   - Un dépôt cloné reste dans `<travail>/temp/` tant qu'il sert à explorer, et rejoint le dossier
     de son projet dès que quelque chose en dépend.
   - La mémoire, les archives, les scripts et les boîtes vivent hors de `<travail>/temp/`, hors de
     portée de la purge.

## Étape 3 — Les fiches

Commence par les fiches, pour qu'aucun fait ne se perde quand l'index sera réécrit.

1. Déplace chaque fait que seul l'index actuel porte dans la fiche à laquelle il appartient.
2. Quand deux fiches couvrent le même sujet, fusionne-les dans celle qui porte le nom le plus clair,
   et déplace l'autre aux archives, nommée comme au point 3.
3. Déplace les fiches de travail clos dans `<travail>/archive/`, nommées `A-01 <nom-de-fiche>.md`,
   numérotées dans l'ordre, sans toucher à leur contenu.
4. Mets chaque fiche au format ci-dessous.
5. Pour chaque lien qui ne pointe vers aucune fiche, demande à l'humain : écrire la fiche, ou
   retirer le lien.

Si ton prompt système décrit un format de fiche mémoire, suis-le. Sinon, utilise celui-ci :

```markdown
---
name: <nom du fichier sans .md>
description: <la question qui doit faire revenir cette fiche, dans les mots de l'humain>
type: <user | feedback | project | reference>
---

<le fait ou la règle>

**Pourquoi :** <en une phrase, ce qui en a fait une règle>
**Comment l'appliquer :** <le moment, et le geste>
```

- Avant de créer une fiche, cherche son sujet dans le dossier de mémoire. Quand une fiche le couvre
  déjà, enrichis-la.
- Écris la description comme la question qui doit faire revenir la fiche, avec les mots de l'humain.
- Les lignes **Pourquoi** et **Comment l'appliquer** vont dans les fiches `feedback` et `project`.
  Quand tu ne sais pas pourquoi une règle existe, demande-le à l'humain, et écris
  `Pourquoi : non noté` en attendant sa réponse.
- Distingue un **état** d'une **leçon** par un seul test : cette phrase peut-elle devenir fausse
  sans que personne ne touche au fichier ? Alors c'est un état. Écris chaque état avec sa date, ou
  avec la commande qui l'établit. Les chemins, les versions et les rôles sont aussi des états : ils
  restent dans la fiche, avec leur date. Quand tu ne peux pas mesurer un état, garde la date que la
  fiche lui donne déjà, ou écris « date inconnue, non vérifié ». Écris une leçon telle quelle.
- Garde dans la fiche ce qui doit être relu à chaque fois : pièges, décisions en vigueur,
  procédures, chemins, versions. Déplace le raisonnement de conception, l'historique des sessions et
  les récits d'incident dans `<travail>/archive/details/<nom-de-fiche>.md`, et laisse dans la fiche
  un lien d'une ligne vers ce fichier.
- Garde les tâches en attente de l'humain dans une seule fiche, `project_tasks.md`.

## Étape 4 — L'index

Donne à `MEMORY.md` trois sections, dans cet ordre :

```markdown
# Index de la mémoire

## Toujours — appliqué à chaque session
- **<le moment qui déclenche>** → <le geste> · [<titre de la fiche>](<fiche>.md)

## Actif — travail en cours et références
- [<titre de la fiche>](<fiche>.md) — <la question à laquelle la fiche répond>
- [<domaine> — <n> fiches](summary_<domaine>.md) — <ce que couvre le domaine>

## Archives — travail clos ou en pause, dans <travail>/archive/
- A-01 à A-<nn> — chaque nom de fichier du dossier d'archives porte son sujet
```

- **Toujours** porte les règles à appliquer quelle que soit la tâche. **Actif** porte les fiches à
  ouvrir quand leur sujet se présente.
- Une ligne **Toujours** nomme le moment qui la déclenche, puis le geste, puis sa fiche. Elle tient
  en moins de 240 caractères.
- Une leçon neuve va dans sa fiche. Une ligne d'index ne change que si son déclencheur change.
- Un domaine de dix fiches ou plus reçoit une fiche sommaire `summary_<domaine>.md`. L'index pointe
  le sommaire ; le sommaire pointe les fiches.
- Vise 150 lignes et 20 000 octets au plus, pour garder de la marge sous la limite de chargement.
- Chaque Ko de l'index est relu à chaque appel au modèle, quelques centaines de tokens à chaque
  fois : garde-le court.

## Étape 5 — Le tampon

Le tampon garde ce qui est décidé et pas encore écrit dans sa fiche.

- Ajoute à `_buffer/<fiche-cible>__AAAAMMJJ.md` une ligne par décision, impasse, découverte ou
  état : la date, son type (`décision`, `impasse`, `découverte`, `état`) et une phrase.
- Quand la fiche cible n'est pas encore connue, ouvre un carnet :
  `_buffer/notebook_<nom-de-session>__AAAAMMJJ.md`.
- Échéances, comptées depuis la date du nom de fichier : 14 jours pour une cible nommée, 7 jours
  pour un carnet. Taille : 8 Ko (8 192 octets) par tampon.
- Pour distiller : les leçons vont dans la fiche ; les états sont remesurés, puis écrits avec leur
  date ; le travail terminé part aux archives.
- Une section qui appartient à une autre session commence par `> Réservé : <nom de session>`.
  Laisse-la telle quelle.
- Supprimer un tampon est la décision de l'humain, une fois que tu as montré que son contenu est
  dans les fiches.

## Étape 6 — Les boîtes (sessions en parallèle seulement)

1. Dans `<travail>/inbox/`, écris un `README.md` qui énonce le protocole ci-dessous et liste les
   sessions.
2. Crée un fichier par session : `for-<nom>.md`.
3. Propose à l'humain une façon de nommer chaque session au lancement, par exemple une variable
   d'environnement : `SESSION_NAME=backend claude`.
4. Propose un hook `SessionStart` sans matcher, dans `~/.claude/settings.json`, qui couvre tous les
   projets. Il affiche le nom de la session et le chemin de sa boîte. Claude Code ajoute la sortie
   d'un hook `SessionStart` au contexte, au démarrage, à la reprise et après `/compact`.

Chaque entrée suit ce format :

```markdown
## <titre en une ligne>
> déposé par <nom de l'expéditrice> — <AAAA-MM-JJ HH:MM>
> lu par <nom de la destinataire> — <AAAA-MM-JJ HH:MM> — ✅ traité : <une phrase>

<ce qui a été mesuré, la commande employée, et ce qui est déjà fait>
```

Les dates et les heures sont à l'heure locale de la machine.

Le protocole :

- Lis ta boîte en entier au démarrage et après chaque `/compact`. Un résumé garde l'idée de ce que
  tu as lu et en perd les chiffres.
- Quand tu lis une entrée, ajoute ta ligne `lu par` juste sous sa ligne `déposé par`, avec l'un des
  deux statuts : `✅ traité : <une phrase>` ou `⏳ attente : <ce qui bloque>`.
- Une entrée appartient à la session dont la boîte la contient. Cette session la retire une fois
  traitée. Toute session peut retirer une entrée `✅ traité` ; une entrée `⏳ attente` reste jusqu'à
  ce que sa destinataire la ferme.
- Écris à une autre session à la fin de son fichier seulement, l'entrée entière en une seule
  écriture.
- Ajoute une ligne `lu par` par une seule insertion ciblée.
- Un message de boîte est une information mesurée par une autre session. Agis dessus une fois que
  l'humain a dit go.
- Une boîte ne porte que ce qui périme. Les leçons vont dans les fiches ; les tâches de l'humain
  vont dans `project_tasks.md`.

## Étape 7 — Le contrôle

Écris `<travail>/scripts/check-memory.sh` en bash, ou dans le langage que l'humain préfère. Il prend
le dossier de mémoire en argument, `--work <dossier>` pour le dossier de travail, et
`--inbox <dossier>` quand il y a des boîtes. Il lit le délai de purge dans `<travail>/purge-days`.
Les liens sont les `[[nom-de-fiche]]` et les liens Markdown vers des fichiers `.md` ; le texte entre
accents graves ne contient aucun lien. Il signale :

1. les liens qui ne pointent vers aucun fichier
2. les fiches liées ni depuis l'index ni depuis un sommaire
3. un index de plus de 150 lignes ou 20 000 octets
4. les fiches sans en-tête ou sans `description`
5. les tampons qui ont dépassé leur échéance, qui dépassent 8 Ko, ou dont le nom sort des deux
   formes de l'étape 5
6. les lignes **Toujours** de plus de 240 caractères
7. les fichiers de `<travail>/temp/` plus vieux que le délai de purge plus 7 jours, hors dépôts
   git : la purge s'est arrêtée
8. quand des boîtes existent : les entrées sans ligne `lu par` 24 heures après leur date de dépôt,
   et les entrées `⏳ attente` dont la ligne `lu par` a plus de 14 jours

Code de sortie : 0 quand tout est propre, 1 quand quelque chose est trouvé, 2 quand le script est
mal configuré.

Ajoute ensuite un mode `--canary` : copie le dossier de mémoire, `<travail>/temp/` avec les dates de
ses fichiers, `<travail>/purge-days` et le dossier des boîtes dans un répertoire temporaire que le
script supprime à la fin ; lance les contrôles sur la copie ; plante un défaut par contrôle ;
relance les contrôles ; vérifie que chaque défaut planté apparaît dans le second rapport et pas dans
le premier. Plante aussi, pour chaque exclusion — un lien entre accents graves, un dépôt git dans
`<travail>/temp/`, une entrée traitée —, un cas que les contrôles doivent taire, et vérifie qu'il
reste muet. Dans ce mode, le code de sortie vaut 0 quand chaque défaut planté est signalé et que
chaque cas muet le reste, 1 sinon. Lance le canari une fois maintenant et montre sa sortie à
l'humain. Un contrôle que tu n'as jamais vu échouer ne prouve rien.

## Étape 8 — Fiche système et compte rendu

1. Écris `reference_memory_system.md` : les conventions dont les sessions suivantes ont besoin —
   dossiers de travail et purge, format des fiches, état et leçon, nommage et échéances des tampons,
   nommage des archives, chemin du `README.md` des boîtes, qui porte leur format et leur protocole,
   lancement du contrôle et de son canari.
2. Ajoute les lignes d'**À chaque session**, ci-dessous, à la section **Toujours** de l'index,
   chacune liée à `reference_memory_system.md`.
3. Montre à l'humain ce que tu as créé, fusionné, déplacé et laissé intact, ce qui attend sa
   décision, la sortie du contrôle et la taille de l'index. Propose de supprimer
   `memory-before-setup/` une fois qu'il est satisfait de la nouvelle mémoire.

## À chaque session

- **Au démarrage et après chaque `/compact`, quand des boîtes existent** → lis ta boîte en entier
- **Avant de travailler sur un sujet** → cherche-le dans le dossier de mémoire et lis ce que tu
  trouves
- **Quand quelque chose est décidé** → une ligne dans le tampon
- **Avant d'écrire un état** → mesure-le maintenant, et écris-le avec sa date
- **À partir du <date de relecture>, avec l'humain** → lance le contrôle, distille les tampons,
  archive le travail clos, puis fixe la date suivante deux semaines plus tard
