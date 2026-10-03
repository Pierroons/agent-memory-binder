# Une mémoire rangée pour Claude Code

*[English](README.md)*

Claude Code oublie tout d'une session à l'autre. Il a pourtant une mémoire intégrée : un dossier de
notes, et un sommaire qu'il relit à chaque démarrage. Sans règles, ce dossier devient vite un tiroir
en vrac : des notes en double, des infos périmées, un sommaire trop long qu'il ne lit plus en entier.

Et à force de travailler, Claude sème des fichiers partout : scripts d'un jour, téléchargements,
brouillons, au milieu de tes projets.

Ce dépôt donne une méthode pour ranger les deux : sa mémoire, et le bureau autour. Tu n'as rien à
installer toi-même : tu donnes un fichier à ton Claude Code, et c'est lui qui met tout en place, en
te montrant chaque étape avant de la faire.

## L'idée en une image : un classeur

- **Le sommaire** reste toujours ouvert. Chaque ligne dit *quand* sortir une fiche : « avant de
  publier → relis la fiche publication ».
- **Les fiches** restent rangées. Claude en sort une quand le sommaire le lui dit.
- **Les archives** : un projet terminé part au grenier. Le sommaire garde une ligne pour savoir
  qu'il existe.
- **Le bac « à classer »** : une décision prise aujourd'hui y attend d'être recopiée dans sa fiche,
  avec une date limite.
- **Le bureau** : un bac à brouillons qui se vide tout seul au bout du délai que tu choisis, et un
  tiroir pour les documents finis, qui ne se vide jamais.

Si tu fais travailler plusieurs sessions Claude en même temps (une sur le site, une sur le serveur,
par exemple), chacune a en plus **une boîte aux lettres** : un fichier où les autres lui laissent
des messages. Un **tableau** dit qui travaille sur quoi en ce moment, pour que deux sessions ne
corrigent pas la même chose. Et quand elles partagent une branche git, elles publient par **un seul
script, que tu lances** : avant d'envoyer, il prend en compte ce que les autres viennent d'envoyer,
puis inscrit l'envoi dans un journal. Sur ses quatre premiers jours chez nous, du 29 septembre au
2 octobre 2026, il a porté 22 envois, tous inscrits ; deux fois, deux sessions ont envoyé à moins
d'une minute d'écart, et les deux envois sont passés. Et si elles tournent sur plusieurs machines, le
binder dit comment les relier par le pont de Claude Code, et comment être sûr d'écrire à la bonne.

## Ce que ça t'apporte

- Claude reprend là où il s'était arrêté, sans que tu réexpliques.
- Une règle que tu lui as donnée une fois reste donnée.
- Quand une info change, elle change à un seul endroit.
- Plus de fichiers semés dans tes projets : les brouillons ont leur bac, les documents finis leur
  tiroir.
- Tu peux lire toi-même ce qu'il sait : ce sont des fichiers texte.
- Ses vérifications sont éprouvées : un contrôle ne compte qu'une fois qu'on l'a vu échouer, alors
  le script de vérification plante exprès des défauts et vérifie qu'il signale chacun.

## Ce que ça ne fait pas

- **Pas d'économie de tokens au premier envoi.** La mesure est juste en dessous.
- **Une mémoire garde le faux aussi bien que le vrai.** La méthode prévoit des vérifications, elle ne
  rend pas Claude infaillible.
- **Il faut un peu d'entretien.** Chez nous, c'est une relecture toutes les deux semaines.

## Ce que ça coûte, mesuré

Mesuré le 26 septembre 2026, sur une mémoire d'environ 200 fiches, avec et sans mémoire : 15 questions
posées trois fois chacune, puis trois tâches réelles corrigées jusqu'à une réponse complète.

- **Elle coûte** environ 8 000 tokens de plus à chaque fois que Claude réfléchit ou utilise un outil,
  parce que le sommaire est relu à chaque fois. Ces tokens viennent surtout du cache, donc coûtent
  peu.
- **Elle rapporte** les réponses qui n'existent que dans la mémoire : 15 justes sur 15 avec, 2 sur 14
  sans. Sans elle, Claude cherche partout sans trouver, et chaque bonne réponse lui coûte plus de dix
  fois plus cher (1,24 $ contre 0,10 $, estimation au prix public).
- **Elle réduit les corrections** : sur les trois tâches, il en a fallu 9 avec la mémoire contre 15
  sans. Selon la tâche, les tokens nécessaires pour arriver à une réponse complète vont de 41 % de
  moins à 36 % de plus.
- **Elle ne raccourcit pas** la recherche quand la réponse se trouve ailleurs dans tes fichiers.

En clair : la mémoire ne réduit pas les tokens d'une première réponse, elle en ajoute à chaque appel.
Ce qu'elle réduit, c'est le nombre de fois où tu dois corriger. Prochaine mesure : avec un
traducteur, une étape qui transforme ta demande en consignes claires dès le départ, comparer avec et
sans mémoire. Elle dira si une mémoire déjà posée consomme moins qu'une consigne transmise à chaque
fois.

Pour voir ce que ta propre mémoire coûte, tape `/context` au début d'une session. Le protocole
complet est dans [`MEASURE.fr.md`](MEASURE.fr.md), pour refaire la mesure chez toi.

## Installer

1. Télécharge le fichier [`POUR-TON-AGENT.md`](POUR-TON-AGENT.md) dans ton projet.
2. Ouvre Claude Code dans ce projet et dis-lui : « Lis `POUR-TON-AGENT.md` et mets ce système en
   place chez moi. »
3. Il te pose cinq questions, puis te montre ce qu'il va créer avant de le faire.

## Ce qui reste ta décision

- **Supprimer une fiche.** Claude te la montre d'abord, et c'est toi qui dis oui.
- **Publier quoi que ce soit.** Quand des sessions partagent une branche git, c'est toi qui lances
  le script d'envoi, et un hook empêche Claude d'envoyer.
- **Ton dossier de mémoire ne va jamais sur un dépôt public.** Il contient ce que Claude sait de toi,
  de tes projets et de tes machines.

## Questions

**Faut-il Obsidian ?** Non. Tout est en fichiers texte (Markdown). Obsidian est pratique pour relire
les archives, rien de plus.

**Ça marche avec un autre agent que Claude Code ?** L'idée, oui : ce sont des fichiers et des
règles. Les chemins et le rappel automatique au démarrage sont propres à Claude Code, il faudra les
adapter.

**Ça coûte combien ?** Le sommaire est relu à chaque session, donc il coûte à chaque fois : c'est
pour ça qu'il reste court. Une fiche ne coûte que quand elle est lue.

**D'où ça vient ?** D'un usage quotidien : environ 200 fiches, et neuf sessions Claude qui
travaillent en parallèle sur les mêmes projets. Chaque règle vient d'un problème rencontré.

## Licence

[CC-BY 4.0](LICENSE) : tu peux reprendre ce texte, le modifier et le partager, en citant la source.
