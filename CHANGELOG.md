# Journal des versions

🇫🇷 Français · [🇬🇧 English](CHANGELOG.en.md)

Une section par version, la plus récente en premier. Les entrées récentes sont
détaillées et prêtes à être collées dans une *release* GitHub ; les anciennes
sont résumées en une ligne dans le [tableau final](#versions-antérieures).

Règle de numérotation (`MAJEUR.MINEUR.CORRECTIF`) : voir la section
[Version du README](README.md#version).

**Compatibilité des sauvegardes** — le chargement fait
`Object.assign(partie_vierge, sauvegarde)`. Ajouter un champ est donc toujours
transparent, dans les deux sens, y compris pour l'export/import. Aucune version
publiée n'a jamais renommé ni supprimé de champ : **toutes les sauvegardes 2.x
restent valides**.

---

## 2.22.0 — Les améliorations acquises sont rangées par catégorie

- 🗂️ La liste **Acquises** de l'onglet Améliorations était une longue file
  plate, dans l'ordre de la définition interne : au bout de 30 ou 40 achats on
  ne retrouvait plus rien. Elle est maintenant découpée en sous-sections :
  - **un en-tête par structure** pour les paliers (🛸 Drone, ⛏️ Foreuse… dans
    l'ordre de l'onglet Extraction) ;
  - puis 🔨 **Puissance de clic**, 📡 **Résonance du clic**, 🔗 **Production
    globale** et 🔮 **Anomalies** pour les autres familles.
- 🔢 Chaque en-tête porte un compteur `acquis/total` (`4/6`, `2/4`…) qui passe
  en **doré** quand la famille est complète, comme dans l'onglet Succès.
- 🙈 Une catégorie n'apparaît **qu'à partir de la première amélioration acquise
  dedans**. Sortir « 0/6 » pour la Sphère de Dyson dès la première partie
  révélerait son existence bien avant l'heure ; ici le compteur ne dit que
  *combien* il reste dans une famille déjà entamée, jamais lesquelles ni ce
  qu'elles font.
- La liste **Disponibles** est inchangée : elle reste triée par prix croissant,
  c'est ce qu'on regarde pour acheter.

Aucun changement d'économie, d'équilibrage ni de format de sauvegarde.

---

## 2.21.10 — Les flèches de défilement suivent le pointeur, pas la largeur

- ◀▶ Les flèches de la barre d'onglets (2.21.9) étaient limitées à la mise en
  page desktop (largeur ≥ 881px). Une fenêtre étroite sur ordinateur — deux
  jeux côte à côte, écran partagé — passe par la mise en page mobile tout en
  restant pilotée à la souris ou au trackpad, exactement le cas où elles
  servent le plus. Elles dépendent maintenant du **pointeur disponible**
  (`pointer:fine`, souris/trackpad) plutôt que de la largeur : actives dans
  les deux mises en page tant qu'il n'y a pas d'écran tactile, masquées dès
  que le pointeur est tactile.

---

## 2.21.9 — Flèches de défilement et espace mobile corrigé

- ◀▶ **Flèches de défilement sur la barre d'onglets** (souris/trackpad,
  desktop uniquement — au doigt le glissement suffit). Elles n'apparaissent
  que du côté où il reste effectivement des onglets cachés, et disparaissent
  automatiquement une fois arrivé au bout.
- 🐛 **Faille corrigée** : sur mobile, `#hero` (le bandeau planète) se calait
  à une hauteur d'en-tête figée à 53px. Un en-tête réellement un peu plus
  haut — rendu de police différent selon navigateur/OS — laissait un espace
  visible entre le bandeau du haut et la planète. La hauteur réelle de
  `<header>` est maintenant mesurée en JS et suit tout changement (rotation,
  changement de langue, etc.), plus aucune valeur figée.

---

## 2.21.8 — La vraie cause des sauts de la barre d'onglets

- 🐛 **Faille corrigée, la bonne cette fois** : `<nav>` n'avait pas de
  `position` propre, alors que son parent `<main>` est `position:relative`
  (grille hero/panneau). Sans ça, `offsetLeft` d'un onglet se mesurait par
  rapport à `<main>` — donc décalé de la largeur de la colonne planète
  (352px) — alors que le calcul de défilement le comparait à
  `nav.scrollLeft`, qui démarre à 0 au bord de `<nav>` lui-même. La cible
  était donc systématiquement bien trop grande, et se faisait quasiment
  toujours ramener au maximum scrollable par la limite de sécurité : cliquer
  sur n'importe quel onglet, même Améliorations juste à côté d'Extraction,
  envoyait la barre tout au bout et cachait Extraction. C'était la vraie
  cause des deux tentatives précédentes (2.21.6, 2.21.7), qui corrigeaient
  des symptômes sans toucher à cette racine commune.
- `position:relative` posé sur `<nav>` : ses onglets se mesurent maintenant
  dans le même repère que son propre défilement.
- Au passage, `centrerOnglet()` fait désormais l'ajustement **minimal**
  nécessaire (coller le bord caché à la vue) plutôt qu'un recentrage complet,
  pour ne jamais déplacer la barre plus que nécessaire.

---

## 2.21.6 — Cliquer sur un onglet visible ne recentre plus la barre

- 🐛 **Faille corrigée** : `centrerOnglet()` recentrait l'onglet cliqué à
  chaque clic, même s'il était déjà entièrement visible. Sur une fenêtre
  étroite (deux jeux côte à côte, écran partagé), cliquer sur Améliorations
  (2ᵉ onglet) recentrait quand même la vue et repoussait Extraction hors
  champ, sans qu'il y ait rien eu à faire défiler au départ.
- La barre ne défile désormais que si l'onglet cliqué est réellement caché,
  en tout ou en partie — un onglet déjà visible reste où il est.

---

## 2.21.5 — La barre d'onglets ne défile plus verticalement (Mac / trackpad)

- 🐛 **Faille corrigée** : `<nav>` ne fixait que `overflow-x:auto`, sans
  préciser `overflow-y`. Un axe ne peut pas rester "visible" quand l'autre
  défile : le navigateur calcule alors `overflow-y:auto` tout seul. La barre
  déborde par ailleurs réellement de 1px en hauteur (le `top:1px` des
  onglets), assez pour la rendre scrollable verticalement — au clic-molette
  ou au trackpad sur ordinateur, un cas que `touch-action:pan-x` (2.15.1,
  2.21.3) ne couvrait pas puisqu'il ne s'applique qu'au tactile.
- `overflow-y:hidden` est maintenant posé explicitement sur `<nav>`.

---

## 2.21.4 — Liens de jeu en ligne

- 🔗 **README** (FR et EN) : ajout des deux adresses où jouer en ligne,
  [orbital-colony.mephissto.fr](https://orbital-colony.mephissto.fr/) et
  [mephissto.github.io/orbital-colony](https://mephissto.github.io/orbital-colony/).

Aucun changement dans le jeu.

---

## 2.21.3 — La barre d'onglets ne bouge plus verticalement (pour de bon)

- 🐛 **Faille corrigée** : `touch-action:pan-x` était bien posé sur `<nav>`
  depuis la 2.15.1, mais chaque onglet recevait individuellement, en style
  inline, `touch-action:manipulation` — posé par la fonction générique
  utilisée pour tous les éléments cliquables du jeu. Comme un onglet occupe
  presque toute la largeur de la barre, c'est presque toujours lui que le
  doigt touche, pas l'espace autour : son propre réglage l'emportait, et un
  glissement un peu diagonal sur un onglet pouvait encore faire défiler la
  page verticalement.
- Chaque onglet reçoit maintenant `pan-x` comme sa barre, pour de bon.

---

## 2.21.2 — Le filon et le bond ignorent la Surtension en cours

- 🐛 **Faille corrigée** : le filon riche et le bond temporel calculaient leur
  gain avec `production/s × durée`, mais la production/s utilisée incluait une
  Surtension en cours. Une Surtension ×10 attrapée juste avant multipliait par
  10 le gain du filon ou du bond suivant — jusqu'à donner en une fois plusieurs
  fois le gain prévu, en contradiction avec la règle du bond temporel (« jamais
  un pouvoir que tu n'as pas déjà, seulement du temps d'avance »).
- Les deux calculent désormais leur gain sur la production **de base**, hors
  bonus temporaire. Aucun autre système touché : l'affichage de la production,
  le clic et le bonus hors-ligne continuent d'inclure les bonus actifs, comme
  prévu.

---

## 2.21.1 — Documentation bilingue

- 🇬🇧 **README traduit en anglais** ([`README.en.md`](README.en.md)), avec un
  sélecteur de langue en tête des deux fichiers.
- 📄 **Journal des versions séparé** ([`CHANGELOG.md`](CHANGELOG.md) et
  [`CHANGELOG.en.md`](CHANGELOG.en.md)) : une section par version, prête à être
  collée dans une *release* GitHub. L'historique quitte le README, qui y renvoie.

Aucun changement dans le jeu.

---

## 2.21.0 — Équilibrage de l'antimatière et correction de quatre failles

### Failles corrigées

Mesurées sur une partie complète (20 000 antimatière, tout au maximum) :

- 🔁 **Les bonus d'anomalie se cumulaient.** Quatre surtensions ×10 attrapées
  coup sur coup donnaient **×10 000 sur la production**, et un bonus de clic
  par-dessus portait le tout à ×490 000. Un seul bonus est désormais actif à la
  fois, production et clic confondus : un nouveau remplace le précédent.
- 🛰️ **Le bonus de clic amplifiait les satellites.** Un clic valant 0,4 fois la
  production, un Écho quantique ×12 sur dix clics automatiques par seconde valait
  **×49 sur la production totale**, sans rien faire. Il ne s'applique plus qu'aux
  clics du joueur — à la main, à 5 clics/s, il rapporte encore l'équivalent de
  24 fois la production.
- 📦 **La Capsule offrait de l'antimatière gratuite.** Son minerai était compté
  comme *extrait* : au niveau 6, chaque cycle démarrait avec **5 antimatière
  acquises avant d'avoir joué une seconde**.
- ♻️ **Le seuil de relance automatique ne suivait pas la progression.** Réglé à
  50 puis oublié, il déclenchait un cycle par image une fois la réserve à
  100 000 — mesuré à **600 cycles et +75 400 antimatière en une minute**. Un
  plancher à 10 % de la réserve s'applique maintenant, et le panneau affiche le
  seuil réellement utilisé.

Le pire cas passif passe de **×490 000 à ×5**.

### Divers

Le succès « Résonance parfaite » demandait deux bonus simultanés, devenu
impossible : il demande maintenant d'attraper un bonus alors qu'un autre est
encore actif.

---

## 2.20.0 — La courbe s'allonge, l'automatisation suit

- ⚛️ **Exposant du gain d'antimatière abaissé à 0,32**, et seuil de la première
  unité ramené de 20 à **10 milliards** de minerai : c'était le haut de la courbe
  qu'il fallait étirer, pas le début de partie.
- 🤖 **Prix de l'automatisation divisés par 3,3**, pour suivre le nouveau revenu.
  Tout automatiser coûte 31 540 antimatière au lieu de 104 650.

| Antimatière | 2.18 | 2.19 | **2.20** |
|---|---|---|---|
| 1 000 | 18 s | 4,7 min | **9 min** |
| 20 000 | 20 s | 9,8 min | **47 min** |
| 100 000 | 22 s | 27 min | **1,5 h** |
| 1 000 000 | 26 s | 1,8 h | **18,8 h** |

---

## 2.19.0 — Le gain d'antimatière change de formule

```
gain = ⌊ ( minerai du cycle ÷ 2e10 ) ^ 0,35 ⌋      au lieu de   12 × √( minerai ÷ 1e12 )
```

Mesuré en simulation, un cycle rapportant +50 % durait **une vingtaine de
secondes à toute échelle** — de 100 à 1 000 000 d'antimatière. L'antimatière
était de fait gratuite, et aucun prix de recherche n'y pouvait rien.

La cause n'était pas le seuil mais l'exposant : la longueur d'un cycle est fixée
par le **rachat des structures**, pas par le seuil d'antimatière. Multiplier le
seuil par 16 ne faisait passer un cycle que de 21 à 40 secondes.

---

## 2.18.0 — Barème des recherches revu

Croissance d'au moins **×1,8** et bases relevées, pour que chaque niveau coûte
visiblement plus que le précédent **dès le premier**. L'ancien barème partait de
4 antimatière avec une croissance de ×1,55, ce qui donnait 4 → 7 → 10 : la
progression était bien là, mais invisible à l'œil sur d'aussi petits nombres.

Tout terminer coûte **234 890 antimatière** au lieu de 106 434.

---

## 2.17.4 — Automatisation, succès et refonte de l'interface

Version publiée, cumulant tout depuis la 2.0.0.

### Nouveau

- 🛰️ **Onglet Automatisation** — cinq automates payés en antimatière, conservés
  d'un cycle à l'autre, coupables à volonté : Satellites d'extraction
  (10 niveaux), Contremaître, Ingénieur, Sonde de récupération, Cycle
  automatique.
- 🏆 **71 succès** au lieu de 44, rangés en huit catégories avec leur
  progression. Dont un succès par type d'anomalie, avec des seuils calés sur
  leurs probabilités.

### Équilibrage

- ⚛️ **Le bonus d'antimatière n'est plus linéaire** : `(1 + am × bonus)^1.5`.
  À 1 000 antimatière, ×2 236 au lieu de ×171.
- 🎲 **Anomalies aléatoires** : chaque anomalie tire sa valeur à chaque
  apparition, et affiche le montant obtenu.

### Interface

- **Barre d'onglets** refaite : une icône par onglet, libellés complets partout,
  défilement horizontal.
- **Statistiques** en tuiles groupées par thème, avec le détail des anomalies
  par type.
- **Une couleur par unité** : minerai doré, antimatière violette, production
  cyan, multiplicateur vert.
- **Niveau possédé** en bas à droite des cartes Recherche et Automatisation.
- **Satellites en orbite** autour de la planète, un par niveau de clic
  automatique.

### Corrections

- Zoom au double-appui sur mobile, verrouillé pour de bon dans l'application
  installée.
- L'en-tête mobile n'est plus une zone défilante ; la barre d'onglets ne bouge
  plus verticalement.

### Projet

Licence **GPL 3.0 ou ultérieure**.

---

## Versions antérieures

| Version | Contenu |
|---|---|
| 2.17.3 | le clic automatique devient les **Satellites d'extraction** (🛰️), avec les deux succès correspondants renommés |
| 2.17.2 | derniers multiplicateurs passés au vert : bonus des succès, bonus du panneau de cycle, pastilles de bonus temporaire |
| 2.17.1 | satellites à vitesse fixe, avec pulsation, et orbite recalibrée pour ne plus déborder sur les éléments voisins |
| 2.17.0 | l'onde est remplacée par des satellites en orbite, un par niveau du clic automatique |
| 2.16.0 | onde cyan sur la planète et point clignotant sur la carte, à la cadence du clic automatique |
| 2.15.2 | les onglets inactifs redeviennent visibles, en sourdine, et l'onglet actif gagne un liseré cyan |
| 2.15.1 | la barre d'onglets ne bouge plus verticalement au toucher : geste limité à l'horizontale et recentrage sans `scrollIntoView` |
| 2.15.0 | niveau possédé en bas à droite des cartes Recherche et Automatisation ; une couleur par unité dans tout le jeu |
| 2.14.0 | barre d'onglets refaite : une icône par onglet, libellés complets partout et défilement horizontal avec dégradés de bord |
| 2.13.2 | zoom au double-appui : trois barrières au lieu d'une ; l'en-tête mobile n'est plus une zone défilante |
| 2.13.1 | les automates à palier unique affichent « Prix » au lieu de « Prix du niveau 1 » |
| 2.13.0 | le clic automatique démarre à 100 antimatière au lieu de 30 (toujours ×2 par niveau) |
| 2.12.1 | le projet passe sous licence GPL 3.0 ou ultérieure : fichier `LICENSE`, en-têtes, tuile « Licence » |
| 2.12.0 | écran des statistiques refait en tuiles groupées par thème ; succès « Réflexe éclair » (71 au total) |
| 2.11.0 | 5 succès de plus (70 au total) : 100 000 et 1 000 000 de clics, puissance de clic jusqu'à 1 Sx, et 1 000 anomalies |
| 2.10.0 | 13 succès de plus (65 au total) : quatre paliers de clic et neuf sur les anomalies, dont un par type |
| 2.9.0 | les succès sont rangés en huit catégories, et huit succès d'automatisation s'ajoutent (52 au total) |
| 2.8.0 | les réglages d'automatisation deviennent deux cadres autonomes, et le plafond de dépense passe en menu déroulant |
| 2.7.0 | plafond de dépense par paliers de 10 % ; seuil de relance du cycle saisi à la main |
| 2.6.0 | les deux réglages d'automatisation passent de pourcentages à trois modes nommés |
| 2.5.0 | clic automatique jusqu'au niveau 10 ; Contremaître à 300 et Ingénieur à 450 antimatière |
| 2.4.0 | Contremaître, Sonde et Cycle automatique passent à un palier unique |
| 2.3.0 | onglet **Automatisation** : cinq automates achetés en antimatière et coupables à volonté |
| 2.2.0 | le bonus d'antimatière n'est plus linéaire : le total est élevé à la puissance 1,5 (`AM_EXP`) |
| 2.1.0 | toutes les anomalies tirent leur valeur au hasard ; le badge et le message affichent le montant obtenu |
| 2.0.0 | version publique consolidée : PWA installable, bilingue FR/EN, en-tête mobile fixe, 44 succès |
| 1.0.0 | première numérotation, introduite en même temps que l'affichage de version |
