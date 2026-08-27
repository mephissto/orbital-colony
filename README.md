# Colonie Orbitale

🇫🇷 Français · [🇬🇧 English](README.en.md)

Idle game spatial, bilingue FR / EN, installable en application (PWA) et jouable
hors connexion. Tout le jeu tient dans `index.html` : aucune dépendance, aucun
serveur, aucune donnée qui sort de ton navigateur.

Logiciel libre sous [GPL 3.0 ou ultérieure](#licence).

**Jouer en ligne :** [orbital-colony.mephissto.fr](https://orbital-colony.mephissto.fr/) ·
[mephissto.github.io/orbital-colony](https://mephissto.github.io/orbital-colony/)

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
| `README.md` / `README.en.md` | ce document, en français et en anglais |
| `CHANGELOG.md` / `CHANGELOG.en.md` | une note de version par livraison |
| `ROADMAP.md` | ce qui est prévu pour la suite, et pourquoi |

Tous les fichiers vont **à la racine du dépôt**, à plat. L'installation exige
HTTPS — GitHub Pages et Netlify le fournissent automatiquement.

**Installer :** Chrome Android → menu ⋮ → « Installer l'application » ; Safari
iOS → Partager → « Sur l'écran d'accueil » ; sur ordinateur, l'icône
d'installation apparaît dans la barre d'adresse.

**Le jeu le propose de lui-même** depuis la 2.35.0 : une fenêtre s'ouvre au bout
de deux secondes chez les joueurs sur **mobile hors mode application**, avec le
mode d'emploi Android et iOS côte à côte. La mise en garde iOS (⚠️ Safari
obligatoire) est détachée sur sa propre ligne — le libellé porte du balisage,
d'où l'attribut `data-i18n-html` qui bascule `applyI18n()` de `textContent` vers
`innerHTML` pour cet élément. Trois conditions pour l'afficher —
pointeur tactile, écran ≤ 1024 px, et `display-mode` différent de `standalone`
(plus `navigator.standalone` pour Safari iOS, qui ne suit pas le standard). Sur
Chrome/Android, l'événement `beforeinstallprompt` est capté : un bouton
**Installer** s'ajoute alors et déclenche le vrai dialogue système. Ailleurs — iOS
en tête, qui n'expose rien — il ne reste que le mode d'emploi, d'où les deux
tutoriels affichés ensemble plutôt qu'un seul choisi sur l'user-agent, toujours
faillible.

Un refus est mémorisé **par appareil**, dans une clé `localStorage` distincte
(`colonie_orbitale_pwa`) et non dans la sauvegarde : refuser sur son téléphone ne
doit pas suivre la partie exportée vers une autre machine, ni réapparaître après
un import. Une **pastille 📲 en bas à droite** prend alors le relais et rouvre la
fenêtre à la demande ; la bande des toasts lui réserve sa largeur pour ne pas la
recouvrir.

La clé connaît **trois valeurs**, et une seule ligne de code les sépare :

| Valeur | Comment on y arrive | Ce qui reste affiché |
|---|---|---|
| *(absente)* | premier lancement | la fenêtre s'ouvre |
| `no` | « Fermer » | la pastille seule |
| `never` | « Fermer » avec la case cochée | rien du tout |
| `ok` | installation acceptée (`appinstalled`) | rien du tout |

La case **« Ne plus me le rappeler sur cet appareil »** (2.35.2) existe pour les
joueurs qui veulent rester dans leur navigateur : sans elle, la pastille était
définitive et le seul moyen de s'en débarrasser était d'installer le jeu. Elle
est décochée à chaque ouverture de la fenêtre — un refus déjà mémorisé ne doit
pas se transformer en refus définitif au premier passage par la pastille.

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
clic = ( frappe + écho ) × améliorations de clic × Bras servo × bonus de clic

  frappe = 1 × multiplicateur global
  écho   = production/s × meilleur résonateur possédé
```

- **Améliorations de clic** — **sur la valeur totale du clic**, écho compris :
  Marteau ionique ×1,5, Exosquelette ×1,6, Condensateur ×1,8, Champ magnétique
  ×2. Au complet : **×8,64**. Chacune vaut exactement son ×N quel que soit ton
  avancement, ce que la carte annonce en un seul nombre.
- **Bras servo-assistés** (recherche) — **+8 % par niveau sur le clic entier**,
  12 niveaux, soit **×2,52** au maximum. Jusqu'à la 2.32.0 c'était ×2 par niveau
  **sur la seule frappe** : mesuré, un joueur avancé relançant un cycle obtenait
  exactement le même minerai et la même production au bout de 5 minutes avec 0 ou
  12 niveaux — 8 675 antimatière pour rien. Le barème de prix n'a pas bougé d'un
  antimatière, ce qui garde la recherche cohérente avec les sept autres (dont les
  premiers niveaux coûtent tous entre 6 et 30).
- **Résonateurs** — ajoutent un pourcentage de ta production par seconde à
  chaque clic : v1 +2 %, v2 +5 %, v3 +10 %. Seul le meilleur compte, ils ne se
  cumulent pas entre eux. En fin de partie c'est ce terme qui domine largement.

**La règle d'équilibrage : un clic ne doit jamais dépasser la production/s.**
Avec le résonateur v3 à 40 % (jusqu'à la 2.30.0), le clic valait déjà 0,40 s de
production **avant toute amélioration de clic** — donc aucun multiplicateur
au-dessus de ×2,5 ne pouvait s'y ajouter sans casser la règle. C'est pourquoi les
résonateurs sont descendus à 2/5/10 % en même temps que les multiplicateurs sont
passés à ×1,5–×2 : sans les Bras servo, le clic **plafonne à 0,86 s de
production** tout en restant 2,15× plus fort qu'à l'origine.

**La seule chose autorisée à franchir ce plafond, ce sont les Bras servo** — et
seulement au prix des douze niveaux payés : à 12/12 le clic atteint **2,18 s de
production**. C'est un dépassement assumé, un joueur qui a investi 8 675
antimatière dans une recherche a le droit d'en voir l'effet.

| Étape | Amél. de clic | Servo | Production/s | Clic | sec. de prod./clic |
|---|---|---|---|---|---|
| Premières minutes | 0 | 0/12 | 3 | 1 | 0,33 |
| Exosquelette | 2 | 1/12 | 485 | 3,6 | 0,01 |
| Résonateur v2 | 3 | 7/12 | 20,5M | 7,61M | 0,37 |
| Champ magnétique | 4 | 9/12 | 822M | 710M | 0,86 |
| Résonateur v3 | 4 | 11/12 | 80,6B | 162B | 2,01 |
| Partie très avancée | 4 | 12/12 | 407T | 887T | **2,18** |

À surveiller si ces valeurs bougent : les **Satellites d'extraction** cliquent
jusqu'à 10 fois par seconde. À 2,18 s de production par clic, ils rapportent donc
**21,8 fois la production passive** — le clic automatique reste, comme avant, la
première source de minerai d'un cycle avancé.

**Historique de ce calcul.** Jusqu'à la 2.28.0, les améliorations de clic ne
multipliaient que la frappe — une base de 1 qui ne grandit jamais — alors que
l'écho suit la production. Mesuré : le Champ magnétique ×8 valait ×7,33 en début
de partie mais **×1,00** avec le résonateur v3 et une grosse production, pour
2 milliards de minerai. La 2.29.0 a essayé un effet double (×N sur la frappe +
points d'écho) : correct sur le papier, mais illisible sur une carte. La 2.30.0
tranche avec **un seul nombre appliqué à tout**.

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
apparaissent dans la liste dès que tu as extrait 8 % de leur prix **sur le cycle
en cours** (`S.runOre`). Le critère reste monotone à l'intérieur d'un cycle — la
liste ne saute jamais pendant que tu joues — mais il repart de zéro au prestige,
comme les améliorations elles-mêmes : chaque cycle redécouvre sa liste au fil de
l'extraction. Jusqu'à la 2.28.0 il s'appuyait sur le total de la partie, jamais
remis à zéro : dès le deuxième cycle la liste entière apparaissait d'un coup.

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

**Améliorations de clic** — sur le clic entier : Marteau ionique ×1,5 (400),
Exosquelette ×1,6 (35 000, dès 50 clics), Condensateur ×1,8 (5e6, dès 250 clics),
Champ magnétique ×2 (2e9, dès 600 clics). Au complet : **×8,64**.

**Résonateurs** — v1 +2 % (2e5), v2 +5 % (4e8, exige v1), v3 +10 % (6e11, exige v2).

**Améliorations globales** — Réseau logistique ×1,25 (5e4), IA de coordination
×1,5 (8e6), Relais quantique ×2 (1,2e9), Bio-ingénierie ×2,5 (3e11), Moteur à
singularité ×4 (9e13). Au complet : **×37,5**.

**Balise d'anomalie** (1e7) — les anomalies apparaissent 30 % plus souvent.

Total : **73 améliorations**.

**Rangement des acquises** — la liste des améliorations déjà achetées est
découpée en sous-sections : une par structure pour les paliers (dans l'ordre de
l'onglet Extraction), puis Puissance de clic, Résonance du clic, Production
globale et Anomalies. Chaque en-tête porte un compteur `acquis/total` qui passe
en doré une fois la famille complète. Une catégorie n'apparaît qu'à partir de la
première amélioration acquise dedans : le compteur dit *combien* il en reste,
jamais lesquelles, et rien ne se dévoile avant l'heure.

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

Les multiplicateurs sont volontairement rares, et **un seul bonus peut être
actif à la fois**, production et clic confondus : un nouveau remplace le
précédent. Ils se cumulaient jusqu'à la 2.21.0 — quatre surtensions ×10
attrapées coup sur coup donnaient **×10 000 sur la production**, et un bonus de
clic par-dessus portait le tout à ×490 000. Le filon et la cache, eux, restent
proportionnés à la progression. Les durées sont allongées de 30 % par niveau de
Détecteur.

Le filon et le bond temporel calculent leur gain sur ta production **de base**,
sans tenir compte d'une Surtension en cours : sinon, attraper un filon ou un
bond juste après une Surtension ×10 aurait multiplié leur gain par 10, en
contradiction avec la règle ci-dessous (« jamais un pouvoir que tu n'as pas
déjà, seulement du temps d'avance »). Corrigé en 2.21.2.

Le bonus de clic ne s'applique **qu'aux clics du joueur**, pas à ceux des
Satellites d'extraction. Sinon l'Écho quantique cessait d'être une récompense du
joueur présent pour devenir un multiplicateur de production déguisé : à dix
satellites et résonateur au maximum, un clic vaut 0,4 fois la production, donc un
×12 sur les clics automatiques valait ×49 sur la production totale. À la main, à
cinq clics par seconde, un ×12 rapporte encore l'équivalent de 24 fois la
production — la récompense reste forte, mais il faut être devant l'écran.

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
gain = ⌊ ( minerai extrait pendant ce cycle ÷ 1e10 ) ^ 0,30 ⌋
```

Il faut donc **10 milliards** de minerai extrait sur le cycle pour la première
unité (`AM_SEUIL`), et l'exposant `AM_EXPG` vaut **0,30**. Le rendement décroît
très vite : 101 milliards pour 2 unités, 21,5 T pour 10, 46,4 Qa pour 100,
100 Qi pour 1 000.

L'exposant est passé de 0,32 à 0,30 en **2.33.0**. Les corrections du clic
(2.30 → 2.32) avaient, sans qu'on le vise, presque doublé le minerai d'un cycle
avancé — donc le gain d'antimatière, passé de 36,2K à 69,9K par cycle. Or
l'antimatière n'a qu'un seul débouché, les 8 recherches (234 890 au total) :
doubler le gain revenait à diviser par deux le temps avant qu'elle ne serve plus
à rien. L'exposant est le bon levier plutôt que le seuil, parce qu'il **ne touche
pas au premier prestige** (1 antimatière dans les deux cas) et corrige d'autant
plus fort que le cycle est gros, exactement là où l'inflation s'est produite.

| Cycle | Minerai | Gain à 0,32 | Gain à 0,30 |
|---|---|---|---|
| 1er prestige | 10,0B | 1 | 1 |
| Intermédiaire | 10,0Qa | 83 | 63 |
| Avancé | 100Qi | 1,58K | 999 |
| Très avancé | 13,8Sp | 69,9K | 34,8K |

Maxer les 8 recherches repasse ainsi de **4 cycles à 7** pour un joueur très
avancé, et un joueur tout équipé retrouve le rendement d'avant la 2.30.0
(×0,96).

### Pourquoi cet exposant, et pas une racine carrée

Jusqu'à la 2.19.0 le gain valait `12 × √(minerai / 1e12)`. Mesuré en simulation
— rachat automatique des structures et des améliorations, seuil de relance à
+50 % —, un cycle durait alors **une vingtaine de secondes, à 100 comme à
1 000 000 d'antimatière**. L'antimatière était de fait gratuite, et aucun prix
de recherche n'y pouvait rien : à ce rythme, n'importe quel barème est épuisé en
quelques minutes.

La cause n'est pas le seuil mais l'exposant. La longueur d'un cycle est fixée par
le **rachat des structures**, pas par le seuil d'antimatière : une fois les
structures reconstruites, la production est telle que le seuil tombe
instantanément, quel qu'il soit. Multiplier le seuil par 16 ne faisait passer un
cycle que de 21 à 40 secondes. Il fallait que le minerai réclamé grandisse **plus
vite que la production**, donc un exposant nettement sous 0,5.

Durée d'un cycle rapportant +50 % d'antimatière, avec les recherches montées en
parallèle :

| Antimatière | Avant (racine carrée) | Après (exposant fractionnaire) |
|---|---|---|
| 200 | 21 s | 25 min |
| 1 000 | 18 s | 9 min |
| 5 000 | 19 s | 13 min |
| 20 000 | 20 s | 47 min |
| 100 000 | 22 s | 1,5 h |
| 1 000 000 | 26 s | 18,8 h |

Les cycles les plus courts se situent désormais autour de 1 000 à 5 000
antimatière — le cœur de la partie — puis s'allongent progressivement. Le seuil
de la première unité a été **abaissé** en même temps (10 milliards au lieu des
6,94 d'origine puis 20 en 2.19.0) : c'est le haut de la courbe qu'il fallait
étirer, pas le début de partie.

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

| Recherche | Effet par niveau | Max | Base | Croissance | Dernier niveau | Total |
|---|---|---|---|---|---|---|
| ⚙️ Optimisation minière | +30 % de production | 15 | 8 | ×1,8 | 29 986 | 67 463 |
| 🤖 Bras servo-assistés | ×2 puissance de clic | 12 | 6 | ×1,8 | 3 857 | 8 675 |
| 💾 Mémoire tampon | +3 h de gains hors-ligne | 8 | 10 | ×2 | 1 280 | 2 550 |
| 🔁 Automatisation | +10 % d'efficacité hors-ligne | 6 | 14 | ×2,1 | 572 | 1 081 |
| 💠 Négociation | −4 % sur le coût des structures | 10 | 16 | ×2 | 8 192 | 16 368 |
| 📶 Détecteur d'anomalies | anomalies +25 % fréquentes, +30 % longues | 5 | 20 | ×2,2 | 469 | 843 |
| ✨ Résonance d'antimatière | +1,5 % de bonus par antimatière | 10 | 30 | ×2,4 | 79 255 | 135 847 |
| 📦 Capsule de départ | minerai offert à chaque nouveau cycle | 6 | 22 | ×2,2 | 1 134 | 2 063 |

Tout terminer coûte **234 890 antimatière**, dont 135 847 pour la seule
Résonance. La croissance ne descend jamais sous ×1,8 : chaque niveau doit coûter
visiblement plus que le précédent **dès le début**. L'ancien barème partait de
4 antimatière avec une croissance de ×1,55, ce qui donnait 4 → 7 → 10 : la
progression était bien là, mais invisible à l'œil sur d'aussi petits nombres.

La Capsule donne `10 000 × 25^niveau` de minerai au début de chaque cycle, soit
2 441 milliards (2,44e12) au niveau 6. Ce minerai est **offert, pas extrait** : il
ne compte donc pas dans le total du cycle et ne rapporte aucune antimatière. Avant
la 2.21.0 il y comptait, et chaque cycle démarrait au niveau 6 avec 5 antimatière
déjà acquises sans avoir joué une seconde.

---

## L'automatisation

Onglet **Auto**, révélé au premier cycle de prestige. Comme les recherches, les
automates se paient en **antimatière** et ne sont jamais perdus. Ils font gagner
du confort, pas de la puissance : les Satellites d'extraction ne font rien qu'un joueur
présent ne puisse faire à la main.

**Les satellites d'extraction** — dès qu'il tourne, **un point par
niveau** se met en orbite autour de la planète, réparti à parts égales sur le
cercle : on lit son niveau en les comptant. **Les deux cadences sont fixes**, un
tour en 8 s et une pulsation de 2,4 s par point, quel que soit le niveau — c'est
le nombre de points qui porte l'information, la vitesse n'a rien à ajouter. Les
pulsations sont simplement décalées d'un point au suivant, de sorte qu'une onde
fait le tour de la couronne exactement une fois par rotation. La carte de
l'onglet Automatisation, elle, ne clignote pas : elle porte déjà « N satellites
en orbite » en toutes lettres, et une pastille animée de plus n'ajoutait rien
qu'une distraction dans un panneau qu'on vient consulter, pas surveiller.

Le rayon de l'orbite est calibré pour que les points ne débordent nulle part :
sur mobile, la place utile est celle qui sépare le bas de la pastille d'anomalie
du haut de la barre d'onglets, et la planète réserve en plus une marge verticale
sur ordinateur.

Côté coût, un seul élément est animé — la couronne entière — et uniquement par
`transform:rotate`, la propriété que le compositeur traite sur le GPU sans
recalculer la mise en page ni redessiner. Le DOM n'est reconstruit qu'au
changement de niveau (`majOrbite()` sort immédiatement si le compte n'a pas
bougé) : en régime établi il n'y a plus une ligne de JavaScript par image. La
période d'une animation ne change d'ailleurs rien à son coût — elle est jouée à
la fréquence de l'écran quelle que soit sa durée —, donc le niveau 10 ne pèse pas
plus lourd que le niveau 1 à ce titre.

L'orbite disparaît si l'automate est coupé, et l'animation est désactivée si le
système demande des mouvements réduits (`prefers-reduced-motion`).

Volontairement, aucun « +N » flottant n'est émis pour un clic automatique : à
10 clics/s ce serait illisible, et ça noierait les « +N » des clics manuels, qui
sont le retour visuel du geste du joueur.

**Chaque automate possède un interrupteur**, dans la section Réglages en tête
d'onglet. Le couper ne rembourse rien et ne fait pas perdre les niveaux : il
suffit de le rallumer.

| Automate | Effet | Max | Coût | Coût cumulé |
|---|---|---|---|---|
| 🛰️ Satellites d'extraction | +1 clic/s par niveau | 10 | 30, ×2 par niveau | 30 690 |
| ⬆️ Ingénieur | achète l'amélioration la moins chère payable | 1 | 100 | 100 |
| 🏗️ Contremaître | achète chaque seconde la structure que tu lui désignes | 1 | 150 | 150 |
| 📡 Sonde de récupération | ramasse l'anomalie à ta place | 1 | 200 | 200 |
| ♻️ Cycle automatique | relance un cycle au seuil choisi | 1 | 400 | 400 |

**Pourquoi le Contremaître coûte plus cher que l'Ingénieur** (150 contre 100,
échangés en 2.34.0) : les deux sont exclusifs, et celui qu'on garde allumé en
pratique est le Contremaître, qui achète des structures en continu. Le travail
de l'Ingénieur, lui, est **fini** une fois les 73 améliorations achetées — passé
ce point il n'a plus rien à faire. Le plus utile devait donc être le plus cher.
Le total de l'automatisation est inchangé : c'est un échange, pas une hausse.

Tout automatiser coûte **31 540 antimatière**, contre 234 890 pour terminer les
recherches. Ces prix ont été divisés par 3,3 en 2.20.0 : ils avaient été fixés
quand l'antimatière s'accumulait vite, et le changement de formule du gain les
avait rendus hors de portée.

Les Satellites d'extraction en représentent à eux seuls **30 690**. Leurs quatre
derniers niveaux (1 920, 3 840, 7 680, 15 360) restent un objectif longtemps
après que tout le reste soit acheté.

Seuls les Satellites d'extraction ont plusieurs niveaux, parce que leur nombre est leur
effet. Les autres n'ont qu'un seul palier : ils font une chose, ils la font
bien, et un découpage en niveaux n'aurait fait qu'étaler artificiellement une
dépense. Contremaître : un achat par seconde (`CONTRE_S`). Sonde : ramassage
2 s après l'apparition (`SONDE_S`), largement sous les 14 s de durée de vie
d'une anomalie.

**En tête d'onglet**, une section **Réglages** regroupe les interrupteurs et les
deux réglages, chacun révélé par l'automate qu'il concerne. Chaque boîte
contient, dans cet ordre : l'intitulé et sa commande sur la même ligne, puis un
trait, puis le **chiffre concret** du moment, puis l'explication en petit. Les
réglages sont placés **avant** la liste des automates : une fois ceux-ci
achetés, la liste ne sert plus qu'à l'achat et aux niveaux alors que le panneau
est ce qu'on revient consulter.

**Automates actifs** — une ligne par automate possédé : icône, nom, état courant
et son interrupteur. Le couper ne rembourse rien et ne fait perdre aucun niveau.
Sous 520 px de large l'état passe **sous** le nom au lieu de disparaître : c'est
lui qui porte le « mise en pause par… », l'information la plus utile de la ligne.

**Le Contremaître achète** — menu déroulant listant les structures **déjà
révélées** (`genRev()`, borné par `S.seen` : rien ne se dévoile d'avance), avec
leur icône. Il n'achète que celle-là, une par seconde. Tant que le joueur n'a
rien choisi, `autoGenId()` vise la **dernière structure révélée** ; dès qu'il
choisit, `S.autoGen` est écrit et ne bouge plus tout seul. La ligne affichée
donne le prix visé, le minerai qu'il reste à posséder et une estimation de temps.

**Le Contremaître et l'Ingénieur sont exclusifs.** Ils puisent dans le même
minerai : allumer l'un met l'autre **en pause**, l'interrupteur le montre, et
c'est le joueur qui décide lequel travaille. **Un seul des deux peut tourner à la
fois, et l'interrupteur de l'autre est inerte** (`verrouille()`, curseur
`not-allowed`) : pour rendre la main au Contremaître il faut d'abord couper
l'Ingénieur. Un clic de plus, mais on ne peut jamais croire avoir rallumé un
automate qui, en réalité, ne démarrera pas.

Un automate a donc **trois états**, et les distinguer est ce qui fait marcher
l'ensemble :

| État | Champ | Levée |
|---|---|---|
| actif | — | — |
| **coupé à la main** | `S.autoOff` | jamais automatiquement : c'est une intention du joueur |
| **mis en pause** par son exclusif | `S.autoPause` | dès que l'autre s'arrête, quelle qu'en soit la raison |

`S.autoMain` retient lequel des deux a pris la main en dernier, `normExclus()`
recalcule les pauses à partir des seules intentions du joueur (idempotent, appelé
après chaque changement, au chargement et à l'import), et `buyAuto()` donne la
main à celui qu'on vient de payer. Concrètement : couper l'Ingénieur **rend la
main au Contremaître** s'il n'avait été que suspendu, mais ne ressuscite pas un
Contremaître que le joueur avait délibérément coupé. `migrerExclus()` relit une
sauvegarde d'avant la 2.26.0, où la pause était écrite comme une coupure
manuelle. Une ligne coupée par l'exclusivité affiche
« mise en pause par *l'autre automate* » plutôt que « coupé » — `enPause()`
renvoie l'automate responsable, `txtPause()` le nomme via son champ `nmd` (nom
avec article) — et **son interrupteur garde le curseur à droite, simplement
grisé** : l'automate est armé, c'est le jeu qui l'a
suspendu — le distinguer visuellement d'un automate qu'on a coupé soi-même évite
de croire qu'on l'a éteint par erreur. Les deux cartes le disent aussi dans leur
description (« met l'Ingénieur en pause » / « met le Contremaître en pause »).

Deux arbitrages **automatiques** ont été essayés puis abandonnés, parce qu'aucun
n'était lisible en jouant :

| Règle | Structures | Améliorations | Production finale |
|---|---|---|---|
| aucune (cible Drone) | 33 | 13 | 37 515 /s |
| moitié du stock au Contremaître (2.24.0) | 31 | 15 | 56 267 /s |
| réserver le prix exact de la prochaine amélioration | 0 | 15 | 56 198 /s |

Mesures sur 30 minutes simulées en milieu de partie. Réserver le prix exact
bloquait le Contremaître à zéro achat : le stock ne dépasse jamais durablement le
prix de l'amélioration suivante, puisque l'Ingénieur l'achète dès qu'il
l'atteint. La moitié du stock donnait de meilleurs chiffres, mais le joueur
voyait ses structures ralentir sans comprendre pourquoi — un arbitrage invisible
vaut moins qu'un interrupteur explicite. Le plafond de dépense en %
(`S.autoPart`), qui jouait ce rôle jusqu'à la 2.23.0, a disparu de l'interface
en 2.24.0 ; le champ reste dans l'état pour que les sauvegardes et les exports
antérieurs restent symétriques.

**Relancer le cycle à partir de** — un **seuil saisi à la main**, en antimatière
(`S.autoCyc`, 50 par défaut). `cycSeuil()` applique un **plancher à 10 % de
l'antimatière possédée** (`CYC_MIN`) : un nombre fixe ne suit pas la progression,
et réglé à 50 puis oublié il déclenchait un cycle par image une fois la réserve à
100 000 — mesuré à **600 cycles et +75 400 antimatière en une minute**. Le panneau
affiche toujours le seuil réellement appliqué, jamais la seule valeur saisie. Le Cycle
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
**nombre** de clics (que les Satellites d'extraction font grimper de 10/s, soit
1 000 000 en une trentaine d'heures) et la **puissance** d'un clic. Le plafond
de puissance est monté jusqu'à 1 Sx parce que 1 Qa se franchit vers « 100 de
chaque structure + toutes les améliorations + 100 antimatière », donc bien avant
la fin de partie ; à 250 de chaque et 100 000 antimatière on dépasse 1 Sx.

Deux succès de la catégorie Anomalies ne se comptent pas : **Résonance
parfaite** demande d'attraper un bonus alors qu'un autre est encore actif (elle
demandait deux bonus simultanés avant la 2.21.0, devenue impossible), et
**Réflexe éclair** une
anomalie ramassée **à la main** en moins de 2 s. Comme la Sonde attend
justement 2 s, celui-là ne peut se décrocher qu'en étant réellement devant
l'écran — c'est le seul succès du jeu qui demande de l'adresse.

Les seuils par **type** d'anomalie sont calés sur les probabilités de tirage :
à 500 anomalies attrapées on a en moyenne 223 filons, 223 caches, 25
surtensions, 25 échos et 5 bonds temporels. Les cinq seuils (200 / 200 / 25 /
25 / 5) se débloquent donc à peu près au même moment que « Œil du vide », qui
demande 500 anomalies. Le comptage par type utilise la clé `k` de chaque entrée
de `ANOMS` et le compteur `S.anomK`.

Les huit succès d'automatisation vont du premier achat (Délégation) aux
Satellites d'extraction au niveau 10, et à tous les automates au maximum
(Colonie autonome).
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

**Barre d'onglets** — un onglet inactif garde sa forme d'onglet, en sourdine :
même fond et même bordure que l'actif, simplement beaucoup plus discrets, avec
son icône désaturée. L'onglet courant se distingue par son fond plein et un
liseré cyan sur son bord supérieur. Auparavant seul l'onglet actif avait une
forme, les autres flottaient en texte libre et on ne voyait pas qu'il y avait
une barre.

Chaque onglet porte une icône et son libellé **complet**,
sur toutes les tailles d'écran : plus d'abréviations du genre « Amélio. » ou
« Stats ». Quand l'ensemble ne tient pas — c'est le cas dès 430 px, où les six
onglets réclament 772 px — la barre **défile horizontalement**, et un dégradé
apparaît du côté où il reste des onglets à voir (classes `fl` / `fr` posées par
`majNavFade()`). Le masque est appliqué au conteneur défilant lui-même, il ne
bouge donc pas avec le contenu. Changer d'onglet ajuste la vue au **minimum**
nécessaire pour révéler l'onglet choisi — jamais plus — y compris par balayage.
Deux flèches ◀▶ apparaissent en superposition du dégradé pour défiler à la
souris ou au trackpad ; elles ne s'affichent que du côté où il reste des
onglets cachés, et seulement s'il n'y a pas d'écran tactile (`pointer:fine`)
— une fenêtre étroite sur ordinateur en profite donc aussi bien qu'un grand
écran.

Deux précautions au toucher : la barre porte `touch-action:pan-x`, donc seul le
glissement gauche-droite l'atteint — sans quoi la moindre composante verticale
du geste faisait défiler la page en même temps et la barre semblait monter et
descendre sous le doigt. Et le recentrage écrit `scrollLeft` à la main plutôt que
d'appeler `scrollIntoView`, qui fait défiler *tous* les ancêtres défilables :
sous un en-tête fixe, le navigateur croit l'onglet caché et provoque un saut
vertical.

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

Le vert vaut pour **tout ce qui est un multiplicateur**, où qu'il apparaisse : la
tuile, le bonus des succès en tête de leur onglet, le bonus d'antimatière du
panneau de cycle, et les pastilles de bonus temporaire sous la planète — celles-ci
étaient dorées alors qu'elles n'ont rien à voir avec du minerai. En revanche les
propriétés de l'antimatière elles-mêmes (le « +17 % par unité », l'exposant)
restent violettes : elles décrivent la ressource, pas son résultat.

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
const VERSION="2.21.1";
```

Il s'affiche à côté du titre sur ordinateur, et dans une tuile de l'onglet
**Statistiques** sur toutes les tailles d'écran. C'est le moyen le plus simple de
vérifier quelle version est réellement servie, le service worker pouvant garder
une page en cache.

Aucune date de compilation n'y figure : une date écrite à la main finit toujours
par mentir, et le numéro de version suffit à identifier une livraison.

**Règle d'incrémentation** (`MAJEUR.MINEUR.CORRECTIF`) :

| Partie | Quand l'incrémenter | Exemples |
|---|---|---|
| **MAJEUR** | refonte visible ou changement des règles du jeu | passage à l'en-tête collant, nouvelle monnaie |
| **MINEUR** | nouvelle fonctionnalité, nouveau contenu, équilibrage | nouvelle anomalie, nouveaux succès, probabilités modifiées |
| **CORRECTIF** | correction de bug, retouche de texte ou de mise en page | libellé raccourci, débordement corrigé |

Une même livraison ne fait avancer qu'un seul niveau — le plus élevé concerné —
et remet à zéro ceux de droite : après `2.0.0`, une correction donne `2.0.1`,
un nouveau contenu `2.1.0`.

L'historique complet est dans [`CHANGELOG.md`](CHANGELOG.md), avec une note de
version par livraison.

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
| Gain de prestige | `amGain()`, `AM_SEUIL`, `AM_EXPG` |
| Automatisations, prix, cadences | tableau `AUTOS`, `CONTRE_S`, `SONDE_S` |
| Paliers du plafond de dépense | tableau `PARTS` |
| Bonus par antimatière | `amBonus()`, `amMult()`, `AM_EXP` |
| Hors-ligne | `offlineCap()`, `offlineRate()`, `GRACE` |
| Textes des deux langues | objet `T` |
| Numéro de version | constante `VERSION` |
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
