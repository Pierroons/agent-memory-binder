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
| **Registre des chantiers** | `<travail>/inbox/work-in-progress.md` — ce que chaque session fait en ce moment — seulement pour des sessions en parallèle | au démarrage et après `/compact` |
| **Journal des envois** | `<travail>/inbox/sends-AAAA-MM.md` — une ligne par publication sur une branche git partagée | sa dernière ligne, au démarrage |
| **Scripts** | `<travail>/scripts/` — le contrôle, la purge, les hooks, le script d'envoi | jamais |
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
     leurs noms, et publient-elles sur une branche git partagée ? Laquelle ?
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
   projets. Il affiche le nom de la session et le chemin de sa boîte ; sans nom, ou avec un nom qui
   n'a pas de boîte, il le dit et liste les boîtes. Claude Code ajoute la sortie d'un hook `SessionStart` au contexte, au démarrage, à la reprise et après `/compact`.

Chaque entrée suit ce format :

```markdown
## <titre en une ligne>
> déposé par <nom de l'expéditrice> — <AAAA-MM-JJ HH:MM>
> lu par <nom de la destinataire> — <AAAA-MM-JJ HH:MM> — ✅ traité : <une phrase>

<ce qui a été mesuré, la commande employée, et ce qui est déjà fait>
```

Les dates et les heures sont à l'heure locale de la machine. L'expéditrice écrit le titre, la ligne
`déposé par` et le corps ; la destinataire ajoute la ligne `lu par`. Une entrée commence à un titre
`## ` hors des blocs de code et finit au suivant.

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

**Les états.** Un état est une phrase qui peut devenir fausse sans que personne ne touche au fichier
(étape 3). Une entrée qui rapporte un état suit un circuit fermé, pour qu'aucune session ne continue
de travailler sur un fait qu'une autre a déjà corrigé :

```markdown
## 📌 ÉTAT <expéditrice>-<MMJJ>-<HHMM> — <titre en une ligne>
> déposé par <nom de l'expéditrice> — <AAAA-MM-JJ HH:MM> — retour attendu
> lu par <nom de la destinataire> — <AAAA-MM-JJ HH:MM> — ⏳ attente : <ce qui bloque>
```

- L'identifiant est le nom de l'expéditrice suivi du mois, du jour, de l'heure et de la minute du
  dépôt. L'expéditrice écrit `retour attendu` ou `sans retour attendu`.
- La destinataire ajoute sa ligne `lu par` dès qu'elle lit l'entrée, comme pour toute entrée. Une
  fois l'état traité, elle remplace `⏳ attente : …` sur cette même ligne par
  `✅ traité : <ce qu'elle a mesuré, la commande, et quand>`.
- Quand un retour est attendu, la destinataire écrit ensuite à la fin de la boîte de l'expéditrice
  une entrée titrée `## ↩️ RETOUR <identifiant> — traité`, avec sa ligne `déposé par` et ce qu'elle
  a mesuré, et retire l'état de sa propre boîte. L'expéditrice lit le retour et le retire : plus
  personne ne porte le sujet.
- Une leçon, une information ou une question ouverte reste une entrée ordinaire. Seuls les états
  suivent ce circuit, parce que seuls ils deviennent faux tout seuls.

## Étape 7 — Le registre des chantiers et la branche partagée (sessions en parallèle seulement)

Les boîtes portent ce qui est fini. Elles ne disent pas ce qu'une autre session fait en ce moment,
ni quand elle publie : deux sessions peuvent corriger la même chose sur deux branches, ou envoyer
sur la même branche à une minute d'écart. Cette étape ferme ces deux trous.

**Le registre.**

1. Crée `<travail>/inbox/work-in-progress.md`, avec le protocole ci-dessous en tête et le modèle de
   bloc dans un bloc de code. Dans le registre, tout titre `## ` hors d'un bloc de code est un bloc :
   écris le protocole sans titre `## `. Le hook, le script d'envoi et le contrôle sautent les blocs
   de code.
2. Avant de choisir un sujet, lis le registre. Une fois le sujet choisi, et avant d'ouvrir le
   premier fichier, ajoute ce bloc à la fin du registre :

```markdown
## 🔨 <session>-<MMJJ>-<HHMM> — <ce que tu vas faire, en une ligne>
> ouvert par <nom de la session> — <AAAA-MM-JJ HH:MM>
Dépôt : <nom> · Branche : <la branche distante où tu enverras> · Cible : <fichiers ou dossiers>
```

3. Quand le travail est publié, ajoute une ligne sous le bloc :
   `> ✅ clos — <AAAA-MM-JJ HH:MM> — <SHA des commits>`.

Le protocole :

- La description réserve le sujet. Un SHA de commit n'existe qu'après le commit, trop tard pour la
  session qui avait besoin de te lire, et un rebase le change : il va sur la ligne de clôture
  seulement.
- Ajoute un bloc à la fin du fichier, et une ligne par une seule insertion ciblée. Ne réécris
  jamais le fichier entier : une réécriture efface le bloc qu'une autre session vient d'ajouter.
- Le registre est un tableau d'affichage, pas un verrou. Quand un bloc ouvert recouvre ton sujet,
  dis-le à l'humain avant de commencer.
- Les blocs clos partent dans `<travail>/inbox/work-archive-AAAA-MM.md` à chaque promotion vers la
  branche principale, ou à la relecture.

**Une branche par session**, quand les sessions partagent un dépôt git :

4. Depuis un clone du dépôt, donne à chaque session son propre arbre de travail et sa branche,
   partis de la branche partagée nommée à l'étape 1, ici `dev` :
   `git fetch origin && git worktree add --no-track -b session/<nom> <chemin> origin/dev`. Pars
   d'`origin/dev` : dans un clone sans `dev` local, `… -b session/<nom> <chemin> dev` fait créer à
   git un `dev` local en ignorant `-b`, et la session travaille sur `dev`. `--no-track` empêche un
   `git push` nu de viser `dev`.

**Le script d'envoi.**

5. Écris `<travail>/scripts/send.sh <arbre> [--main]`. C'est l'humain qui le lance ; une session
   lui donne la commande et ne la lance jamais. Il lit le dossier des boîtes dans `SEND_INBOX`, par
   défaut `<travail>/inbox`. L'URL de la forge qu'il sert est écrite une seule fois, en tête du
   script ; il la compare à `git config remote.origin.url`, parce que `git remote get-url`
   applique `insteadOf` et rend une autre adresse. Il lance git avec
   `LC_ALL=C`, pour que les messages de git se lisent de la même façon sur toutes les machines.
   Sans `--main`, il :
   - refuse un arbre qui a des modifications non commitées, ou qui est sur `dev` ou `main`, et
     s'arrête sans rien inscrire quand la branche n'a rien à envoyer ;
   - récupère la forge (`fetch`), puis fusionne `origin/dev` dans la branche de la session quand
     `dev` a bougé. Il ne fait jamais de rebase. Sur un conflit, il abandonne la fusion, nomme les
     fichiers en conflit et s'arrête, `dev` intact ;
   - envoie la branche de la session sur `dev` et sur elle-même en un seul envoi atomique
     (`git push --atomic`) ;
   - reprend depuis la récupération, jusqu'à trois reprises, quand l'envoi est refusé parce que
     `dev` a bougé entre-temps, et seulement dans ce cas. Git le signale sous deux formes :
     `[rejected]` avec `(fetch first)` ou `(non-fast-forward)` ; et, quand deux envois atteignent
     la forge au même instant, `[remote rejected]` avec `cannot lock ref` sur une ligne `remote:`.
     Tout autre refus, d'un hook ou de la forge, l'arrête ;
   - vérifie après l'envoi, avec `git ls-remote`, que `dev` sur la forge contient le commit envoyé ;
   - ajoute une ligne à `<travail>/inbox/sends-AAAA-MM.md` :
     `- AAAA-MM-JJ HH:MM · dev · <SHA court> ← <branche> · <n> commit(s) · <sujet>`, où `<n>`
     compte les commits nouveaux sur `dev`, fusion comprise, et `<sujet>` est le sujet du dernier
     commit de la session elle-même (`git log --first-parent --no-merges -1`). La ligne se termine
     par `· reprise` quand il a dû recommencer.

   Avec `--main`, il avance `main` jusqu'à `dev` seulement en avance rapide, une fois les
   vérifications de la forge passées sur `dev`. Il les lit avec l'outil en ligne de commande de la
   forge (pour GitHub, `gh api repos/<owner>/<repo>/commits/<SHA>/check-runs`) toutes les
   30 secondes tant qu'elles ne sont pas finies ou pas commencées, 15 minutes au plus. Puis il déplace les blocs clos du registre dans l'archive du mois, en ne réécrivant le
   registre que s'il n'a pas changé depuis que le script l'a lu ; sinon les blocs attendent la
   relecture. Il inscrit la ligne avec `main` et `← dev`.
6. Écris `<travail>/scripts/send-bench.sh`. Il trompe le script de l'extérieur seulement : un dépôt
   nu local tient lieu de forge (`git config url.<chemin local>.insteadOf <URL de la forge>`), un
   faux outil en ligne de commande de la forge, placé en tête du `PATH`, rend les résultats des
   vérifications, `SEND_INBOX` désigne un dossier de boîtes temporaire, et
   `GIT_CONFIG_GLOBAL=/dev/null` avec `GIT_CONFIG_NOSYSTEM=1` tient à l'écart les réglages git de
   l'humain. Un faux `git` en tête du `PATH`, ou un hook `reference-transaction` dans le clone,
   fait envoyer une autre session entre la récupération et l'envoi ; un faux `sleep` abrège les
   attentes de `--main`. Les cas :
   - deux envois dans la même seconde passent tous les deux, l'un inscrit avec `· reprise` ; un
     hook `pre-receive` du dépôt nu qui attend deux secondes les fait vraiment se chevaucher ;
   - une autre session envoie entre la récupération et l'envoi : l'envoi passe, avec `· reprise` ;
   - un conflit arrête l'envoi, `dev` inchangé ;
   - un hook `pre-push` qui refuse arrête l'envoi sans reprise ;
   - des vérifications en échec bloquent `--main`, et des vérifications en cours puis passées le
     laissent faire ;
   - un dépôt autre que celui configuré est refusé.

   Encadre la fusion et la reprise dans `send.sh` par des lignes de commentaire (`# [merge]` …
   `# [/merge]`, `# [retry]` … `# [/retry]`). À chaque passage, le banc retire chaque région
   encadrée d'une copie du script, vérifie que des lignes ont été retirées et que la copie passe
   `bash -n`, et vérifie qu'il échoue sur cette copie. Lance-le et montre sa sortie à l'humain.

**Le hook qui laisse la publication à l'humain.**

7. Propose un hook `PreToolUse` avec le matcher `Bash`, dans `~/.claude/settings.json`, qui refuse
   `git push` et le script d'envoi quand c'est toi qui les lances, avec une raison qui te dit de
   donner la commande à l'humain. Il lit la commande avec un découpage qui respecte les guillemets
   et les redirections (`2>&1` ne sépare rien), et la coupe en segments sur `|`, `;`, `&`, `&&`,
   `||`, les retours à la ligne, les sous-shells, `$( )` et les accents graves. Dans chaque segment,
   il saute les affectations de variables, les mots-clés du shell (`if`, `then`, `do`, `{`, `!` …),
   les enveloppes avec leurs options et arguments (`env`, `command`, `exec`, `nohup`, `time`,
   `nice`, `timeout 30`, `sudo -u <utilisateur>`) et les options globales de git (`-C <chemin>`,
   `-c <clé=valeur>`, `--git-dir=…`, `--work-tree=…`). Il juge le nom du script passé à `bash`,
   `sh` ou `source`, sans le lire, pour que le banc reste permis, et rejuge le texte passé à `-c`
   ou à `eval` ; sinon il juge le premier mot restant.
   Une recherche du texte `git push` n'est pas un envoi. Éprouve-le sur ces commandes avant de
   l'installer :

| commande | attendu |
|---|---|
| `git push origin dev` | refusé |
| `git -C ../depot push origin dev` | refusé |
| `cd depot && git  push origin dev` (deux espaces) | refusé |
| `git --git-dir=/x/.git push origin dev` | refusé |
| `<travail>/scripts/send.sh ../depot` | refusé |
| `bash <travail>/scripts/send.sh ../depot` | refusé |
| `sh -c 'git push origin dev'` | refusé |
| `echo "$(git push origin dev)"` | refusé |
| `timeout 30 git push origin dev` | refusé |
| `bash <travail>/scripts/send-bench.sh` | accepté |
| `grep -rn 'git push' docs/ 2>&1` | accepté |
| `echo 'ne lance pas git push toi-même'` | accepté |
| `git log --oneline origin/dev..HEAD` | accepté |

8. Étends le hook `SessionStart` de l'étape 6 : après la boîte, il affiche les blocs ouverts du
   registre et la dernière ligne du journal des envois le plus récent.

## Étape 8 — Le contrôle

Écris `<travail>/scripts/check-memory.sh` en bash, ou dans le langage que l'humain préfère. Il prend
le dossier de mémoire en argument, `--work <dossier>` pour le dossier de travail, et
`--inbox <dossier>` pour le dossier des boîtes, `<travail>/inbox` par défaut ; son rapport nomme le
dossier des boîtes qu'il a lu. Il lit le délai de purge dans `<travail>/purge-days`.
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
   les entrées ordinaires `⏳ attente` dont la ligne `lu par` a plus de 14 jours, les états encore
   `⏳ attente` 24 heures après leur ligne `lu par`, et les entrées dont la ligne `déposé par` ou
   `lu par` ne porte aucune date lisible
9. quand le registre existe : plus de 12 blocs ouverts, les blocs ouverts depuis plus de 7 jours, et
   les blocs ouverts sans date lisible

Code de sortie : 0 quand tout est propre, 1 quand quelque chose est trouvé, 2 quand le script est
mal configuré.

Ajoute ensuite un mode `--canary` : copie le dossier de mémoire, `<travail>/temp/` avec les dates de
ses fichiers, `<travail>/purge-days` et le dossier des boîtes dans un répertoire temporaire que le
script supprime à la fin ; lance les contrôles sur la copie ; plante un défaut par condition de
chaque contrôle ; relance les contrôles ; vérifie que chaque défaut planté apparaît dans le second
rapport et pas dans le premier. Plante aussi, pour chaque exclusion — un lien entre accents graves,
un dépôt git dans `<travail>/temp/`, une entrée traitée, un état traité, un bloc clos de plus de
7 jours, une entrée déposée il y a une heure et pas encore lue, une entrée ordinaire en attente
depuis trois jours, un titre `## ` dans un bloc de code —, un cas que les contrôles doivent taire, et vérifie qu'il reste muet. Dans ce mode, le code de sortie vaut 0 quand chaque défaut planté est signalé et que
chaque cas muet le reste, 1 sinon. Lance le canari une fois maintenant et montre sa sortie à
l'humain. Un contrôle que tu n'as jamais vu échouer ne prouve rien.

## Étape 9 — Fiche système et compte rendu

1. Écris `reference_memory_system.md` : les conventions dont les sessions suivantes ont besoin —
   dossiers de travail et purge, format des fiches, état et leçon, nommage et échéances des tampons,
   nommage des archives, chemin du `README.md` des boîtes, qui porte leur format et leur protocole,
   chemins du registre, du script d'envoi et de son banc, lancement du contrôle et de son canari.
2. Ajoute les lignes d'**À chaque session**, ci-dessous, à la section **Toujours** de l'index,
   chacune liée à `reference_memory_system.md`.
3. Montre à l'humain ce que tu as créé, fusionné, déplacé et laissé intact, ce qui attend sa
   décision, la sortie du contrôle et la taille de l'index. Propose de supprimer
   `memory-before-setup/` une fois qu'il est satisfait de la nouvelle mémoire.

## À chaque session

- **Au démarrage et après chaque `/compact`, quand des boîtes existent** → lis ta boîte en entier
- **Avant de travailler sur un sujet** → cherche-le dans le dossier de mémoire et lis ce que tu
  trouves
- **Avant de choisir un sujet, quand des sessions travaillent en parallèle** → lis le registre des
  chantiers, puis ajoute ton bloc avant d'ouvrir le premier fichier
- **Pour publier sur la branche partagée** → donne la commande d'envoi à l'humain ; n'envoie jamais
  toi-même
- **Quand quelque chose est décidé** → une ligne dans le tampon
- **Avant d'écrire un état** → mesure-le maintenant, et écris-le avec sa date
- **Avant de te fier à un contrôle qui passe** → vois-le d'abord échouer sur le défaut qu'il vise
- **À partir du <date de relecture>, avec l'humain** → lance le contrôle et son canari, distille
  les tampons, archive le travail clos, puis fixe la date suivante deux semaines plus tard
