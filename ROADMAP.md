# Feuille de route

Version **3.0.28**. Ce document sert de mémoire entre
deux sessions : ce qui est décidé, ce qui reste à faire, et pourquoi.

---

## Où on en est

Le plan d'ensemble pour allonger la durée de vie du jeu comportait quatre
étapes. Les deux premières sont faites.

| | Étape | État |
|---|---|---|
| 0 | Corriger la linéarité du bonus d'antimatière | ✅ v2.2.0 |
| 1 | Automatisation | ✅ v2.3.0 → 2.8.0 |
| 2 | **Défis** | ✅ v3.0.28 |
| 3 | **Effondrement + astres** | ⬜ à faire — étape suivante |

Entre-temps : succès rangés en catégories et portés à 71 (v2.9 → 2.11),
statistiques refaites en tuiles (v2.12.0), licence GPL 3.0 (v2.12.1).

Le diagnostic de départ reste la référence : maxer les 8 recherches coûte
**234 890 antimatière** (106 434 avant le rebarèmage de la 2.18.0), après quoi
l'antimatière n'avait plus aucun débouché. La 2.19.0 a par ailleurs corrigé la
cause profonde : le gain d'antimatière était tel qu'un cycle ne durait qu'une
vingtaine de secondes à toute échelle, ce qui vidait n'importe quel barème en
quelques minutes.
L'automatisation en a ouvert un premier (31 540, dont 30 690 pour les dix
niveaux des Satellites d'extraction). Les défis et l'effondrement
doivent ouvrir la suite.

---

## Étape 2 — Les défis · ✅ livrée en 3.0.28

**Validé tel quel.** Y compris le point qui avait été corrigé après réflexion :
**entrer dans un défi encaisse d'abord le cycle en cours** et crédite
l'antimatière en attente, *puis* la remise à zéro a lieu. Sans ça le joueur
attend toujours « le bon moment » et lancer un défi devient une corvée à
planifier.

### La boucle

- Nouvel onglet **Défis**, visible dès le premier cycle mais **grisé**, avec le
  décompte des cycles restants ; il s'ouvre quand la tuile de l'en-tête affiche
  **« cycle n°6 en cours »**, soit après 5 cycles bouclés. `CHAL_CYCLES` est ce
  numéro affiché, pas un nombre de cycles terminés — le code compte ce que le
  joueur lit.
- Six défis, chacun avec son état : verrouillé / disponible / en cours / réussi.
- Entrer → encaissement du cycle → remise à zéro (minerai, structures,
  améliorations) → la règle cassée s'applique.
- **Conservé pendant le défi** : antimatière, recherches, succès, automates,
  récompenses des défis déjà réussis.
- Bandeau permanent en haut : défi actif, progression, bouton **Quitter**
  (nouvelle remise à zéro, aucune pénalité).
- Objectif toujours exprimé en **minerai extrait sur le cycle** — le compteur
  `runOre` existe déjà, donc barre de progression gratuite.
- Réussi une fois = validé définitivement, récompense permanente et active en
  dehors du défi. **Pas de répétition** : ce serait du farm, pas du contenu.

### Les six défis

| Défi | Règle cassée | Récompense permanente | coef |
|---|---|---|---|
| 📵 Silence radio | aucune anomalie n'apparaît | anomalies +20 % plus fréquentes | 0,005 |
| ⛓️ Mains liées | le clic ne rapporte rien | production +25 % | 0,002 |
| 📈 Inflation | prix en ×1,35 au lieu de ×1,15 | −8 % sur tous les prix | 0,006 |
| 🏚️ Colonie naine | seules les 6 premières structures existent, mais ×4 | les 4 dernières produisent ×2 | 0,0015 |
| 💨 Fuite de confinement | production −2 % par tranche de 2 min, plancher 20 % | gain d'antimatière +15 % | 0,015 |
| 🕳️ Le Vide | l'antimatière ne compte plus dans le multiplicateur | exposant antimatière 1,50 → 1,55 | 0,005 |

### Équilibrage mesuré (3.0.28)

Simulation seconde par seconde sur une vraie sauvegarde (cycle n°6, 34
antimatière, meilleur cycle 285 Qa), anomalies, buffs et automates compris,
chaque défi comparé à un cycle ordinaire visant le même objectif :

| Défi | Attentif | × cycle normal | Absent |
|---|---|---|---|
| 📵 Silence radio | 3,0 h | 3,2× | 9,1 h |
| ⛓️ Mains liées | 2,4 h | 3,9× | 2,4 h (1,2×) |
| 📈 Inflation | 2,5 h | 2,5× | 7,2 h |
| 🏚️ Colonie naine | 2,7 h | 5,0× | 7,8 h |
| 💨 Fuite de confinement | 3,8 h | 2,7× | 19,1 h |
| 🕳️ Le Vide | 2,8 h | 5,2× | 7,3 h |

**Deux défis étaient impossibles avant cette mesure**, pas difficiles :

- **Fuite** — sans plancher, la décroissance composée bornait le minerai total à
  `production × 1485 s`. Plafond mesuré : 5,58 T en 36 h, soit 0,000002 % du
  record du joueur. Aucun objectif n'était atteignable.
- **Colonie naine** — avec 4 structures, le coût monte en 1,15ⁿ quand la
  production monte linéairement : plafond à 124 T, 0,00004 % du record. Et son
  handicap annonçait 12× de ralentissement là où le réel dépassait 100×, parce
  qu'il mesurait une part de production en supposant qu'on garde sa colonie —
  alors qu'on la reconstruit.

**Ce que le coefficient règle vraiment.** Pour les règles à facteur constant
(silence, mains, inflation, vide) le rapport de difficulté ne dépend pas du
coefficient : celui-ci ne règle que la durée. Pour les règles qui composent dans
le temps (fuite, naine), le rapport explose avec l'objectif — elles ne se règlent
donc pas au coefficient mais en bornant la règle elle-même.

Un coefficient unique reste légitime parce que le minerai croît
exponentiellement : atteindre 0,5 % du record prend la même *part* du cycle pour
un débutant et pour une colonie avancée.

### Notes d'implémentation

- Chaque contrainte est **un seul interrupteur inversé** dans le code existant :
  `globalMult`, `clickVal`, `genCost`, `anomInterval`, `amGain`. Aucune économie
  nouvelle à équilibrer.
- État : `S.chal` (id du défi actif ou `null`) et `S.chalDone` ({id:1}).
  Champs additifs → sauvegardes antérieures compatibles sans conversion.
- Objectifs **relatifs au meilleur cycle** (`coef × bestRun × handicap`), figés
  à l'entrée. L'idée initiale de valeurs fixes a été abandonnée en cours de
  route : mesuré, deux joueurs à 10 et 24 cycles mettaient 78 min et 1 min pour
  le même minerai.
- **Reste à faire (3.1)** : des succès de la catégorie Défis, et une tuile de
  statistiques « Défis réussis ». Volontairement laissés de côté pour la 3.0.28 —
  ajouter du contenu non testé le jour de la sortie est la meilleure façon de
  livrer un bug.
- **À revoir après du jeu réel** : les six coefficients sont calibrés sur une
  seule sauvegarde, avec un joueur simulé qui clique 4 fois par seconde sans
  interruption. Les vrais temps tomberont entre les colonnes « attentif » et
  « absent » du tableau ci-dessus.

---

## Étape 3 — L'effondrement et les astres

**Validé.** Deuxième couche de prestige, débloquée à **5 000 antimatière
produites au total**.

### L'effondrement

| Remis à zéro | Conservé |
|---|---|
| minerai, structures, améliorations, **antimatière, recherches** | succès, défis réussis, **automates**, Éclats |

Les automates sont conservés délibérément : perdre ses satellites à chaque
effondrement serait une punition, pas un défi, et découragerait d'effondrer.
Les recherches, elles, repartent à zéro — le puits de 234 890 antimatière se
rouvre.

### Les Éclats

```
éclats = ⌊ 3 × ( antimatière totale du cycle ÷ 1000 ) ^ 0,6 ⌋
```

| Antimatière accumulée | Éclats |
|---|---|
| 5 000 | 7 |
| 20 000 | 18 |
| 100 000 | 47 |
| 1 000 000 | 189 |

Même logique de racine que l'antimatière : mieux vaut effondrer souvent que
rester deux fois plus longtemps.

### L'arbre des Éclats

Le point capital : **aucun niveau maximum**, contrairement aux recherches
plafonnées à 5–15. Prix en `base × croissance^n` : ça explose, mais rien ne se
ferme jamais. C'est ce qui rend la partie sans fin.

| Branche | Effet par niveau |
|---|---|
| 💠 Densité | +8 % de gain d'antimatière |
| 🌀 Résonance profonde | +0,01 sur l'exposant antimatière (`AM_EXP`) |
| ⚡ Amorçage | démarre chaque cycle avec N niveaux de recherche déjà payés |
| 🧲 Rémanence | conserve X % de l'antimatière à travers l'effondrement |
| 🔭 Cartographie | débloque un astre par niveau |

La Résonance profonde attaque l'exposant, donc multiplie tout le reste : c'est
de très loin la branche la plus forte, à tarifer en conséquence. C'est
l'objectif lointain qui justifie de continuer.

### Les astres

Débloqués un par un par Cartographie. On **choisit son astre au début de chaque
cycle** — pas de chaque effondrement, pour que le choix revienne souvent.

| Astre | Règle changée |
|---|---|
| 🌍 Terre-mère | rien, la référence |
| 🧊 Lune glacée | production ×3, structures ×5 plus chères |
| 🌋 Monde volcanique | buffs ×3 plus longs et cumulables, aucune production hors-ligne |
| 🪐 Géante gazeuse | clic = 0, production passive ×2, hors-ligne à 100 % |
| ☄️ Astéroïde errant | production oscillant de ±80 % sur 30 s, anomalies ×3 |

Chaque astre a une piste de maîtrise : y atteindre un palier donne un bonus
global permanent, ce qui pousse à tous les visiter au lieu de camper sur le
meilleur.

**Pourquoi un seul astre à la fois** — deux planètes en parallèle, ce sont les
mêmes gestes en double : deux fois plus de taps, zéro décision nouvelle, et deux
cibles de clic à l'écran (refusé). Une seule planète dont les règles changent,
c'est une vraie décision à chaque cycle et l'interface reste la même.

### Points de vigilance

- Un joueur qui vient de dépenser 90 000 en Résonance détestera tout perdre. La
  branche Amorçage existe pour ça, et le seuil de 5 000 est placé **bien avant**
  que les recherches soient finies, pour que le premier effondrement arrive
  alors qu'il n'y a pas encore grand-chose à perdre.
- Un défi et un astre non standard peuvent se contredire (« Mains liées » sur la
  Géante gazeuse, où le clic vaut déjà zéro). En première version, **interdire
  de combiner un défi avec autre chose que la Terre-mère**.

---

## Règles de travail à conserver

- **Sauvegardes** — le chargement fait `Object.assign(partie_vierge, sauvegarde)`.
  *Ajouter* un champ est donc toujours transparent, dans les deux sens y compris
  pour l'export/import. Seuls un **renommage** ou une **suppression** casseraient
  quelque chose : dans ce cas, prévenir et écrire une conversion.
- **Version** — `MAJEUR.MINEUR.CORRECTIF`, un seul niveau par livraison, et
  annoncer lequel a été incrémenté. Voir la section Version du README.
- **README** — tenir les chiffres à jour ; ils sont vérifiés automatiquement
  contre le code. **Deux versions à maintenir** : `README.md` (français) et
  `README.en.md` (anglais).
- **CHANGELOG** — chaque nouvelle version ajoute sa section en tête de
  `CHANGELOG.md` **et** de `CHANGELOG.en.md`, prête à être collée dans une
  *release* GitHub. Les entrées récentes sont détaillées ; les anciennes
  redescendent dans le tableau de fin.
- **Licence** — GPL 3.0 ou ultérieure. Remplacer `Guilhem` par le nom complet
  dans `index.html`, `sw.js` et le README si souhaité.

## Petits points en suspens

- Rien n'annonce l'apparition de l'onglet **Auto** au premier cycle : proposition
  d'ajouter un message façon toast de succès. En attente de décision.
- Catégories de succès **Extraction** (8) et **Production** (5) : ce sont de purs
  paliers de nombres, la partie la plus mécanique de la liste. À retravailler
  éventuellement en même temps que les défis.

## Décision actée — sécurisation de l'export/import

Discuté (v2.21.1, sans code) : l'export est aujourd'hui `btoa(JSON.stringify(S))`,
sans vérification. Un salt ou une clé codés en dur dans `index.html` ne
protègent de rien : le jeu est un fichier unique, GPL, sans serveur — le
"secret" est visible en clair par n'importe qui via *Afficher le source*, ce
qui est même l'esprit de la licence. Un checksum protégerait contre la
corruption accidentelle (copier-coller tronqué, edit à la main qui casse un
champ), mais pas contre une triche volontaire — et dans un jeu solo sans
classement, éditer sa propre sauvegarde ne lèse que soi-même.

**Décision : on n'y touche pas.** Le rapport effort/bénéfice ne le justifie
pas. À reconsidérer seulement si un jour le jeu gagne une dimension partagée
(classement, comparaison entre joueurs) qui rendrait la triche opposable à
quelqu'un d'autre.

## Décision actée — prix des paliers (améliorations « Palier N »)

Discuté (v2.21.10, sans code) : sur le Drone, le prix d'un palier
(`base×18×11^i`) est très en-dessous de l'investissement cumulé en structure
nécessaire pour l'atteindre — écart minime aux paliers 1-3 (need 10/25/50,
ratio cumul/palier ~1-3×) mais qui explose aux paliers 5-6 (need 175/250,
ratio 1,06M puis 3,44Md à ×11).

Deux pistes testées, aucune ne convient :
- **Augmenter le facteur de croissance ×11** (essayé jusqu'à ×30) : ne peut
  pas suivre, la structure grandit en exposant du nombre possédé (jusqu'à
  250) alors que le palier grandit en exposant d'un simple indice 0-5. Même
  à ×30 le ratio reste à 19 200 puis 22,8M — et ça rend déjà les paliers 2-3
  relativement trop chers (ratio < 1).
- **Indexer le prix sur l'investissement englouti** (fraction du cumul) :
  corrige le ratio partout, mais rend les derniers paliers d'une structure
  faible (Drone) coûteux en dizaines de milliards pour un simple ×5/×6 sur
  une production qui ne pèse plus grand-chose à ce stade — exactement le
  défaut pointé par le joueur.

**Décision : on n'y touche pas.** À 175-250 Drones possédés, la production
du Drone est de toute façon négligeable face aux structures plus tardives ;
l'écart de prix ne fausse aucune progression, c'est une curiosité sans
conséquence plutôt qu'un vrai problème d'équilibrage.
