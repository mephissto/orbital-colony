# Colonie Orbitale

Idle game spatial, bilingue FR / EN, installable en application (PWA) et jouable
hors connexion. Tout le jeu tient dans `index.html` : aucune dépendance, aucun
serveur, aucune donnée qui sort de ton navigateur.

Logiciel libre sous [GPL 3.0 ou ultérieure](#licence).

---

## Sommaire

- [Fichiers et déploiement](#fichiers-et-déploiement)
- [Boucle de jeu](#boucle-de-jeu)
- [Le clic](#le-clic)
- [Les structures](#les-structures)
- [Les améliorations](#les-améliorations)
- [Les anomalies](#les-anomalies)
- [Le multiplicateur global](#le-multiplicateur-global)
- [Le prestige et l'antimatière](#le-prestige-et-lantimatière)
- [Les recherches](#les-recherches)
- [L'automatisation](#lautomatisation)
- [Les succès](#les-succès)
- [Les gains hors-ligne](#les-gains-hors-ligne)
- [Sauvegarde](#sauvegarde)
- [Interface](#interface)
- [Version](#version)
- [Modifier l'équilibrage](#modifier-léquilibrage)
- [Licence](#licence)

---

## Fichiers et déploiement

| Fichier | Rôle |
|---|---|
| `index.html` | le jeu entier — logique, styles, favicon, traductions |
| `manifest.webmanifest` | nom, couleurs et icônes de l'application installée |
| `sw.js` | service worker : jeu jouable hors connexion |
| `icon-192.png`, `icon-512.png` | icônes d'application |
| `icon-maskable-512.png` | icône adaptative Android (recadrable en rond) |
| `apple-touch-icon.png` | icône iOS |
| `LICENSE` | texte complet de la GPL 3.0 |
| `README.md` | ce document |
| `ROADMAP.md` | ce qui est prévu pour la suite, et pourquoi |

Tous les fichiers vont **à la racine du dépôt**, à plat. L'installation exige
HTTPS — GitHub Pages et Netlify le fournissent automatiquement.

**Installer :** Chrome Android → menu ⋮ → « Installer l'application » ; Safari
iOS → Partager → « Sur l'écran d'accueil » ; sur ordinateur, l'icône
d'installation apparaît dans la barre d'adresse.

**Mettre à jour :** le service worker cherche toujours `index.html` sur le
réseau en priorité, donc un simple rechargement suffit après un déploiement. Si
tu modifies **les icônes ou le manifeste**, incrémente `CACHE` en haut de
`sw.js` (`colonie-orbitale-v2` → `-v3`) pour forcer le renouvellement du cache.

---

## Boucle de jeu

Tu extrais du **minerai**, la seule ressource courante. Le minerai sert à
acheter des **structures** qui en produisent automatiquement, et des
**améliorations** qui multiplient cette production. Quand la progression
ralentit, tu relances un **cycle** : tu perds tout mais tu gagnes de
l'**antimatière**, qui augmente définitivement ta production et finance des
**recherches** permanentes.

```
   clic ─┐
         ├─→ MINERAI ─→ structures ─→ production /s ─┐
 hors-ligne ┘     └─→ améliorations ────────────────┤
                                                     ├─→ ×  multiplicateur global
   anomalies ─→ bonus temporaires ──────────────────┤
   succès ────────────────────────────────────────  ┤
   antimatière + recherches ─────────────────────────┘
                    ↑
                 prestige (remet le cycle à zéro)
```

---

## Le clic

Cliquer sur la planète rapporte du minerai immédiatement. La valeur d'un clic
est la somme de deux termes, le tout multiplié par le multiplicateur global :

```
clic = ( 1 × améliorations de clic × 2^(Bras servo) × multiplicateur global
         + production/s × résonateur )
       × bonus de clic actif
```

- **Améliorations de clic** — Marteau ionique ×3, Exosquelette ×4,
  Condensateur ×5, Champ magnétique ×8 (cumulatifs, soit ×480 au complet).
- **Bras servo-assistés** (recherche) — ×2 par niveau, jusqu'à ×4096.
- **Résonateurs** — ajoutent un pourcentage de ta production par seconde à
  chaque clic : v1 +3 %, v2 +12 %, v3 +40 %. Seul le meilleur compte, ils ne se
  cumulent pas entre eux. En fin de partie c'est ce terme qui domine largement.

---

## Les structures

Dix structures, chacune produisant du minerai en continu.

| # | Structure | Prix de base | Production de base |
|---|---|---|---|
| 1 | Drone mineur | 15 | 0,1 /s |
| 2 | Foreuse automatique | 120 | 1 /s |
| 3 | Extracteur laser | 1 400 | 8 /s |
| 4 | Raffinerie orbitale | 20 000 | 52 /s |
| 5 | Essaim de nanites | 240 000 | 300 /s |
| 6 | Ascenseur spatial | 3 000 000 | 1 800 /s |
| 7 | Broyeur d'astéroïdes | 45 000 000 | 11 000 /s |
| 8 | Forge stellaire | 800 000 000 | 75 000 /s |
| 9 | Déchireur dimensionnel | 1,5e10 | 540 000 /s |
| 10 | Sphère de Dyson | 3e11 | 4 200 000 /s |

**Prix du n-ième exemplaire :** `prix de base × 1,15^(déjà possédés)`, réduit
par la recherche Négociation. Le bouton **MAX** calcule combien tu peux en
acheter d'un coup, sommes géométriques comprises.

**Production totale :**

```
production/s = Σ ( nombre × production de base × paliers de la structure )
               × multiplicateur global
```

Une structure n'apparaît dans la liste qu'une fois que tu as approché son prix
(35 %), et les deux suivantes s'affichent en « ??? ». Une structure découverte
le reste, même après un prestige.

---

## Les améliorations

Achats **uniques et permanents**, payés en minerai, perdus au prestige. Elles
apparaissent dans la liste dès que tu as extrait 8 % de leur prix au cours de la
partie — critère volontairement basé sur le total extrait, pour que la liste ne
saute pas pendant que tu joues.

**Paliers de structure** — 6 par structure, soit 60 au total. Chacun exige un
nombre d'exemplaires et multiplie la production de cette seule structure :

| Palier | Exemplaires requis | Effet | Prix |
|---|---|---|---|
| 1 | 10 | ×2 | prix de base × 18 |
| 2 | 25 | ×2 | × 11 |
| 3 | 50 | ×3 | × 11 |
| 4 | 100 | ×4 | × 11 |
| 5 | 175 | ×5 | × 11 |
| 6 | 250 | ×6 | × 11 |

Une structure entièrement améliorée produit **×1 440**.

**Améliorations de clic** — Marteau ionique (400), Exosquelette (35 000, dès 50
clics), Condensateur (5e6, dès 250 clics), Champ magnétique (2e9, dès 600 clics).

**Résonateurs** — v1 (2e5), v2 (4e8, exige v1), v3 (6e11, exige v2).

**Améliorations globales** — Réseau logistique ×1,25 (5e4), IA de coordination
×1,5 (8e6), Relais quantique ×2 (1,2e9), Bio-ingénierie ×2,5 (3e11), Moteur à
singularité ×4 (9e13). Au complet : **×37,5**.

**Balise d'anomalie** (1e7) — les anomalies apparaissent 30 % plus souvent.

Total : **73 améliorations**.

---

## Les anomalies

Une anomalie apparaît régulièrement quelque part à l'écran. Elle reste
**14 secondes**, puis disparaît. La cliquer déclenche un effet et relance le
compte à rebours.

**Fréquence :** un intervalle aléatoire entre **110 et 240 secondes**, réduit
par la Balise d'anomalie (×0,7) et par la recherche Détecteur (×0,8 par niveau).
Au maximum des deux : entre 25 et 55 secondes.

**Effets possibles :**

| Anomalie | Chance | Effet |
|---|---|---|
| 🌟 **Bond temporel** | **1 %** | **20 à 30 minutes** de production, d'un coup |
| ⚡ Surtension | 5 % | production **×5 à ×10** pendant 45 s |
| ✨ Écho quantique | 5 % | clic **×6 à ×12** pendant 60 s |
| 💎 Filon riche | 44,5 % | **120 à 300 s** de production, d'un coup |
| 📦 Cache abandonnée | 44,5 % | **+15 à 20 %** de ton minerai en réserve |

**Chaque anomalie tire sa valeur au hasard** dans la fourchette indiquée, à
chaque apparition — le message et le badge affichent le montant exact obtenu
(« Production ×6,4 », « +813K minerai (21 minutes d'avance) »). Les
multiplicateurs sont arrondis au dixième.

Les multiplicateurs sont volontairement rares : ils se cumulent avec tout le
reste et deviennent démesurés en fin de partie, alors que le filon et la cache
restent proportionnés à ta progression. Les durées sont allongées de 30 % par
niveau de Détecteur.

### Le bond temporel

C'est le gros lot : un saut de **20 à 30 minutes en avant**, crédité
instantanément. Soit **quatre à quinze fois** un Filon riche, tout en restant
proportionné à ta progression — il ne donne jamais un pouvoir que tu n'as pas
déjà, seulement du temps d'avance. C'est volontaire : il accélère la partie sans
raccourcir la courbe de progression.

Visuellement, impossible de le rater : plus grand (96 px contre 62), étoile
irisée blanc → cyan → violet, halo bleuté pulsé deux fois plus vite, et surtout
**des arcs qui tournent quatre fois plus vite** que sur une anomalie ordinaire —
l'image du temps qui s'emballe.

**Signal d'approche :** un compte à rebours est affiché au-dessus de la planète.
Sous **10 secondes**, il passe au magenta et l'anneau de la planète en fait
autant. Quand l'anomalie est à l'écran, il affiche « En vue ! ».

---

## Le multiplicateur global

Il multiplie **toute** ta production, clic compris. C'est le produit de quatre
familles :

```
multiplicateur = (1 + antimatière × bonus par unité)   ← antimatière
               × (1 + 0,01 × succès obtenus)           ← succès
               × 1,3^(Optimisation minière)            ← recherche
               × améliorations globales                ← ×1,25 … ×4
               × bonus de production actifs            ← anomalies
```

Le détail complet est disponible en infobulle sur la tuile Multiplicateur.

---

## Le prestige et l'antimatière

Relancer un **cycle** remet à zéro : minerai, structures, améliorations, bonus
en cours. Tu **conserves** : antimatière, recherches, succès, structures déjà
découvertes, statistiques.

**Antimatière gagnée :**

```
gain = ⌊ 12 × √( minerai extrait pendant ce cycle ÷ 1e12 ) ⌋
```

Il faut donc environ **6,94 milliards** de minerai extrait sur le cycle pour la
première unité. Le rendement est en racine carrée : rester dix fois plus
longtemps ne rapporte que ~3,2 fois plus d'antimatière — mieux vaut relancer
régulièrement que s'acharner.

**Bonus permanent :** chaque unité d'antimatière donne **+2 %** de production.
La recherche Résonance ajoute +1,5 point par niveau, soit **+17 % par unité** au
niveau 10 — c'est de très loin le plus gros levier du jeu.

Le total est ensuite **élevé à la puissance 1,5** :

```
multiplicateur = ( 1 + antimatière × bonus ) ^ 1.5
```

Sans cet exposant le multiplicateur montait *linéairement* avec l'antimatière
alors que le prix des structures monte *exponentiellement* (×1,15 par achat) :
chaque cycle rapportait donc mécaniquement moins que le précédent. Avec la
puissance 1,5, le multiplicateur croît assez vite pour compenser le minerai
demandé, et un cycle garde une durée à peu près stable très loin dans la partie.
L'exposant est la constante `AM_EXP`.

| Antimatière | Ancien bonus | Bonus actuel |
|---|---|---|
| 10 | ×2,7 | ×4,4 |
| 100 | ×18 | ×76 |
| 1 000 | ×171 | ×2 236 |
| 10 000 | ×1 701 | ×70 155 |

*(valeurs à Résonance niveau 10)*

---

## Les recherches

Payées en **antimatière**, jamais perdues. Le prix du niveau `n` vaut
`coût de base × croissance^n`.

| Recherche | Effet par niveau | Max | Coût de base | Croissance |
|---|---|---|---|---|
| ⚙️ Optimisation minière | +30 % de production | 15 | 4 | ×1,55 |
| 🤖 Bras servo-assistés | ×2 puissance de clic | 12 | 3 | ×1,5 |
| 💾 Mémoire tampon | +3 h de gains hors-ligne | 8 | 6 | ×1,8 |
| 🔁 Automatisation | +10 % d'efficacité hors-ligne | 6 | 8 | ×2 |
| 💠 Négociation | −4 % sur le coût des structures | 10 | 10 | ×1,9 |
| 📶 Détecteur d'anomalies | anomalies +25 % fréquentes, +30 % longues | 5 | 14 | ×2,2 |
| ✨ Résonance d'antimatière | +1,5 % de bonus par antimatière | 10 | 20 | ×2,4 |
| 📦 Capsule de départ | minerai offert à chaque nouveau cycle | 6 | 15 | ×2,1 |

La Capsule donne `10 000 × 25^niveau` de minerai au début de chaque cycle, soit
2 441 milliards (2,44e12) au niveau 6.

---

## L'automatisation

Onglet **Auto**, révélé au premier cycle de prestige. Comme les recherches, les
automates se paient en **antimatière** et ne sont jamais perdus. Ils font gagner
du confort, pas de la puissance : le Bras automatique ne fait rien qu'un joueur
présent ne puisse faire à la main.

**Chaque automate possède un interrupteur** (à droite de sa carte). Le couper ne
rembourse rien et ne fait pas perdre les niveaux : il suffit de le rallumer.

| Automate | Effet | Max | Coût | Coût cumulé |
|---|---|---|---|---|
| 🖱️ Bras automatique | +1 clic/s par niveau | 10 | 100, ×2 par niveau | 102 300 |
| 🏗️ Contremaître | rachète chaque seconde la structure la moins chère | 1 | 300 | 300 |
| ⬆️ Ingénieur | achète l'amélioration la moins chère payable | 1 | 450 | 450 |
| 📡 Sonde de récupération | ramasse l'anomalie à ta place | 1 | 600 | 600 |
| ♻️ Cycle automatique | relance un cycle au seuil choisi | 1 | 1 000 | 1 000 |

Tout automatiser coûte **104 650 antimatière**, soit presque exactement le prix
des recherches complètes (106 434) : les deux puits sont volontairement du même
ordre de grandeur, pour que chaque achat d'automate soit un vrai renoncement à
un niveau de Résonance.

Le Bras automatique en représente à lui seul **102 300**. Ses quatre derniers
niveaux (6 400, 12 800, 25 600, 51 200) restent un objectif très longtemps après
que tout le reste soit acheté : c'est le débouché de fond de l'antimatière, celui
qui empêche la ressource de n'avoir plus aucun usage une fois les recherches
terminées.

Seul le Bras automatique a plusieurs niveaux, parce que sa cadence est son
effet. Les autres n'ont qu'un seul palier : ils font une chose, ils la font
bien, et un découpage en niveaux n'aurait fait qu'étaler artificiellement une
dépense. Contremaître : un achat par seconde (`CONTRE_S`). Sonde : ramassage
2 s après l'apparition (`SONDE_S`), largement sous les 14 s de durée de vie
d'une anomalie.

Sous la liste, une section **Réglages** regroupe deux boîtes. Chacune contient,
dans cet ordre : l'intitulé et sa commande sur la même ligne, puis un trait, puis
le **chiffre concret** du moment, puis l'explication en petit. Tout ce qui
concerne un réglage est ainsi enfermé dans son propre cadre — aucune ambiguïté
sur l'explication qui va avec quoi — et le joueur n'a jamais de calcul mental à
faire pour savoir ce que son réglage donne réellement.

**Les automates ne dépensent pas plus de** — menu déroulant, paliers de 10 % à
100 % (`PARTS`, `S.autoPart`, 30 % par défaut). Le Contremaître et l'Ingénieur
n'achètent que si le prix ne dépasse pas cette part du minerai possédé. Sous
100 %, chaque achat laisse forcément un reste : la réserve ne tombe donc jamais
à zéro. Plus le plafond est bas, plus le stock grossit et plus il reste de quoi
acheter soi-même — au prix de quelques structures en moins. La ligne affichée
donne le prix plafond du moment (`oreDispo()`).

**Relancer le cycle à partir de** — un **seuil saisi à la main**, en antimatière
(`S.autoCyc`, 50 par défaut, `cycSeuil()` en garantit au moins 1). Le Cycle
automatique repart dès que `amGain()` atteint ce nombre. Un seuil absolu se
comprend sans explication, mais ne suit pas la progression : c'est au joueur de
le relever, et la ligne sous le champ lui donne son gain courant, ce qu'il reste
à atteindre et une estimation de temps pour l'aider à choisir. La carte du Cycle
automatique reprend le même seuil.

Le champ n'est jamais réécrit pendant la frappe (`document.activeElement`), et
une valeur vide ou nulle est ignorée : le dernier seuil valide est restauré à la
sortie du champ.

Les sauvegardes antérieures peuvent contenir un plafond disparu (25 %) :
`normPart()` le ramène silencieusement au palier le plus proche.

**Garde-fou** — `runAutos()` plafonne le temps traité à 1 seconde. Un retour
d'arrière-plan ou de hors-ligne ne déclenche donc jamais des milliers de clics
d'un coup : l'automatisation ne joue pas pendant l'absence, seuls les gains
hors-ligne habituels s'appliquent.

---

## Les succès

**71 succès**, chacun donnant **+1 % de production** — soit **+71 %** au
complet. Ils ne sont **jamais perdus** au prestige.

L'onglet les range en **huit catégories** (tableau `ACHCATS`, dont l'ordre est
celui de l'affichage ; le champ `c` de chaque succès dit à quelle section il
appartient). Chaque intitulé de section affiche sa progression, et passe en doré
une fois la catégorie complète.

| Catégorie | Nombre | Ce qu'elle mesure |
|---|---|---|
| 🖱️ Clics | 12 | nombre de clics jusqu'à 1 000 000, puissance de clic de 1 M à 1 Sx |
| 🏗️ Structures | 12 | drones, sphères de Dyson, paliers « X de chaque », totaux |
| ⛏️ Extraction | 8 | minerai extrait, de 1 M à 1 Oc |
| ⚡ Production | 5 | production par seconde, de 1 K/s à 1 Qa/s |
| 🔬 Améliorations et recherches | 4 | achats d'améliorations, complétion des deux arbres |
| ✦ Anomalies | 14 | anomalies attrapées, au total et par type |
| ♻️ Cycles et antimatière | 8 | nombre de cycles, antimatière possédée |
| ⚙️ Automatisation | 8 | achat et usage des automates |

Les deux échelles de la catégorie Clics sont volontairement séparées : le
**nombre** de clics (que le Bras automatique fait grimper de 10/s, soit
1 000 000 en une trentaine d'heures) et la **puissance** d'un clic. Le plafond
de puissance est monté jusqu'à 1 Sx parce que 1 Qa se franchit vers « 100 de
chaque structure + toutes les améliorations + 100 antimatière », donc bien avant
la fin de partie ; à 250 de chaque et 100 000 antimatière on dépasse 1 Sx.

Deux succès de la catégorie Anomalies ne se comptent pas : **Résonance
parfaite** demande deux bonus actifs en même temps, et **Réflexe éclair** une
anomalie ramassée **à la main** en moins de 2 s. Comme la Sonde attend
justement 2 s, celui-là ne peut se décrocher qu'en étant réellement devant
l'écran — c'est le seul succès du jeu qui demande de l'adresse.

Les seuils par **type** d'anomalie sont calés sur les probabilités de tirage :
à 500 anomalies attrapées on a en moyenne 223 filons, 223 caches, 25
surtensions, 25 échos et 5 bonds temporels. Les cinq seuils (200 / 200 / 25 /
25 / 5) se débloquent donc à peu près au même moment que « Œil du vide », qui
demande 500 anomalies. Le comptage par type utilise la clé `k` de chaque entrée
de `ANOMS` et le compteur `S.anomK`.

Les huit succès d'automatisation vont du premier achat (Délégation) au Bras
automatique niveau 10 et à tous les automates au maximum (Colonie autonome).
Deux d'entre eux portent sur l'**usage** et non sur l'achat : 50 anomalies
ramassées par la Sonde (`S.asonde`) et 10 puis 100 cycles relancés par le Cycle
automatique (`S.acyc`) — deux compteurs ajoutés à la sauvegarde, sans effet sur
les sauvegardes antérieures qui repartent simplement de zéro.

---

## Les gains hors-ligne

Le temps passé hors du jeu est crédité au retour, plafonné et à rendement
réduit :

```
gain = production/s × min(absence, plafond) × rendement
```

- **Plafond :** 4 h de base, +3 h par niveau de Mémoire tampon → **28 h**.
- **Rendement :** 35 % de base, +10 points par niveau d'Automatisation → **95 %**.

Une absence de **moins de 90 secondes** — changement d'onglet, écran verrouillé
un instant — est payée **plein tarif, sans plafond**. Au-delà, les règles
ci-dessus s'appliquent et un message annonce le gain au retour.

Un bonus temporaire expiré pendant l'absence n'est pas compté, et une horloge
système qui recule ne crédite rien.

---

## Sauvegarde

La partie est enregistrée automatiquement **toutes les 20 secondes**, ainsi
qu'à chaque fermeture d'onglet, dans le `localStorage` du navigateur — donc liée
au domaine et à l'appareil.

Le bouton **Export / Import** produit un code texte qui contient toute la
partie : c'est le seul moyen de la transférer d'un appareil à l'autre, ou de la
récupérer si tu changes d'hébergement. Si le stockage est indisponible
(navigation privée stricte), le jeu bascule en mémoire seule et le signale.

---

## Interface

**Ordinateur** — colonne de gauche fixe (planète, tuiles, badges), onglets et
listes à droite. La colonne devient défilable si la fenêtre est trop courte.

**Mobile** — en-tête collant en haut (planète à droite, tuiles 2×2 à gauche,
badges en dessous), listes en dessous. Un **balayage horizontal** dans la zone
de contenu passe d'un onglet à l'autre.

**Barre d'onglets** — chaque onglet porte une icône et son libellé **complet**,
sur toutes les tailles d'écran : plus d'abréviations du genre « Amélio. » ou
« Stats ». Quand l'ensemble ne tient pas — c'est le cas dès 430 px, où les six
onglets réclament 772 px — la barre **défile horizontalement**, et un dégradé
apparaît du côté où il reste des onglets à voir (classes `fl` / `fr` posées par
`majNavFade()`). Le masque est appliqué au conteneur défilant lui-même, il ne
bouge donc pas avec le contenu. Changer d'onglet recentre l'onglet actif dans la
barre, y compris par balayage.

**Toucher** — un achat n'est validé qu'au relâchement du doigt, et seulement si
tu n'as pas bougé de plus de 12 px en moins de 0,9 s : faire défiler ne déclenche
jamais d'achat par erreur. Le clic sur la planète reste instantané.

**Zoom au double-appui** — neutralisé par trois barrières successives, parce
qu'aucune n'est fiable partout : `touch-action:manipulation` posé explicitement
sur chaque conteneur défilant, l'annulation du second appui rapproché
(500 ms / 45 px) et de l'événement `dblclick`, et — **uniquement dans
l'application installée** — le verrouillage de l'échelle dans la balise
`viewport`. Cette dernière n'est pas appliquée dans un navigateur ordinaire :
y désactiver le zoom serait un problème d'accessibilité. Les champs de saisie et
les menus déroulants sont exclus de l'annulation, sans quoi un second appui
rapproché les empêcherait de s'ouvrir. Le pincement reste possible dans le
navigateur, et n'est bloqué que dans l'application.

**En-tête mobile non défilant** — `#hero` porte `overflow-y:auto` pour la colonne
d'ordinateur, où elle sert vraiment quand la fenêtre est basse. Sur mobile, où
l'en-tête est en position fixe et de hauteur automatique, cette valeur en faisait
une zone défilante inutile : un glissement sur la planète « rebondissait » au
lieu de ne rien faire. La règle est donc annulée sous 880 px.

**Une couleur par unité** — la même dans tout le jeu, tuiles comprises :

| | Couleur |
|---|---|
| Minerai | doré `--gold` |
| Antimatière | violet `--violet` |
| Production par seconde | cyan `--cyan` |
| Multiplicateur global | vert `--green` |

Le multiplicateur, qui occupait le doré, a été déplacé sur le vert : ce n'est pas
une unité mais un résultat, et le doré revient au minerai, qu'il désignait déjà
dans tous les prix. Un prix payé en antimatière porte la classe `.cost.am` et
passe donc en violet ; un « MAX » n'est plus un prix et passe en vert.

**Niveaux possédés** — dans **Recherche** et **Automatisation**, le coin
inférieur droit de chaque carte affiche le niveau possédé (`3/15 niveaux`,
`✓ acquis` pour un automate à palier unique), exactement là où l'onglet
Extraction affiche le nombre d'exemplaires. Ce qui manquait en antimatière n'y
est plus affiché : la carte grisée et la barre de progression le disaient déjà,
et le niveau possédé est l'information qu'on cherche vraiment du regard.
L'onglet **Améliorations** n'est pas concerné, ses achats étant uniques.

**Statistiques** — l'onglet est découpé en six sections (`STATCATS`), chacune
affichant ses valeurs sous forme de **tuiles** : intitulé en petit au-dessus,
valeur en gros en dessous, et une barre de couleur à gauche propre à la section.
L'ancienne présentation en lignes étiquette-à-gauche / valeur-à-droite devenait
illisible sur un écran large, où les deux se retrouvaient séparées de près de
900 px. Le contenu est déclaré dans le tableau `STATS` : chaque entrée porte sa
catégorie, son intitulé bilingue et la fonction qui calcule sa valeur. On y
trouve notamment le **détail des anomalies par type**, invisible ailleurs dans
le jeu.

**Langue** — sélecteur FR / EN en haut à droite ; la langue par défaut suit celle
du navigateur et ton choix est mémorisé. Changer de langue ne touche pas à la
partie en cours.

---

## Version

Le numéro de version est défini en haut du `<script>` :

```js
const VERSION="2.15.0", BUILD="2026-08-22";
```

Il s'affiche à côté du titre sur ordinateur, et dans la dernière ligne de
l'onglet **Statistiques** sur toutes les tailles d'écran. C'est le moyen le plus
simple de vérifier quelle version est réellement servie, le service worker
pouvant garder une page en cache.

**Règle d'incrémentation** (`MAJEUR.MINEUR.CORRECTIF`) :

| Partie | Quand l'incrémenter | Exemples |
|---|---|---|
| **MAJEUR** | refonte visible ou changement des règles du jeu | passage à l'en-tête collant, nouvelle monnaie |
| **MINEUR** | nouvelle fonctionnalité, nouveau contenu, équilibrage | nouvelle anomalie, nouveaux succès, probabilités modifiées |
| **CORRECTIF** | correction de bug, retouche de texte ou de mise en page | libellé raccourci, débordement corrigé |

Une même livraison ne fait avancer qu'un seul niveau — le plus élevé concerné —
et remet à zéro ceux de droite : après `2.0.0`, une correction donne `2.0.1`,
un nouveau contenu `2.1.0`.

### Historique

| Version | Contenu |
|---|---|
| **2.15.0** | niveau possédé en bas à droite des cartes Recherche et Automatisation ; une couleur par unité dans tout le jeu (minerai doré, antimatière violette, multiplicateur vert) |
| 2.14.0 | barre d'onglets refaite : une icône par onglet, libellés complets partout et défilement horizontal avec dégradés de bord |
| 2.13.2 | zoom au double-appui : trois barrières au lieu d'une, dont le verrouillage de l'échelle dans l'application installée ; l'en-tête mobile n'est plus une zone défilante |
| 2.13.1 | les automates à palier unique affichent « Prix » au lieu de « Prix du niveau 1 » |
| 2.13.0 | le Bras automatique démarre à 100 antimatière au lieu de 30 (toujours ×2 par niveau) : 102 300 pour les dix niveaux |
| 2.12.1 | le projet passe sous licence GPL 3.0 ou ultérieure : fichier `LICENSE`, en-têtes dans `index.html` et `sw.js`, tuile « Licence » dans les statistiques |
| 2.12.0 | écran des statistiques refait en tuiles groupées par thème, avec le détail des anomalies par type ; succès « Réflexe éclair » (71 au total) |
| 2.11.0 | 5 succès de plus (70 au total) : 100 000 et 1 000 000 de clics, puissance de clic jusqu'à 1 Sx, et 1 000 anomalies |
| 2.10.0 | 13 succès de plus (65 au total) : quatre paliers de clic, dont deux au-delà de 1 B, et neuf sur les anomalies dont un par type, calés sur leurs probabilités |
| 2.9.0 | les succès sont rangés en huit catégories avec leur progression, et huit succès d'automatisation s'ajoutent (52 au total, +52 % au complet) |
| 2.8.0 | les réglages d'automatisation deviennent deux cadres autonomes sous une section « Réglages », et le plafond de dépense passe en menu déroulant |
| 2.7.0 | plafond de dépense par paliers de 10 % ; seuil de relance du cycle saisi à la main, en antimatière |
| 2.6.0 | les deux réglages d'automatisation passent de pourcentages à trois modes nommés, doublés d'une ligne qui affiche le chiffre concret du moment |
| 2.5.0 | Bras automatique jusqu'au niveau 10 (jusqu'à 10 clics/s) ; Contremaître à 300 et Ingénieur à 450 antimatière |
| 2.4.0 | Contremaître, Sonde et Cycle automatique passent à un palier unique (200 / 600 / 1 000 antimatière) au lieu de niveaux successifs |
| 2.3.0 | onglet **Automatisation** : bras automatique, contremaître, ingénieur, sonde de récupération et cycle automatique, achetés en antimatière et coupables à volonté |
| 2.2.0 | le bonus d'antimatière n'est plus linéaire : le total est élevé à la puissance 1,5 (`AM_EXP`), pour que les cycles tardifs restent rentables |
| 2.1.0 | toutes les anomalies tirent leur valeur au hasard ; le badge et le message affichent le montant obtenu |
| 2.0.0 | version publique consolidée : PWA installable, bilingue FR/EN, en-tête mobile fixe, 44 succès, anomalies pondérées avec bond temporel |
| 1.0.0 | première numérotation, introduite en même temps que l'affichage de version |

---

## Modifier l'équilibrage

Tout est regroupé en haut du `<script>` dans `index.html` :

| Ce que tu veux changer | Où |
|---|---|
| Structures, prix, production | tableau `GENS` |
| Croissance des prix (1,15) | constante `GROWTH` |
| Paliers d'amélioration | tableau `TIERS` |
| Améliorations de clic / globales | appels `UPS.push(...)` |
| Recherches | tableau `RES` |
| Succès | tableau `ACHS` |
| Catégories de succès | tableau `ACHCATS` |
| Contenu des statistiques | tableaux `STATS` et `STATCATS` |
| Anomalies, effets et **probabilités** (`w`) | tableau `ANOMS` |
| Fréquence des anomalies | `anomInterval()` |
| Gain de prestige | `amGain()` |
| Automatisations, prix, cadences | tableau `AUTOS`, `CONTRE_S`, `SONDE_S` |
| Paliers du plafond de dépense | tableau `PARTS` |
| Bonus par antimatière | `amBonus()`, `amMult()`, `AM_EXP` |
| Hors-ligne | `offlineCap()`, `offlineRate()`, `GRACE` |
| Textes des deux langues | objet `T` |
| Numéro de version | constantes `VERSION` et `BUILD` |
| Licence affichée dans le jeu | constante `LICENCE` |

Les poids `w` du tableau `ANOMS` totalisent 200 : un point vaut 0,5 %.

---

## Licence

**GNU General Public License v3.0 ou ultérieure** (`GPL-3.0-or-later`).
Le texte complet est dans le fichier [`LICENSE`](LICENSE).

Ce que ça veut dire, en clair :

- Tu peux **utiliser, copier, modifier et redistribuer** le jeu, y compris
  commercialement.
- Toute version modifiée que tu **distribues** doit l'être elle aussi sous
  GPL 3, avec son code source et la mention des changements.
- Il est donc impossible d'en faire une version fermée ou de l'intégrer à un
  logiciel propriétaire.
- Le jeu est fourni **sans aucune garantie** ; l'auteur n'est responsable
  d'aucun dommage lié à son utilisation.

Point pratique propre à ce projet : le jeu est un fichier HTML lisible servi tel
quel au navigateur. Le code source *est* ce que reçoit le visiteur — un simple
« afficher la source » suffit à vérifier qu'une version dérivée respecte bien la
licence.

La licence couvre le **code**. Elle ne couvre ni le nom « Colonie Orbitale »
(droit des marques, distinct) ni l'idée du jeu : quelqu'un qui réécrit un jeu
équivalent depuis zéro ne lui est pas soumis.

Pour l'appliquer à ton nom complet, remplace `Guilhem` dans l'en-tête de
`index.html`, celui de `sw.js` et la section ci-dessus.
