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

## 3.7.25 — La barre dit ce que disent les nombres

Signalé sur « 1 M/s de production » : le texte lit `124K / 1.00M`, soit 12 %, et
la barre est aux trois quarts pleine.

Ce n'était pas un défaut de calcul. Dix-huit succès sur quatre-vingt-huit —
minerai, production, puissance de clic, antimatière des percées — montent de
mille en mille, et leur barre était **logarithmique**, comptée depuis le palier
précédent : de 1 K/s à 124 K/s, on a multiplié par 124 sur les 1000 nécessaires,
donc 70 %. Elle avançait régulièrement au lieu de rester à plat presque tout le
palier.

Le texte, lui, restait en valeurs brutes. Deux dessins de la même chose qui se
contredisent à l'écran : c'est le dessin que le joueur ne peut pas vérifier qui
a tort. `achProg()` rend désormais `v / n`, partout, sans exception. Les paliers
précédents (`n0`) et la fonction qui les calculait disparaissent — plus personne
ne s'en servait.

| Production | Barre avant | Barre après |
|---:|---:|---:|
| 1 K/s | 0 % | 0,1 % |
| 124 K/s | 69,8 % | **12,4 %** |
| 500 K/s | 89,9 % | **50 %** |
| 1 M/s | 100 % | 100 % |

Contrepartie assumée : sur ces dix-huit succès la barre bouge peu longtemps,
puis se remplit sur la dernière décade. Elle ne ment plus.

---

## 3.7.24 — La puce à la largeur d'une carte

Une puce du bandeau et une carte de l'onglet Succès montrent le même succès :
elles doivent faire la même largeur. La 3.6.0 avait corrigé la puce seule qui
s'étirait sur toute la ligne, mais avec une **constante** : `flex:0 1 250px`.
250 px ne vaut une colonne de la grille que vers 530 px de fenêtre. Sur un
téléphone de 440 px la colonne fait 205,5 px — la puce dépassait la carte d'un
cinquième.

La base de la puce est maintenant `var(--pchipW)`, posée par `syncChipW()` à
chaque changement de largeur. On ne mesure pas une carte : l'onglet Succès est
le plus souvent caché et une carte cachée mesure zéro. On refait le calcul de
la grille — `repeat(auto-fill, minmax(180px,1fr))`, gouttière de 9 px — sur la
largeur utile de `#panels`, qui contient cette grille et reste toujours
affiché.

| Fenêtre | Colonne | Puce avant | Puce après |
|---:|---:|---:|---:|
| 320 px | 300 | 250 | **300** |
| 390 px | 180,5 | 250 | **180,5** |
| 440 px | 205,5 | 250 | **205,5** |
| 529 px | 250 | 250 | **250** |
| 1280 px | 217,3 | 250 | **217,3** |

À trois puces elles rétrécissent comme avant : la base a changé, pas la règle.

---

## 3.7.23 — Onze pixels

`--edgeTop` est fixé à **11 px**, après quatre essais sur l'appareil.

| Valeur | En-tête | Haut du logo | Haut des boutons | Verdict |
|---:|---:|---:|---:|---|
| 24 px | 77 px | 43,5 | 32 | dégage, trop épais |
| **11 px** | **64 px** | **30,5** | **20** | **retenu** |
| 8 px | 61 px | 27,5 | 17 | acceptable |
| 5 px | 58 px | 24,5 | 14 | trop juste |

11 px est la plus petite valeur qui laisse le logo **et** le haut des boutons
hors de la bande — ce qui n'est vrai d'aucune valeur plus basse.

La mesure ne permettait pas de trancher seule : elle donnait un plancher de
20 px, pas la fin de la bande, la colonne d'échantillonnage tombant sur du texte
au-delà. C'est l'œil sur l'appareil qui a décidé.

---

## 3.7.21 — Cinq pixels suffisent

`--edgeTop` descend de 11 à **5 px**.

Ce qui contraint la valeur, c'est le **texte**, pas la boîte de l'en-tête. Le
logo n'est pas collé au bord : il est centré au milieu d'une rangée de boutons
plus hauts que lui, donc il descend déjà d'une dizaine de pixels tout seul.

| `--edgeTop` | En-tête | Haut du logo | Haut des boutons |
|---:|---:|---:|---:|
| 11 px | 64 px | 30,5 | 20 |
| 8 px | 61 px | 27,5 | 17 |
| **5 px** | **58 px** | **24,5** | **14** |
| 0 px | 53 px | 19,5 ✗ | 9 |

La bande du système cesse d'être mesurable vers 20 px. À 5 px de décalage le
texte est au-delà ; le haut des boutons Langue et Réglages, lui, y entre. Un bord
arrondi légèrement adouci ne se voit pas, du texte oui — c'est l'arbitrage
assumé.

L'en-tête ne fait plus que **58 px**, contre 53 hors application installée et 77
au premier jet. Si un flou réapparaissait sur le logo, 11 px a été vérifié bon
sur l'appareil.

---

## 3.7.20 — Au plus juste

`--edgeTop` passe de 24 à **11 px**, le strict nécessaire.

Le logo commence à 9 px du haut de l'en-tête, et la bande du système est mesurée
à **au moins** 20 px du bord de la vue : 9 + 11 tombe donc pile dessus, aux deux
ou trois pixels près que les caractères laissent en haut de leur boîte.

**Ce 20 px est un plancher, pas la fin de la bande.** Le profil montrait encore
4,5 d'assombrissement à 17 px, puis la colonne d'échantillonnage est tombée sur
du texte ; et un rapport indépendant parle d'« environ 35 points ». 24 px reste
la seule valeur dont on sache qu'elle dégage le texte sur l'appareil.

C'est donc un essai délibéré du minimum théorique. Si le flou revient, la
fourchette à reprendre est 16 à 24 px. L'en-tête passe de 77 à **64 px**, contre 53 hors application
installée.

---

## 3.7.19 — La bande se fond dans la barre

La 3.7.18 dégageait 24 px en haut de l'en-tête pour sortir le texte du flou.
Ça marchait, et ça se voyait : un en-tête devenu haut, avec un vide au-dessus du
logo.

Le dégradé part maintenant de la couleur de fond — exactement celle dont iOS
peint la barre d'état — et rejoint sa teinte normale au bas de ces 24 px :

```css
html.edgeband header{
  background:linear-gradient(180deg,var(--bg) 0,
    rgba(10,16,32,.96) var(--edgeTop,0px), rgba(10,16,32,.68) 100%)}
```

Les deux zones se raccordent sans couture. L'ensemble se lit comme une barre
d'état un peu plus haute, et l'en-tête semble commencer là où son contenu
commence. La bande du système tombe sur la partie la plus sombre du dégradé,
là où il n'y a rien à assombrir.

La classe `edgeband` est posée par `syncEdge()`, en même temps que `--edgeTop` :
hors application installée sur iOS, la règle ne s'applique pas du tout.

---

## 3.7.18 — Sous la bande

Le style de barre d'état n'y change rien : en application installée sur iOS 26+,
le système peint une bande de flou sur le **bord haut de la vue web**. Les 3.7.16
et 3.7.17 s'attaquaient à la barre d'état, ce n'était pas le bon endroit.

La bande a été mesurée sur l'appareil, en comparant une colonne vide de l'en-tête
au dégradé seul :

| Distance au bord | Assombrissement |
|---:|---:|
| 0,5 px | 24 |
| 8 px | 11 |
| 17 px | 4,5 |
| 20 px | 0 |

Le logo occupait **9 à 24 px** : sa moitié haute baignait dedans. C'est
l'explication de l'asymétrie mesurée depuis le premier jour — 5 px d'étalement en
vertical contre 3 en horizontal — le haut des glyphes était brouillé, pas le bas.

Le contenu de l'en-tête est donc décalé sous la bande, qui tombe alors sur du
dégradé lisse où il n'y a rien à brouiller :

```css
header{padding-top:calc(9px + var(--satTop,env(safe-area-inset-top))
                            + var(--edgeTop,0px))}
```

`--edgeTop` vaut 24 px — quatre de plus que les 20 mesurés, la mesure venant d'un
seul appareil — et **uniquement en application installée sur iOS**. L'en-tête y
passe de 53 à 77 px ; partout ailleurs, rien ne bouge.

La sonde d'un pixel de la 3.7.17 est retirée : la barre d'état se peint avec le
fond du `body`, pas avec elle.

---

## 3.7.17 — Le flou du haut, deuxième prise

Trois corrections sur la 3.7.16.

**La valeur.** `black` ne suffit pas ; les projets qui ont réglé ce même problème
posent tous `default`.

```html
<meta name="apple-mobile-web-app-status-bar-style" content="default">
```

**La couleur.** iOS 27 **ignore `<meta theme-color>`** pour la barre d'état. Il
lit le `background-color` d'un *élément réel* placé en haut de la page — la
propriété, pas les pixels rendus. D'où une bande d'un pixel, invisible à la mise
en page :

```html
<div id="sbsample"></div>
```
```css
#sbsample{position:fixed;top:0;left:0;right:0;height:1px;z-index:0;
  pointer-events:none;background-color:#090f1f}
```

C'est elle qui empêche la barre de se peindre en noir et de se détacher au-dessus
de l'en-tête, comme en 3.7.9 et 3.7.10. `theme-color` reste pour Android et les
iOS antérieurs.

**L'installation.** iOS ne lit ce réglage qu'**à l'installation**. Une icône déjà
posée sur l'écran d'accueil garde l'ancien — ce qui explique sans doute pourquoi
les versions précédentes semblaient sans effet. Il faut supprimer l'icône et la
reposer.

Sources : [holy-grails #191](https://github.com/shawnhiatt/holy-grails/pull/191) ·
[vcsudoku #38](https://github.com/tmshv/vcsudoku/pull/38) ·
[fin-app #411](https://github.com/MrClit/fin-app/issues/411)

---

## 3.7.16 — Le flou du haut, expliqué

Ce n'était pas un bug du jeu. Depuis **iOS 26**, le système applique son *scroll
edge effect* à toute application installée dont la page remonte sous la barre
d'état : un flou qui descend d'environ **35 points sous la barre**, donc par
dessus l'en-tête. iOS 27 l'a accentué, et plusieurs projets rapportent le même
symptôme au même endroit.

Il est composé par le système **au-dessus du fond déjà peint**. Aucune règle CSS
ne l'enlève — d'où l'échec du fond opaque (3.7.12) puis de la bande d'absorption
(3.7.14). Le seul remède connu est de ne plus remonter là-dessous :

```html
<meta name="apple-mobile-web-app-status-bar-style" content="black">
```

### Ce qui manquait aux tentatives précédentes

Les 3.7.9 et 3.7.10 posaient déjà `black`, et laissaient une **bande sombre** en
haut. La cause : avec une barre opaque, iOS la peint avec `theme-color`, qui
valait le fond de page — `#05070f` — quand l'en-tête commence à `#090f1f`.

```html
<meta name="theme-color" content="#090f1f">
```

La couleur est **mesurée sur le rendu**, pas calculée : le dégradé translucide de
l'en-tête se compose sur le fond de page *et* sur le champ d'étoiles.

`viewport-fit=cover` reste — la marge sûre du bas en dépend — ainsi que
`hauteurUtile()`, qui ne se déclenchera simplement plus : avec une barre opaque,
la fenêtre annoncée est déjà la bonne.

La bande `#satband` de la 3.7.14 est retirée, son hypothèse étant infirmée.

Sources : [fin-app #411](https://github.com/MrClit/fin-app/issues/411) ·
[meshmonitor #5286](https://github.com/Yeraze/meshmonitor/issues/5286)

---

## 3.7.15 — Le manifeste n'était jamais relu

Renommer l'application en 3.7.14 n'a rien changé sur l'écran d'accueil. Le
service worker servait `manifest.webmanifest` **depuis le cache** :

```js
const isDoc = req.mode === "navigate" || url.pathname.endsWith(".html");
if (isDoc) { /* réseau d'abord */ }
/* tout le reste : cache d'abord */
```

Le manifeste tombait dans « tout le reste ». Il dormait dans
`colonie-orbitale-v2` depuis la première installation, et le fichier avait beau
changer sur le serveur, le navigateur ne le relisait jamais.

Il rejoint donc le document HTML côté réseau, et le cache passe en `v3` pour que
l'ancien soit jeté. Les icônes restent au cache : quand elles changent, leur nom
de fichier change aussi, donc la question ne se pose pas pour elles.

**À faire côté appareil** : iOS fige le nom de l'application à l'installation. Il
faut déployer, ouvrir le jeu une fois pour que le service worker se mette à jour,
puis supprimer l'icône de l'écran d'accueil et la reposer.

---

## 3.7.14 — Une bande pour encaisser

Le flou de l'en-tête sur iOS 26+ ne vient pas du jeu : **Chromium ne le
reproduit pas** à densité d'écran égale, alors que l'appareil montre 5 px
d'étalement vertical sur l'en-tête contre 2 px partout ailleurs. C'est le
système, et il ne touche qu'une chose — le seul élément qui remonte sous la
barre d'état.

L'en-tête n'y monte donc plus. Une bande sans contenu occupe la marge sûre à sa
place :

```html
<div id="satband"></div>
<header>…</header>
```
```css
#satband{height:var(--satTop, env(safe-area-inset-top));
         background:rgba(10,16,32,.96)}
header  {padding-top:9px}
```

La bande porte la couleur de départ du dégradé : la jointure est invisible, le
dégradé continue simplement en dessous. Hauteur nulle sur un appareil sans
encoche, donc rien ne bouge ailleurs.

`syncHeaderH()` mesure désormais le **bas** de l'en-tête et non sa hauteur —
`#hero` doit se poser sous l'ensemble bande + en-tête — et `syncSat()` le
rappelle, la hauteur de la bande dépendant de `--satTop`.

### Le nom sur l'écran d'accueil

« Colony » devient **« Orbital Colony »** (`short_name` du manifeste et
`apple-mobile-web-app-title`). iOS fige ce nom à l'installation : il faut
supprimer l'icône et la reposer pour le voir changer.

---

## 3.7.13 — Retour en arrière

La 3.7.12 rendait le fond de l'en-tête opaque, sur l'hypothèse que le flou
observé sur iOS 27 venait du matériau « Liquid Glass » du système. **Sans effet
sur l'appareil.** L'hypothèse était fausse, et elle coûtait les quelques étoiles
qui transparaissaient derrière l'en-tête : annulée.

Ce que la mesure dit vraiment, en séparant les deux axes — un flou ordinaire
étale autant dans les deux sens :

| | horizontal | vertical |
|---|---:|---:|
| Barre d'état iOS | 2 | 2 |
| **En-tête, le logo** | **3** | **5** |
| **En-tête, le bouton FR** | **3** | **4** |
| Tuile « 23 » | 2 | 2 |
| Onglet « Extraction » | 2 | 2 |

L'étalement est nettement vertical. L'explication naturelle — du texte posé sur
une fraction de pixel — a été testée : rendu du même texte à 60, 60,17, 60,5 et
60,83 px de décalage, aucune différence, le moteur aligne les glyphes sur la
grille de l'écran. **Cause non déterminée.**

Les marges sûres sont malgré tout arrondies avant usage (`--satTop` et ses
voisines) : l'en-tête était le seul élément à recevoir `env()` brut, tout le
reste étant placé à partir de `--headerH`, que `syncHeaderH()` arrondit déjà.
C'est de l'hygiène, pas un correctif.

---

## 3.7.11 — Ni bande en haut, ni bande en bas

Supprimer la bande du bas avait fait apparaître une bande **noire en haut** : en
renonçant à `black-translucent`, iOS peignait lui-même la barre d'état en noir
opaque au lieu de laisser le dégradé de l'en-tête la recouvrir.

Le translucide est donc rétabli, et le rattrapage de hauteur s'occupe du revers —
dans ce mode iOS annonce une hauteur qui **exclut** la barre : 894 px pour un
écran de 956.

```js
function hauteurUtile(){
  const h=window.innerHeight;
  if(!installée())return h;
  const manque=screen.height-h, top=satTop();
  if(top>0 && manque>0 && manque<=top+4)return screen.height;
  return h;
}
```

Le principe tient en une phrase : **dans une application installée, la fenêtre
est l'écran**. Si iOS en annonce moins et qu'il manque au plus la hauteur de la
barre, on croit l'écran. Chaque condition écarte un cas où ce serait faux — pas
installée (le navigateur a sa barre d'adresse), marge haute nulle (barre opaque,
rien n'est caché), rien ne manque (déjà juste), il manque bien plus (iPad en
écran partagé, fenêtre redimensionnée : on ne devine pas).

Les 4 px absorbent les arrondis. La 3.7.10 exigeait l'égalité au pixel près, ce
qui aurait laissé la bande revenir pour trois pixels d'écart.

`t_haut` rejoue huit situations : les trois variantes de l'écart, barre opaque,
Android, navigateur, iPad en Split View, paysage.

---

## 3.7.6 — Le résumé couvre toute la mise à jour

La **3.5.0** était encore en ligne. Ceux qui reviennent sautent donc huit
versions d'un coup, et le panneau **Nouveautés** ne parlait que du dernier
correctif — un z-index sur une épingle. Il résume maintenant l'écart complet :
les Percées, le seuil de relance calé sur l'antimatière en poche, les succès
épinglés, et les dix-huit succès ajoutés (78 → 96).

Le détail version par version reste juste en dessous, dans la même fenêtre.
Texte uniquement, dans les deux langues.

---

## 3.7.5 — L'épingle se laisse cliquer

Le titre de la carte se peignait **par-dessus l'épingle** et lui volait le clic.
Effet de bord de la 3.7.3, et pas celui qu'on attend.

### La règle qui manquait

Pour cesser d'atténuer la carte entière, l'atténuation est passée sur son
contenu :

```css
.ach>.an,.ach>.ad,.ach>.ap{ opacity:.38 }
```

Or **une opacité inférieure à 1 ouvre un contexte d'empilement**. Ces trois blocs
ne se peignent donc plus avec le flux normal mais *au niveau des éléments
positionnés*, départagés par l'ordre du DOM. `.an` suit `.apin` dans le
gabarit — il passait devant.

`.an{padding-right:24px}` écartait le *texte* de l'épingle, pas la boîte du
`div`, qui occupe toute la largeur de la carte. Au centre du bouton, là où on
vise, `elementFromPoint()` renvoyait `DIV.an`. Seuls les quelques pixels du
bouton dépassant au-dessus et à droite de cette boîte répondaient encore — d'où
un clic qui marchait une fois sur deux.

`z-index:2` sur `.apin`, `isolation:isolate` sur `.ach` pour que ce z-index reste
local à sa carte. Rien d'autre ne bouge.

### Le test qui manquait

Vérifier les propriétés CSS n'aurait rien vu : chaque règle était correcte
séparément. `t_perc` demande maintenant **qui reçoit réellement le pointeur**,
au centre et aux deux coins de chaque épingle visible, puis fait quatre vrais
clics à la souris et vérifie que l'état bascule à chaque fois. Repassé sur la
3.7.4, il échoue en nommant le coupable : `DIV.an`.

---

## 3.7.4 — Grise si libre, rouge si suivie

L'épingle des succès était à peu près invisible, et pour une raison bête : **les
opacités se multiplient**.

```css
.ach      { opacity:.38 }   /* un succès pas encore décroché */
.ach .apin{ opacity:.38 }   /* l'épingle, discrète */
```

38 % de 38 %, soit **14 % à l'écran**, en nuances de gris par-dessus le marché.
L'épingle allumée s'en sortait mieux (`opacity:1` × .38 = 38 %, en rouge), ce qui
explique qu'on voyait les succès suivis et pas les autres — donc pas le bouton
qui sert à les suivre.

### Ce qui change

L'atténuation porte désormais sur le **contenu** de la carte, plus sur la carte
entière :

```css
.ach>.an,.ach>.ad,.ach>.ap{ opacity:.38 }
```

L'épingle n'est plus dans le sous-arbre atténué et garde l'opacité qu'on lui
donne. Le cadre et le fond reprennent à la main ce que l'opacité faisait — leurs
alphas d'origine (.16 et .78) multipliés par .38 — donc la carte a exactement le
même rendu qu'avant.

| État | Avant | Après |
|---|---|---|
| Non suivi | 14 %, gris intégral | **100 %**, `grayscale(1) brightness(1.75)` |
| Survol | 28 % | `grayscale(.3)`, agrandie de 12 % |
| Suivi | 38 %, couleur | **100 %**, rouge, aucun filtre |

`brightness` après `grayscale` : sans lui le gris de l'émoji reste sombre sur un
fond sombre, et « gris » veut dire « invisible ». Ni fond ni cadre sur aucun des
deux états — c'est la couleur seule qui porte l'information, et on la rend
lisible en remontant l'épingle éteinte plutôt qu'en décorant l'allumée.

**Rouge partout où le succès est suivi** : dans la liste, dans la section
épinglée en tête d'onglet, et dans le bandeau au-dessus des onglets, dont la
puce passe de 75 % à pleine opacité. Le bouton gagne aussi 1 px de taille et son
`.an` 4 px de marge à droite, pour la cible tactile.

`t_perc` gagne six assertions, dont celle qui aurait attrapé le bug : l'opacité
**effective**, produit de celles de tous les ancêtres. Les deux mesures sont
séparées d'une attente — `.apin` porte une transition de 150 ms et
`getComputedStyle` rend la valeur en cours d'animation, pas celle d'arrivée.

---

## 3.7.2 — Des paliers atteignables

Les paliers de succès ajoutés en 3.7.0 montaient jusqu'à **un million de
cycles**. Ils étaient faux.

### D'où venait l'erreur

La cadence avait été estimée par `amMinerai(cycSeuil()) / perSec()` : le minerai
qu'il faut, divisé par la production. Sauf que `perSec()` est lu sur la colonie
**déjà construite** — et qu'un cycle remet `S.gens` à zéro. Le temps réel d'un
cycle, c'est la reconstruction, pas la production de pointe. L'estimation à
« plusieurs centaines de cycles par heure » sautait purement et simplement la
majeure partie du cycle.

Mesuré ensuite dans le jeu, automates allumés, sur la partie de référence :

| État | Cycles en 90 s |
|---|---:|
| Poche pleine (162,8 M), aucune Percée | **0** |
| Poche pleine, 31 Percées | **0** |
| Poche à 20 M (juste après une dépense) | 1 |
| Poche à 2 M | 1 |

Et la partie de référence elle-même en est à **131 cycles** après des semaines de
jeu.

### Le nouveau barème

| Catégorie | Paliers |
|---|---|
| ♻️ Cycles | 100 · 250 · 500 · 1 000 · **2 500** |
| ⚙️ Relancés seuls | 250 · 500 · 1 000 · **2 500** |

Toujours 96 succès, toujours neuf de plus qu'en 3.6.x. Aucun palier ne vaut cent
fois le précédent, donc les barres restent linéaires.

### Un ménage rendu nécessaire

`achMult()` compte les **clés de `S.achs`**, pas les entrées d'`ACHS` : un
identifiant retiré continuerait de donner son +1 % et le multiplicateur ne
collerait plus au « X / 96 » affiché à côté. C'est la première version à retirer
des identifiants (`a_pr10k`, `a_pr100k`, `a_pr1M`, `a_acyc10k`, `a_acyc100k`,
`a_acyc1M`, jamais parvenus jusqu'à une partie publiée), d'où `purgeAchs()` au
chargement, à côté de `purgePauses()`.

`t_cyc` gagne quatre assertions : le barème exact, aucun palier au-delà de
2 500, l'effacement des identifiants disparus et le multiplicateur qui retombe
juste.

---

## 3.7.1 — Les Percées en tête

Les Percées s'affichent **au-dessus** des recherches, juste sous le panneau de
cycle, au lieu d'être reléguées tout en bas de l'onglet.

Elles ne s'ouvrent qu'une fois les **huit recherches au maximum**. À ce
moment-là, la liste des recherches est un mur de cartes « MAX » sur lequel il n'y
a plus rien à acheter — et c'est pourtant elle qu'il fallait faire défiler en
entier pour arriver au seul étage encore vivant, celui où l'antimatière sert
encore à quelque chose. Rien n'est perdu à faire descendre les recherches : elles
ne gênent plus personne en bas.

Déplacement de blocs dans le gabarit de `#pPr`, rien d'autre. `syncList()`
travaillant en place, l'ordre du code de rendu n'a aucun effet sur le DOM ; il a
quand même été aligné sur ce qu'on voit. Tant que les Percées sont fermées,
`#percList` est de hauteur nulle et le titre du Laboratoire reste collé au
panneau de cycle, à 22 px — mesuré, pas d'espace ajouté.

`t_perc` gagne cinq assertions : l'ordre dans le DOM, l'ordre à l'écran, le
panneau de cycle toujours au-dessus, et l'absence de trou quand l'étage est
fermé.

---

## 3.7.0 — De quoi compter loin

Depuis la 3.6.2, une partie qui dépense son antimatière tourne à **plusieurs
centaines de cycles par heure**. Deux affichages n'avaient jamais été écrits pour
ça.

### Le numéro de cycle et le bonus passent à l'échelle du jeu

| | Avant | Après |
|---|---|---|
| Sous la tuile ANTIMATIÈRE | `cycle #3200000` | `cycle #3.20M` |
| Panneau Recherche | `×146785797934.3` | `×70.1M` |

Les deux interpolaient leur nombre brut. La sous-ligne de la tuile tronquait déjà
à 320 px avec trois chiffres ; à sept, elle sautait. `fmt()` couvre jusqu'à `Td`
(10⁴⁵), il n'y a pas de plafond derrière.

Le multiplicateur garde sa décimale en dessous du millier — `xfmt()` délègue à
`mfmt()` tant que `v < 1000`, sans quoi un bonus d'anomalie à ×1,5 se serait
affiché ×1. Les buffs, eux, continuent de passer par `mfmt()` seul.

### Neuf succès de plus, 96 au total

L'échelle s'arrêtait à **25 cycles** et **100 relances automatiques** — des
chiffres calibrés quand un cycle durait des centaines d'heures. Il n'y avait plus
rien à viser.

| Catégorie | Nouveaux paliers |
|---|---|
| ♾️ Cycles | 100, 1 000, 10 000, 100 000, 1 M |
| ⚙️ Automatisation | 1 000, 10 000, 100 000, 1 M relancés seuls |

Les barres de progression suivent sans rien déclarer de plus : la famille se
déduit de la fonction de mesure, et aucun palier ne vaut cent fois le précédent,
donc elles restent linéaires.

### Un test passait au vert en échouant

`t_e2e` échouait **depuis la 3.4.0** — onze assertions sur l'exclusivité
Contremaître/Ingénieur, supprimée à cette version-là. Personne ne l'a vu : le
script ne se terminait pas par `process.exit(ko?1:0)`, donc il sortait 0 quoi
qu'il arrive, et le lanceur ne lisait que le code de sortie.

Le lanceur lit désormais **aussi le log** : un `ÉCHEC` écrit suffit à faire
échouer le test, quel que soit le code de sortie. Les assertions de `t_e2e` sont
réécrites sur le comportement réel (les deux automates tournent ensemble,
interrupteurs indépendants, aucun verrou), et une douzième vérifie que les deux
ensemble achètent bien améliorations *et* structures.

À savoir : six tests (`t_upcats`, `t52`, `t7`, `t_contre3`, `t_mob24`, `t62`)
n'affirment rien — ils impriment des valeurs à lire. Ils ne peuvent échouer que
si la page lève une erreur. Le compte de 35 recouvre donc deux natures de test.

Un test de plus (`t_cyc`, 35 au total) : le formatage des deux affichages, la
bascule pile au millier, les neuf paliers et leurs barres, et une sauvegarde
d'avant la 3.7.0.

Sauvegardes compatibles dans les deux sens.

---

## 3.6.2 — Le seuil suit la poche

La 3.6.0 faisait compter au seuil de relance l'antimatière versée dans les
Percées (`S.am + S.percAm`), pour que dépenser ne raccourcisse pas les cycles par
un tour de passe-passe. Le raisonnement tenait ; la partie qui en est sortie,
non.

### Ce qui s'est passé

Une partie a enchaîné **61 niveaux** — Compression 20, Réacteur 15, Veille 14,
Cascade 12 — en versant 184,8 M d'antimatière. La poche est tombée de **162,8 M
à 4,8 M**, donc le multiplicateur avec ; le seuil, lui, n'a pas bougé d'un
pouce. Le joueur payait deux fois.

| | Poche | Seuil | Cycle |
|---|---:|---:|---:|
| Avant les Percées | 162,8 M | 16,3 M | **542 h** |
| Après 61 niveaux, règle 3.6.0 | 4,8 M | 19,0 M | **4 449 h** |
| Après 61 niveaux, règle 3.6.2 | 4,8 M | 0,48 M | **1,3 min** |

La 3.6.1 avait répondu en affichant l'arbitrage sur chaque carte et en ajoutant
un bouton de démantèlement. Deux ajouts corrects et deux ajouts de trop : le
joueur n'a pas à arbitrer une formule à la main.

### La règle

`cycSeuil()` ne lit plus que `S.am`. C'est désormais **le même nombre qui paie
les Percées et qui fixe la cible**, donc la sur-dépense se corrige d'elle-même :
la poche baisse, le seuil baisse avec, le cycle raccourcit. Il n'y a plus de
piège à signaler, donc plus rien à afficher — la 3.6.1 est annulée en entier.

`S.percAm` continue d'être tenu à jour : trois succès le lisent et l'onglet
Statistiques l'affiche. Il n'entre plus dans aucun calcul d'équilibrage.

### Ce que ça coûte, assumé

Dépenser raccourcit les cycles : garder son antimatière n'est plus une
stratégie. Mesuré sur la partie de référence, à Percées constantes, le revenu
suit `poche^−0,83` :

| Poche | Seuil | Cycle | AM / heure |
|---:|---:|---:|---:|
| 162,8 M (100 %) | 16,3 M | 542 h | 30 000 |
| 40,7 M (25 %) | 4,1 M | 45,7 h | 89 000 |
| 1,6 M (1 %) | 163 k | 8,8 min | 1 108 000 |

C'est donc le barème en **1,15 par niveau** qui porte seul la tension : vider sa
poche suppose d'acheter des niveaux de plus en plus chers, et chacun rapporte.
Le succès « 1 T d'antimatière versée » devient la ligne d'horizon à la place du
multiplicateur.

Sauvegardes compatibles dans les deux sens. Une sauvegarde 3.6.0 sur-dépensée se
débloque à l'ouverture, sans rien faire.

---

## 3.6.0 — Les Percées

Les huit recherches au maximum, l'antimatière n'avait plus qu'un débouché : un
multiplicateur qui s'applique tout seul. Mesuré sur une partie à **92,9 M
d'antimatière et 96 cycles** — un cycle de **105 heures** pour un gain de 9,29 M,
et plus une seule décision à prendre.

### Un second étage de recherche, sans plafond

Ouvert quand les huit recherches sont au maximum. Niveaux illimités, payés en
antimatière, conservés d'un cycle à l'autre, **+15 % par niveau** comme les
structures.

| Ligne | Effet par niveau | 1ᵉʳ niveau |
|---|---|---:|
| 🜛 **Compression** | gain d'antimatière **+4 %** | 1 M |
| ⚛️ **Réacteur** | production **+10 %** | 1 M |
| 🛰️ **Veille prolongée** | hors-ligne **+2 h** | 0,5 M |
| ✴️ **Cascade** | minerai des anomalies **+5 %** | 0,5 M |

Compression paraît minuscule à +4 %, mais elle agit sur le seuil de relance à la
puissance 3,33 : c'est elle qui casse le mur de fin de partie. Sur la partie de
référence, le premier passage achète 11 Compression et 9 Réacteur, dépense 41 M
sur 92,9, et fait passer le cycle de **105 h à 25,5 h** — aucun achat ne coûtant
plus de 7 % de la poche.

### Le seuil de relance compte l'antimatière versée

> **Annulé en 3.6.2** — le seuil ne lit plus que l'antimatière en poche. Ce qui
> suit décrit la règle telle qu'elle a été publiée en 3.6.0.

`cycSeuil()` lit désormais `S.am + S.percAm`. Sans ça, acheter viderait la poche,
abaisserait le seuil et raccourcirait le cycle : mesuré, **vider sa poche faisait
passer un cycle de 105 h à 4 minutes et multipliait le revenu par 125**. Dépenser
serait devenu le meilleur coup du jeu en permanence — et pas à cause des effets
achetés : *une Percée sans aucun effet aurait accéléré la partie de la même
façon.*

Le multiplicateur, lui, continue de ne lire que l'antimatière **possédée** :
acheter coûte bien de la production, comme une recherche. `S.percAm` vaut 0 sur
toute sauvegarde antérieure : **le seuil y est rigoureusement inchangé.**

### Le barème, à contre-courant de l'intuition

Trois pentes simulées. Une pente raide fait dépenser **moins**, parce qu'un
niveau cesse d'être rentable dès qu'il coûte plus de ~8 % de la poche — la
production perdue dépasse alors le bonus — et une pente raide atteint ce point
après moins de niveaux.

| Pente | Versé au 1ᵉʳ passage | Part versée à 8 mois | Cycle à 8 mois |
|---|---:|---:|---:|
| **+15 %** | **41 M** | **52 %** | **167 h** |
| +30 % | 30 M | 37 % | 624 h |
| +50 % | 21 M | 26 % | 801 h |

Sur huit mois simulés, multiplier son antimatière par 155 multipliait la durée
d'un cycle par **11 700** sans les Percées, par **4,6** avec.

### Neuf succès et l'épinglage

- 🜛 **Une dixième catégorie de succès**, 9 entrées : 1, 10, 25 et 50 niveaux,
  les quatre lignes entamées, 20 niveaux dans une seule, puis 100 M, 1 G et 1 T
  d'antimatière versée. 87 succès au total.
- 📌 **Trois succès épinglés** au maximum s'affichent dans un bandeau permanent
  **au-dessus des onglets** — au même endroit que le bandeau de défi, donc sous
  les yeux quel que soit l'onglet ouvert — et dans une section en tête de
  l'onglet Succès. Toucher une puce ouvre les Succès, toucher son épingle la
  retire, et un succès décroché se dépingle tout seul.
- 📊 **L'échelle des barres est corrigée** : elle ne se lit depuis le palier
  précédent que pour les familles exponentielles. Sur « 25 niveaux de Percées »,
  3 niveaux affichent désormais 12 % et non 0 %.
- 📊 **Deux tuiles de statistiques** : niveaux de Percées, antimatière versée.
- 🧪 **Un test de plus** (`t_perc`, 34 au total) : l'ouverture de l'étage, le
  barème, ce qui bouge et ce qui ne bouge pas à l'achat, l'effet de chaque ligne,
  l'aller-retour `amGain`/`amMinerai` Compression comprise, la conservation au
  cycle, le blocage pendant un défi, l'épinglage et une sauvegarde 3.5.0.

Aucun équilibrage existant touché.

---

## 3.5.0 — L'avancement des succès

Un succès encore à décrocher porte désormais une **barre et deux nombres** —
« 37 / 100 ». Il fallait jusqu'ici ouvrir les statistiques et rapprocher soi-même
le compteur du succès : « Roue libre : 100 cycles relancés » d'un côté, la tuile
« Cycles relancés seuls » de l'autre.

- 🎯 **Le seuil n'existe plus qu'à un endroit.** Un succès chiffré déclare sa
  **mesure** (`v`) et sa **cible** (`n`) ; le prédicat de déblocage en est
  déduit. La barre ne peut donc pas dire autre chose que la condition réelle —
  c'est exactement le défaut qui avait faussé le « il manque X » de
  l'antimatière jusqu'en 3.3.0. Les huit succès binaires gardent leur prédicat :
  il n'y a rien à doser.
- 📊 **L'avancement se lit par palier**, pas depuis zéro. Deux succès sont de la
  même famille si leur fonction `v` s'écrit pareil — rien à déclarer en plus,
  donc aucune famille ne peut être oubliée.
- 📈 **Échelle logarithmique quand un palier vaut cent fois le précédent**, donc
  pour les seules familles qui montent de mille en mille : minerai, production,
  puissance de clic. Une barre linéaire y resterait collée à zéro pendant tout
  le palier puis sauterait à plein. Ailleurs elle reste linéaire. Les deux
  nombres exacts sont écrits à côté, et c'est le texte qui fait foi.
- 🏆 **Une carte déjà décrochée n'affiche pas de barre** : elle ne dirait plus
  que « 100 % ».
- 🧪 **Un test de plus** (`t_achprog`, 33 au total) : 631 états balayés où le
  prédicat, la mesure et une barre pleine doivent toujours dire la même chose,
  les bornes 0–100 % même à ∞, et ce que la carte affiche.

Les 70 prédicats convertis ont été comparés un à un aux anciens sur 139 états,
de la partie vierge au tout-maxé, paliers et juste-en-dessous compris : **aucun
écart**. Aucun succès ne se débloque plus tôt ni plus tard qu'avant.

---

## 3.4.0 — Les deux automates ensemble

L'Ingénieur et le Contremaître étaient **exclusifs** depuis la 2.25.0 : allumer
l'un mettait l'autre en pause, et l'interrupteur du second était inerte tant que
le premier tournait. C'était la réponse à un vrai problème — ils puisent dans le
même minerai — mais elle demandait de basculer deux fois par cycle, et en fin de
cycle, quand il n'y a plus d'amélioration à acheter, elle ne servait plus à rien.

La règle devient une **priorité**, pas un partage :

- ⬆️ **Chaque seconde, l'Ingénieur passe d'abord** et achète l'amélioration la
  moins chère payable.
- 🏗️ **Le Contremaître achète ensuite**, avec ce qui reste — et il achète tout
  le temps, puisqu'il n'y a aucune réserve à respecter.
- ♻️ **Quand il ne reste plus d'amélioration**, l'Ingénieur ne fait rien et tout
  va aux structures. C'est le cas de la plus grande partie d'un cycle avancé.

Ce n'est pas une règle écrite quelque part : c'est **l'ordre des deux blocs dans
`runAutos()`**. Le premier prélève, le second dépense le solde.

### Pourquoi ça n'affame pas l'Ingénieur

Le prix d'une structure monte de **15 % à chaque exemplaire acheté**. Le
Contremaître relève donc son propre plancher à chaque achat, jusqu'à ce que le
minerai dépasse l'amélioration en attente. Le retard est borné, jamais définitif.

Mesuré sur 30 minutes simulées, depuis un cycle 5 reconstruit 10 minutes à la
main (145 structures, 5 710 /s, 19 améliorations disponibles) :

| Règle | Améliorations | Structures | Production finale |
|---|---:|---:|---:|
| Contremaître seul | 0 | 165 | 5 722 /s |
| Ingénieur seul | 15 | 145 | 29 480 /s |
| Ingénieur puis Contremaître, à la main | 15 | 145 | 29 480 /s |
| réserver le prix exact de la prochaine amélioration | 15 | 145 | 29 480 /s |
| cagnotte alimentée à 30 % du flux | 15 | 175 | 29 970 /s |
| **priorité aux améliorations** | **16** | **185** | **35 010 /s** |

Dans le pire cas — cible bon marché, lots de 1 — la priorité donne les mêmes
15 améliorations que l'Ingénieur seul **et** 38 lots de structures, la dernière
amélioration tombant à 1 548 s au lieu de 353 s. C'est le seul coût de la règle,
et qui veut aller plus vite coupe le Contremaître.

### Ce qui disparaît

- 📖 **Les textes disent qui dépense quoi.** L'encart sous les interrupteurs
  déroule la règle en toutes lettres, chaque carte dit sa place dans l'ordre
  (« il se sert avant le Contremaître » / « avec le minerai que l'Ingénieur
  laisse »), et l'état de chaque ligne aussi (« se sert en premier » /
  « ×1 par seconde, sur le reste »).
- 🔌 **Les deux interrupteurs sont indépendants.** Plus de « mise en pause par
  l'autre », plus de curseur `not-allowed`, plus de clic supplémentaire pour
  rendre la main.
- 🧹 **Six fonctions, deux champs lus et quatre clés de traduction en moins** —
  `EXCLUS`, `autreAuto()`, `enPause()`, `verrouille()`, `txtPause()`,
  `normExclus()`, `migrerExclus()`, `S.autoPause`, `S.autoMain`, `au_pause`,
  `au_off_by`, `t_auto_excl`, `t_auto_lock`. Un automate n'a plus que deux
  états : actif, ou coupé à la main.
- 💾 **Les sauvegardes d'avant la 3.4.0 s'ouvrent normalement** :
  `purgePauses()` efface la pause d'exclusivité au chargement et l'automate
  suspendu repart tout seul. Une coupure manuelle, elle, reste une intention du
  joueur et n'est pas touchée. `S.autoPause` et `S.autoMain` restent dans l'état,
  vidés, pour que les exports antérieurs restent symétriques.
- 🧪 **`t_lock` devient `t_prio`** et `t_cases`, qui ne testait que la machine à
  états de l'exclusivité, disparaît : ses deux cas encore valables sont repris
  dans `t_prio`. 32 tests au total.

Aucune valeur d'équilibrage, aucun prix, aucun champ de sauvegarde touché.

---

## 3.3.3 — L'indicateur de défilement

Sur téléphone, un trait gris apparaissait au bord droit pendant qu'on faisait
défiler l'onglet Extraction — coupé au niveau de la rangée d'achat, coupé encore
au niveau des onglets, puis disparaissant sous le bandeau de la planète. De loin,
l'effet donnait aux deux barres l'air d'avoir un fond différent du reste.

L'indicateur appartient au **conteneur défilant**, pas au flux : il est peint
sous les descendants de ce conteneur. Or `#hero` (fixe), `#stickyhead` et
`#buyRow` (collantes) vont tous d'un bord à l'autre pour que les cartes passent
dessous — ils recouvrent donc aussi le bord droit, là où l'indicateur se dessine.
Aucun `z-index` ne le remonte au-dessus : il n'est pas dans l'arbre d'empilement.
Et l'inverse — rentrer les barres de 8 px à droite — ouvrirait une brèche par où
passeraient les cartes.

- 📱 **L'indicateur est masqué sous 880 px** (`scrollbar-width:none` et
  `::-webkit-scrollbar{display:none}` sur `main` et `body`). Sur un écran
  tactile, la position se lit à la liste, pas à un trait de 3 px.
- 🖥 **L'ordinateur ne bouge pas** : là c'est `#panels` qui défile, avec sa barre
  cyan de 9 px, et une barre classique retient sa largeur — les barres collantes
  s'arrêtent donc avant elle.

Aucune règle de jeu, aucun équilibrage, aucun champ de sauvegarde touché.

---

## 3.3.2 — Le compte des structures

Deux succès se jouent sur le **total** des structures — *Mille pièces* (1 000) et
*Mégastructure* (2 500) — et ce total n'était écrit nulle part. Il fallait
additionner dix cartes à la main pour savoir où on en était.

- 🏗️ **La rangée d'achat le porte à son extrémité droite**, toutes catégories
  confondues. La somme est lue dans `S.gens` et non sur les cartes affichées : le
  compte suit l'état de la partie, pas ce que la liste montre à cet instant.
- 🔢 **Écrit avec ses séparateurs de milliers**, pas via `fmt()` : « 1 002 » et
  non « 1.00K », puisque le seuil se joue à l'unité près.
- 📱 **Sous 560 px la rangée devient insécable.** Le libellé se raccourcit en
  « Par », le mot *structures* tombe, les boutons se resserrent — et si la place
  manque encore, c'est le libellé qui est rogné : les quatre boutons le rendent
  devinable, alors qu'une deuxième ligne coûterait 21 px de hauteur à chaque
  défilement, sur une barre qui reste collée en haut de l'onglet.
- 🧪 **Un test de plus** (`t_buyrow`, 33 au total) : la somme, le changement de
  langue, le passage à chaud d'une largeur à l'autre, l'indifférence au défi
  *Colonie naine*, et une seule ligne à 1200, 880, 560, 414, 390, 360 et 320 px.

Aucune règle de jeu, aucun équilibrage, aucun champ de sauvegarde touché.

---

## 3.3.1 — Un pourboire, et un compteur réparé

### Le « il manque X » de l'antimatière était faux

Pour qui a terminé **Fuite de confinement**, la tuile d'antimatière annonçait un
minerai restant sans rapport avec la réalité : « −2,41 Qi » sur une partie
avancée alors qu'il en manquait 12,4 Qa, pendant que le gain, lui, montait de
deux ou trois points par seconde. Le joueur voyait donc un compteur grimper à
vue d'œil sous un chiffre qui prétendait l'inverse.

La récompense de ce défi (×1,15 sur le gain) n'était appliquée que dans un
sens : `amGain()` la comptait, son inverse `amMinerai()` ne la défaisait pas. Le
jeu répondait donc à la question « combien de minerai pour l'antimatière
suivante ? » par le minerai d'un gain **1,15 fois plus grand**. L'écart n'est
pas de 15 % : l'exposant du gain étant 0,30, il vaut `1,15^(1/0,30)`, soit
**×4,9** sur le seuil — et jusqu'à un facteur 80 sur ce qu'il reste à extraire,
puisque c'est une différence entre deux grands nombres.

- 🧮 **Le coefficient vit désormais dans une seule fonction**, `amCoef()`,
  appelée des deux côtés. Il ne peut plus être oublié d'un côté.
- 🧪 **Un test vérifie l'aller-retour** : pour onze gains entre 1 et 999 999,
  avec et sans la récompense, le minerai renvoyé redonne exactement le gain
  demandé, et un millionième en dessous redonne exactement le gain précédent.

Le **gain lui-même n'a jamais été faux** : `amGain()` était juste, et
l'antimatière effectivement créditée aussi. Rien à rattraper sur les parties en
cours, c'était un défaut d'affichage.

### Un pourboire, pas un don

Le vocabulaire du **don** a été remplacé partout par celui du **pourboire**. Ce
n'est pas une nuance de style : un don appelle une cause et une collecte, un
pourboire remercie un travail déjà livré. Le second décrit exactement ce qui se
passe ici, et c'est aussi ce que demandent les conditions d'utilisation des
processeurs de paiement.

- ☕ **« Soutenir le jeu » devient « Laisser un pourboire ».** L'explication sous
  le libellé passe de « gratuit, sans publicité, sans compte » à « si le jeu t'a
  plu — il reste gratuit dans tous les cas » : la gratuité est rappelée, mais
  cette fois comme la raison pour laquelle il n'y a rien à acheter.
- 🚫 **Aucune contrepartie n'est promise, et aucune ne l'était.** Rien ne se
  débloque, rien n'est réservé, rien n'est vendu. C'était déjà vrai ; c'est
  maintenant écrit.
- 📄 **La page Ko-fi est reformulée dans le même esprit** — la présentation, le
  message de remerciement et le billet épinglé ne parlent plus de « payer le
  domaine et l'hébergement ». Une affectation annoncée, c'est une collecte ;
  un pourboire n'a pas d'affectation.

Aucune règle de jeu, aucun équilibrage, aucun champ de sauvegarde touché.

---

## 3.3.0 — Le clic, revu par le bas

Le clic décollait trop tard. Entre la première minute et l'achat du Condensateur
— soit une bonne partie du début de partie — il restait sous 0,2 seconde de
production, pendant que la production passive, elle, montait. Cette version
déplace la puissance vers le début **sans toucher au plafond de fin de partie**,
mesuré identique au millième près.

- 📈 **L'écho de base passe de 7 à 10 %.** C'est lui qui donne au clic une valeur
  avant tout achat de résonateur.
- 📡 **Les résonateurs passent de 9/12/15 % à 12/13,5/15 %.** Ce n'était pas
  optionnel : le jeu retient le **maximum** entre l'écho et le meilleur
  résonateur, donc un écho à 10 % aurait rendu le Résonateur v1 (9 %) strictement
  inutile. Le v3 reste à 15 %, c'est lui qui fixe le plafond.
- 🔨 **Les multiplicateurs de clic sont resserrés**, à cumul constant :
  1,45 / 1,52 / 1,58 / 1,64 au lieu de 1,4 / 1,5 / 1,6 / 1,7, toujours **×5,71**
  au total. À produit constant, monter la première oblige à baisser la dernière ;
  la moyenne géométrique des quatre, **1,546**, est la valeur maximale que peut
  prendre le Marteau ionique sans devenir plus fort que le Champ magnétique.
- 💰 **Le Marteau ionique coûte 250 au lieu de 400, l'Exosquelette 18 000 au lieu
  de 35 000.** C'est le seul levier sans contrepartie : le joueur atteint les
  mêmes multiplicateurs plus tôt, sans qu'aucune valeur d'équilibre ne bouge.

Mesuré sur le jeu, en secondes de production par clic :

| Étape | 3.2.3 | 3.3.0 | |
|---|---|---|---|
| départ, aucune amélioration | 0,075 | **0,105** | +40 % |
| Marteau ionique | 0,105 | **0,152** | +45 % |
| + Résonateur v1 | 0,133 | **0,181** | +36 % |
| + Exosquelette | 0,199 | **0,275** | +38 % |
| + Condensateur, v2 | 0,420 | **0,488** | +16 % |
| + Champ magnétique, v2 | 0,714 | **0,800** | +12 % |
| + Résonateur v3 | 0,885 | 0,885 | — |
| servo 12/12 | 2,229 | 2,229 | — |

La règle d'équilibrage est respectée : un clic ne dépasse jamais une seconde de
production, sauf par les douze niveaux de Bras servo, dépassement assumé depuis
la 2.32.0.

Aucun champ de sauvegarde touché.

---

## 3.2.3 — Le menu au cordeau

- ☕ **« Soutenir le jeu » devient une ligne comme les autres** : icône, titre,
  explication en dessous. La mention « gratuit, sans publicité, sans compte »
  quitte la même ligne que le titre pour prendre la place qu'elle a partout
  ailleurs dans le menu, sans tiret devant. La ligne garde son violet — celui
  de l'antimatière — mais à peine posé : 7 % d'opacité, 16 % au survol.
- 📐 **Les icônes sont centrées dans une boîte carrée.** Un `text-align:center`
  ne suffisait pas : les émojis (💾 🗑️ 🎓) et les symboles texte (⇅) n'ont ni la
  même chasse ni les mêmes approches, et la colonne partait en biais d'une
  ligne à l'autre. Le chevron reçoit le même traitement.
- ↗ **Les liens qui quittent le jeu le disent.** *Kit de marque*, *Code source*
  et *GPL 3.0* du pied de page portent une flèche sortante, et « Soutenir le
  jeu » remplace son chevron par la même. Le pointillé reste sous le libellé
  seul : passer sous la flèche la faisait ressembler à un caractère du texte.
- 🎨 **Un lien vers le kit de marque** ouvre le pied de page, avant le code
  source : [brand.orbital-colony.app](https://brand.orbital-colony.app/) — le
  logo, la palette et les fonds d'écran du jeu. Sur petit écran il se raccourcit
  en « Marque ».
- 🌍 **La détection de langue suit toute la liste des préférences.** Elle ne
  regardait que `navigator.language`, c'est-à-dire le premier choix : un
  navigateur réglé sur `nl-BE` puis `fr-BE` recevait l'anglais alors que le
  français était disponible et voulu. Elle parcourt maintenant
  `navigator.languages` dans l'ordre et retient la première langue connue du
  jeu. Au passage, `fry` (frison) ne passe plus pour du français.

Aucun champ de sauvegarde touché.

---

## 3.2.2 — Ko-fi remplace Patreon

- ☕ **Le bouton « Soutenir le jeu » mène désormais à Ko-fi.** Patreon prenait
  10 % de commission, imposait un abonnement mensuel, et exigeait du visiteur
  qu'il se crée un compte. Ko-fi ne prend **rien** sur un don ponctuel — seuls
  restent les frais du processeur de paiement — et l'argent arrive directement
  sur le compte de l'auteur au lieu de transiter par la plateforme.
- 🔗 La constante `LIEN_PATREON` devient `LIEN_KOFI`. C'est le seul lien sortant
  du menu à avoir changé ; le code source, la licence et le GitHub de l'auteur
  sont inchangés.
- Le bouton garde sa forme, sa couleur violette et sa mention « gratuit, sans
  publicité, sans compte » : rien d'autre ne bouge à l'écran.

Aucun champ de sauvegarde touché.

---

## 3.2.1 — Le nom de l'auteur est cliquable

- 🔗 Dans le pied de page du menu, **« mephissto » mène à son GitHub**. Le nom
  y était affiché en gras depuis la 3.2.0 sans être un lien — ce qui ressemblait
  à un oubli, et en était un.

Aucun champ de sauvegarde touché.

---

## 3.2.0 — Le menu passe en liste

- 📋 **Une entrée par ligne, pleine largeur**, avec une explication sous chacune :
  « le jeu le fait déjà tout seul, toutes les 20 s » sous *Sauvegarder*, « un code
  à copier pour changer d'appareil » sous *Export / Import*, « efface la partie,
  définitivement » sous *Reset*. La grille à deux colonnes offrait une cible
  tactile deux fois plus petite et aucune place pour expliquer.
- 💜 **Un bouton « Soutenir le jeu »**, avec la mention « gratuit, sans publicité,
  sans compte » qui justifie sa présence.
- ℹ️ **La fenêtre « À propos » disparaît.** Elle ne contenait plus que quatre
  lignes : l'auteur et la version tiennent maintenant sur une ligne de pied de
  page, le code source et la licence juste en dessous. Une fenêtre de moins.
- 🎯 Deux groupes au lieu de trois : **Ta partie** et **Le jeu**.

Aucun champ de sauvegarde touché.

---

## 3.1.1 — Le menu s'ouvre au doigt

- 🐛 **Sur téléphone, la roue crantée n'ouvrait rien.** Après un appui, le
  navigateur synthétise un `click` et le place sur l'élément qui se trouve alors
  sous le doigt — c'est-à-dire, la fenêtre venant de s'ouvrir, sur son fond. Le
  « clic à côté pour fermer » (3.0.0-dev.17) le prenait pour un vrai clic et
  refermait la fenêtre dans la foulée. **Menu, changelog, à propos, export et
  tutoriel étaient tous touchés.**
- Fermer au clic à côté exige désormais que **l'appui ET le relâchement** aient
  eu lieu sur le fond. Effet de bord bienvenu : glisser depuis l'intérieur d'une
  fenêtre et relâcher sur le fond ne la ferme plus.
- À la souris le bug n'existait pas — `bindAction` y agit sur le `pointerdown`,
  et la cible d'un `click` est l'ancêtre commun du mousedown et du mouseup, donc
  jamais le fond. Toute la suite de tests pilotait ces fenêtres à la souris :
  elle est désormais doublée d'un test **au doigt**.

Aucun champ de sauvegarde touché.

---

## 3.1.0 — Les succès des défis

- 🎯 **Sept succès de plus**, dans une **nouvelle catégorie « Défis »** : un par
  défi réussi (*Vide sidéral*, *Sans les mains*, *Krach évité*, *Petit mais
  costaud*, *Colmatage*, *Ex nihilo*) et un dernier pour les six, *Briseur de
  règles*.
- Ils sont **construits à partir de la liste des défis**, pas recopiés à côté :
  ajouter un défi ajoutera son succès, et les deux listes ne peuvent pas se
  désynchroniser.
- Le bonus des succès passe donc de **+71 %** à **+78 %** de production au
  complet.

Aucun champ de sauvegarde touché : les succès des défis se déduisent de
`chalDone`, qui existe depuis la 3.0.28.

---

## 3.0.28 — Les défis

Première des deux grandes étapes prévues pour allonger la partie. Six **défis**
cassent chacun une règle du jeu pour un cycle entier, avec un objectif calculé
sur ton meilleur cycle et une récompense définitive.

### Les défis

- 🎯 Nouvel onglet **Défis**, visible dès le premier cycle mais **grisé**, avec
  le décompte des cycles restants. Il s'ouvre quand la tuile de l'en-tête
  affiche « cycle n°6 en cours ».
- Entrer **encaisse d'abord le cycle en cours** et crédite l'antimatière en
  attente, puis remet minerai, structures et améliorations à zéro. Sont
  conservés : antimatière, recherches, succès, automates et les récompenses des
  défis déjà réussis.
- Bandeau permanent en haut avec la progression et un bouton **Quitter**, sans
  pénalité. Réussi une fois, un défi est validé **définitivement**.
- Pendant un défi, **seul le relanceur de cycle ♻️ est suspendu** — il effacerait
  ta progression. Tous les autres automates travaillent. Les recherches ne
  s'achètent pas, l'onglet est grisé et explique pourquoi.

| Défi | Règle cassée | Récompense permanente |
|---|---|---|
| 📵 Silence radio | aucune anomalie n'apparaît | anomalies +20 % plus fréquentes |
| ⛓️ Mains liées | le clic ne rapporte rien | production +25 % |
| 📈 Inflation | prix en ×1,35 au lieu de ×1,15 | −8 % sur tous les prix |
| 🏚️ Colonie naine | 6 structures seulement, mais elles produisent ×4 | les 4 dernières produisent ×2 |
| 💨 Fuite de confinement | production −2 % par tranche de 2 min, plancher 20 % | gain d'antimatière +15 % |
| 🕳️ Le Vide | l'antimatière ne compte plus dans le multiplicateur | exposant antimatière 1,50 → 1,55 |

Les six ont été équilibrés par **simulation seconde par seconde d'une vraie
partie**, anomalies, bonus et automates compris, chaque défi comparé à un cycle
ordinaire visant le même objectif : entre 2,4 et 3,8 h pour un joueur attentif,
soit 2,5 à 5 fois un cycle normal. Deux d'entre eux étaient **arithmétiquement
impossibles** avant cette mesure — la Fuite bornait le minerai total du cycle, et
la Colonie naine ne pouvait pas approcher un record établi avec dix structures.
Le détail est dans [ROADMAP.md](ROADMAP.md).

### Le clic compte enfin en début de partie

Un **écho de base de 7 %** apparaît avant tout résonateur : sans lui, le clic
passait de 10 s de production à **0,006 s** en une heure sur une partie neuve. Il
en vaut maintenant ~0,10 s en permanence.

Les résonateurs montent à 9/12/15 % et les multiplicateurs de clic descendent à
×1,4 / ×1,5 / ×1,6 / ×1,7 (×5,71 au total). Le plafond de fin de partie ne bouge
pas d'un pouce — 0,15 × 5,71 = **0,86 s de production**, la valeur d'avant : la
puissance du clic est simplement déplacée vers le début.

### Un seul bouton, un menu

- ⚙️ Les trois boutons du haut (Sauvegarder, Export/Import, Reset) sont regroupés
  derrière une **roue crantée**. Le menu contient aussi un **tutoriel**, un
  **changelog** embarqué et un **à propos** (code source, licence, Patreon).
- 🎓 Le **tutoriel** s'ouvre tout seul à la première partie — cinq écrans, avec
  flèches et glissement du doigt. Une partie déjà commencée ne le déclenche pas.
- 🛰️ Un **lore** ouvre le tutoriel et justifie le nom du jeu : la colonie tourne
  autour d'une planète morte, l'orbite se dégrade, et chaque colonie finit par
  retomber. « Une colonie orbitale n'est jamais finie, elle est seulement en
  train de tomber moins vite que la précédente. »
- Toutes les fenêtres se ferment au clic à côté — sauf la confirmation, qu'on
  n'annule pas d'un clic distrait.

### Interface

- 🏗️ Le **Contremaître** achète par lots de **×1, ×10 ou ×25**.
- 📌 La rangée **×1 / ×10 / ×100 / MAX** reste collée en haut pendant qu'on fait
  défiler les structures.
- 🌐 **L'anglais devient la langue par défaut** : titre de la page, nom de
  l'application installée, `lang` du document et description. Le français reste
  détecté automatiquement. Le sélecteur devient un menu déroulant.
- 📊 La tuile **Minerai** affiche `cycle · total`, la tuile **Antimatière**
  affiche le cycle en cours et ce qui manque pour l'antimatière suivante.
  L'onglet Recherche compte lui aussi le cycle **en cours** et non les cycles
  terminés.
- 🔴 Sur `dev.` le bandeau passe au rouge, avec une pastille **DEV** et un titre
  préfixé — pour ne plus confondre les deux onglets.
- 🔢 Le numéro de version reste affiché à toutes les largeurs.

### Compatibilité

Un seul champ ajouté (`tuto`), plus ceux des défis (`chal`, `chalDone`,
`chalStart`, `chalBut`, `bestRun`, `autoQte`). **Toutes les sauvegardes 2.x
restent valides** et ne déclenchent pas le tutoriel.

---

## 2.35.3 — Le Contremaître achète, sur deux lignes

- 🏗️ Dans les réglages de l'automatisation, **« Le Contremaître rachète » devient
  « achète »** — il n'y a pas de rachat, c'est un achat de plus, chaque seconde.
  Même correction sur la description de sa carte.
- ↩️ La ligne d'état passe sur **deux lignes** : le prix visé, puis « il manque…
  ». Sur petit écran la coupure tombait à un endroit variable selon le nombre
  affiché ; elle est maintenant toujours au même endroit.

Aucun champ de sauvegarde touché.

---

## 2.35.2 — Une case pour ne plus être rappelé

- ☑️ La fenêtre d'installation gagne une case **« Ne plus me le rappeler sur cet
  appareil »**. Cochée avant « Fermer », elle retire aussi la **pastille 📲** :
  plus rien ne réapparaît pour qui veut jouer dans son navigateur.
- 🧠 Le choix reste **par appareil** (`localStorage`, hors sauvegarde), qui compte
  désormais trois refus possibles : *plus tard* (pastille), *jamais* (rien), et
  *installé* (rien).
- 🔁 La case est **décochée à chaque ouverture** : rouvrir la fenêtre depuis la
  pastille ne doit pas transformer un refus temporaire en refus définitif par
  accident.
- 🏷️ Le bouton **« Plus tard » devient « Fermer »** : cocher « ne plus me le
  rappeler » puis valider par « Plus tard » n'avait plus de sens.

Aucun champ de sauvegarde touché.

---

## 2.35.1 — La mise en garde iOS est détachée

- ⚠️ Dans la fenêtre d'installation, « À faire depuis Safari : les autres
  navigateurs iOS ne le proposent pas » passe **sur sa propre ligne**, précédée
  d'un ⚠️ — c'est le piège classique, il ne devait pas se perdre à la suite du
  mode d'emploi.
- 🔧 Les libellés traduits passaient tous par `textContent` : un `<br>` s'y
  serait affiché littéralement. Nouvel attribut `data-i18n-html`, qui bascule
  `applyI18n()` vers `innerHTML` pour les seuls libellés qui portent du balisage.

Correctif d'affichage uniquement.

---

## 2.35.0 — Le jeu propose son installation sur mobile

- 📲 Une fenêtre invite à **installer le jeu en application** les joueurs sur
  mobile qui passent par le navigateur. Elle contient le mode d'emploi
  **Android** et **iPhone / iPad** côte à côte.
- 🎯 Elle n'apparaît **ni sur ordinateur, ni pour qui joue déjà en mode
  application** — détecté par `display-mode: standalone` et, pour Safari iOS qui
  ne suit pas le standard, `navigator.standalone`.
- ⚡ Sur **Chrome/Android**, l'événement `beforeinstallprompt` est capté : un
  bouton **Installer** s'ajoute et déclenche le vrai dialogue système, sans
  passer par le mode d'emploi. iOS n'expose rien d'équivalent, d'où les deux
  tutoriels affichés ensemble plutôt qu'un seul deviné sur l'user-agent.
- 🔁 Refuser laisse une **pastille 📲 en bas à droite**, qui rouvre la fenêtre à
  la demande. Le refus est mémorisé **par appareil**, dans une clé `localStorage`
  distincte de la sauvegarde : il ne suit pas une partie exportée et ne
  réapparaît pas après un import.
- 🐛 Deux corrections d'affichage trouvées en testant : les boutons des fenêtres
  modales étaient écrasés à 34×34 px sous 720 px de large — une règle destinée
  aux seules icônes de la barre d'outils, désormais limitée à celle-ci — et la
  bande des toasts réserve maintenant la largeur de la pastille pour ne pas la
  recouvrir.

Aucun champ de sauvegarde touché.

---

## 2.34.0 — Contremaître et Ingénieur échangent leur prix

- 🔄 Le **Contremaître passe de 100 à 150 antimatière**, l'**Ingénieur de 150 à
  100**. Les deux sont exclusifs, et celui qu'on garde allumé en pratique est le
  Contremaître : il rachète des structures en continu, alors que le travail de
  l'Ingénieur est **fini** une fois les 73 améliorations achetées. Le plus utile
  devait être le plus cher.
- 📋 Ils échangent aussi leur place dans la liste, pour qu'elle reste en prix
  croissant : 30 → 100 → 150 → 200 → 400.
- 💰 **Le total de l'automatisation ne bouge pas** (31 540 antimatière) : c'est un
  échange, pas une hausse.

Aucun champ de sauvegarde touché ; un automate déjà acheté le reste.

---

## 2.33.1 — Numéro de version

Aucun changement de code ni d'équilibrage. Version incrémentée pour marquer la
livraison de l'ensemble 2.31 → 2.33.

---

## 2.33.0 — Le gain d'antimatière revient à son rythme d'avant

- ⚖️ Les corrections du clic (2.30 → 2.32) avaient, **sans que ce soit le but**,
  presque doublé le minerai d'un cycle avancé — donc le gain d'antimatière :
  **69,9K par cycle contre 36,2K** à l'origine. L'exposant du gain (`AM_EXPG`)
  passe de **0,32 à 0,30** pour annuler cette inflation.
- 🎯 L'exposant plutôt que le seuil, parce qu'il **ne touche pas au premier
  prestige** — 1 antimatière dans les deux cas — et corrige d'autant plus fort
  que le cycle est gros, exactement là où l'inflation s'est produite :
  intermédiaire 83 → 63, avancé 1,58K → 999, très avancé 69,9K → 34,8K.
- 🕳️ L'enjeu réel : l'antimatière n'a qu'un débouché, les 8 recherches
  (234 890 au total). Doubler le gain, c'était **diviser par deux le temps avant
  qu'elle ne serve plus à rien**. Maxer les recherches repasse de **4 cycles à
  7** pour un joueur très avancé.
- Un joueur tout équipé retrouve le rendement d'avant la 2.30.0 (×0,96). Un
  joueur qui n'automatise pas ses clics reste environ 30 % en dessous — cas
  volontairement écarté, les Satellites étant acquis de longue date à ce stade.

Aucun champ de sauvegarde touché : l'antimatière déjà gagnée n'est pas reprise.

---

## 2.32.0 — Les Bras servo-assistés servent enfin à quelque chose

- 🤖 La recherche **Bras servo-assistés** donnait ×2 de puissance de clic par
  niveau, mais **sur la seule frappe** — une base de 1 qui ne grandit jamais.
  Mesuré : un joueur avancé relançant un cycle obtenait **exactement le même
  minerai et la même production au bout de 5 minutes avec 0 ou 12 niveaux**.
  8 675 antimatière pour rien, et c'est la recherche la plus chère après
  l'Optimisation minière et la Résonance.
- ✖️ Elle donne maintenant **+8 % par niveau sur le clic entier**, soit **×2,52**
  au maximum. Chaque niveau se sent, du premier au douzième.
- 💰 **Le barème de prix ne bouge pas d'un antimatière** (6 → 3 857, 8 675 au
  total). C'est ce qui garde la recherche cohérente avec les sept autres, dont
  les premiers niveaux coûtent tous entre 6 et 30 : une base relevée à 150,
  envisagée un moment, aurait été cinq fois plus chère que le plus cher du jeu.
- 📈 Sur un cycle de 30 min avec dix satellites : **50,1K d'antimatière** à 0/12
  (identique à avant), **59,1K** à 6/12, **69,9K** à 12/12. La recherche maxée
  rapporte donc **+39 % par cycle**, contre 0 % auparavant.
- Le clic plafonne à **2,18 s de production** par clic, atteint seulement avec
  les douze niveaux payés.

---

## 2.31.0 — Le clic ne dépasse plus jamais la production

- 📏 **Nouvelle règle d'équilibrage : un clic ne doit jamais valoir plus d'une
  seconde de production.** La 2.30.0 l'enfreignait largement — le clic montait à
  **6,40 s de production** en fin de partie.
- ✖️ Les améliorations de clic passent de ×2 partout à **×1,5 / ×1,6 / ×1,8 /
  ×2** (soit ×8,64 au complet au lieu de ×16), avec une progression qui suit
  enfin le prix.
- 📡 Les **Résonateurs** descendent de 3/12/40 % à **2/5/10 %**. Les deux vont
  ensemble : à 40 %, le clic valait déjà 0,40 s de production **avant toute
  amélioration de clic**, donc aucun multiplicateur au-dessus de ×2,5 ne pouvait
  s'ajouter sans casser la règle.
- 📊 Résultat mesuré : le clic **plafonne à 0,86 s de production** (contre 6,40
  en 2.30.0 et 0,40 à l'origine), tout en restant **2,15× plus fort qu'à
  l'origine**. Chaque amélioration garde son gain exact — ×1,50, ×1,60, ×1,80,
  ×2,00 — jusqu'en fin de partie, ce qui était tout le problème de départ.
- ⏱️ Sur un cycle simulé de 30 minutes avec dix Satellites, l'antimatière gagnée
  passe de 101K (2.30.0) à environ **50K**, contre 36K pour le comportement
  d'origine — au lieu de ×2,8, l'écart n'est plus que de ×1,4.

Aucun nouveau champ de sauvegarde.

---

## 2.30.0 — Les améliorations de clic : un seul nombre, sur tout le clic

- ✖️ Les quatre améliorations de clic valent maintenant **×2 chacune, sur la
  valeur totale du clic**, écho du résonateur compris. La carte dit « Clic ×2 »
  et ça veut dire exactement ×2, quel que soit ton avancement. Au complet :
  **×16**.
- 🧹 L'effet double de la 2.29.0 (×N sur la frappe + points de résonance) est
  abandonné : il corrigeait bien le problème de fond, mais **deux nombres sur une
  carte, dont un qui ne s'applique que sous condition, ne se lisent pas**.
- ⚖️ Rééquilibrage assumé dans les deux sens : les quatre donnent ×16 au lieu de
  ×480 en tout début de partie (clic plus faible au démarrage), et **3,04M au
  lieu de 328K** en fin de partie sur le scénario de référence.
- 💰 Les quatre donnent le même ×2 malgré des prix très différents : doubler un
  gros clic rapporte déjà bien plus en absolu que doubler un petit, c'est ce que
  le prix croissant paie.
- Les **Bras servo-assistés** restent sur la frappe seule (×4096 sur le clic
  entier serait hors d'échelle) : comme toute la frappe, cette recherche pèse de
  moins en moins à mesure que l'écho domine.

Aucun nouveau champ de sauvegarde.

---

## 2.29.0 — Les améliorations de clic renforcent aussi la résonance

- 🖱️ Les améliorations « **puissance de clic** » ne multipliaient que le premier
  terme de la formule du clic — `1 × multiplicateurs × multiplicateur global` —
  une base de **1 qui ne grandit jamais**, alors que le terme du résonateur suit
  ta production. Dès que la production brute dépasse quelques milliers/s, le
  premier terme est noyé. Mesuré en achetant le **Champ magnétique ×8** :
  **×7,33** en début de partie, **×1,31** à production moyenne avec le
  résonateur v2, et **×1,00** avec le v3 et une grosse production — pour
  2 milliards de minerai.
- ➕ Chacune ajoute maintenant des **points de résonance** en plus de son ×N :
  Marteau ionique **+2**, Exosquelette **+4**, Condensateur **+8**, Champ
  magnétique **+15**. Le résonateur v3 seul donne 40 %, v3 avec les quatre
  améliorations de clic donne **69 %**. Le Champ magnétique vaut désormais
  **×5,71 / ×1,71 / ×1,28** selon l'avancement : toujours perceptible, sans
  bouleverser l'économie.
- 🚫 Les points **n'agissent que si un résonateur est possédé** — sans lui, il
  n'y a pas de résonance à renforcer, et le ×N brut suffit largement à ce stade.
- 📝 Les cartes annoncent les deux effets (« Puissance de clic ×8, résonance
  +15 points »).

Aucun nouveau champ de sauvegarde.

---

## 2.28.0 — Les améliorations se redécouvrent à chaque cycle

- 🔎 Une amélioration apparaissait dans la liste dès que tu avais extrait 8 % de
  son prix **sur toute la partie** — un total jamais remis à zéro. Résultat : à
  partir du deuxième cycle, **toutes les améliorations non liées aux structures**
  (clic, résonateurs, production globale, balise) s'affichaient d'un bloc dès la
  première seconde, et la redécouverte progressive disparaissait définitivement.
- ♻️ Le critère porte maintenant sur le minerai extrait **sur le cycle en
  cours**. Il reste monotone à l'intérieur d'un cycle — la liste ne saute jamais
  pendant que tu joues — mais il repart de zéro au prestige, comme les
  améliorations elles-mêmes, qui sont perdues à ce moment-là.
- Les **paliers de structure** ne changent pas : ils ont en plus leur condition
  de N exemplaires possédés, donc ils réapparaissaient déjà au fil du cycle.

---

## 2.27.1 — Plus de pastille clignotante sur la carte des Satellites

- 🔕 Le petit point cyan qui pulsait sur la ligne d'état des **Satellites
  d'extraction**, dans l'onglet Automatisation, est retiré. La carte annonce
  déjà « N satellites en orbite » en toutes lettres, et les points en orbite
  autour de la planète portent la même information : une animation de plus ne
  servait qu'à distraire dans un panneau qu'on vient consulter, pas surveiller.
- Les **points en orbite autour de la planète sont inchangés**, eux continuent
  de tourner et de pulser.

---

## 2.27.0 — L'interrupteur de l'automate à l'arrêt est verrouillé

- 🔒 Tant que l'un des deux travaille, l'interrupteur de l'autre est
  **grisé et inerte** (curseur `not-allowed`) — y compris quand celui-ci avait
  été **coupé à la main** avant. Il n'était jusque-là verrouillé que s'il avait
  été suspendu : on pouvait donc croire l'avoir rallumé alors qu'il n'aurait pas
  démarré. Pour lui rendre la main, il faut d'abord couper celui qui tourne.
- 🏷️ Le libellé distingue les deux situations : « **mise en pause par
  l'Ingénieur** » quand il repartira tout seul (curseur du bouton à droite), et
  « **coupé — l'Ingénieur travaille** » quand tu l'avais coupé toi-même (curseur
  à gauche). La position du bouton dit donc ce qui se passera une fois l'autre
  arrêté.
- 💬 Un clic sur un interrupteur verrouillé affiche « Coupe d'abord l'Ingénieur
  pour libérer celui-ci » plutôt que de ne rien faire en silence.

Aucun nouveau champ de sauvegarde.

---

## 2.26.0 — Couper l'Ingénieur rend la main au Contremaître

- 🐛 **Bug corrigé** : depuis la 2.25.0, la pause d'exclusivité était stockée
  comme une **coupure manuelle**. Couper l'Ingénieur laissait donc le
  Contremaître éteint alors qu'il n'avait jamais été coupé par le joueur — juste
  suspendu — et il fallait le rallumer à la main.
- 🔁 Les deux états sont maintenant distincts : **coupé à la main**
  (`S.autoOff`, une intention du joueur, que rien ne lève automatiquement) et
  **mis en pause** (`S.autoPause`, un état dérivé qui se lève dès que l'autre
  s'arrête). Couper l'Ingénieur **rend donc la main au Contremaître** — mais ne
  ressuscite pas un Contremaître que tu avais délibérément coupé.
- 🗃️ Une sauvegarde d'avant la 2.26.0 est relue au chargement : si exactement
  l'un des deux est coupé pendant que l'autre tourne, la coupure est
  réinterprétée comme une pause.

Deux nouveaux champs purement additifs, `S.autoPause` et `S.autoMain` (lequel des
deux a pris la main en dernier). Sauvegardes antérieures compatibles.

---

## 2.25.2 — La pause dit qui l'a déclenchée

- 🏷️ Un automate suspendu n'affiche plus « en pause » mais **« mise en pause par
  l'Ingénieur »** (ou « par le Contremaître ») : la cause est nommée là où on la
  lit, sans avoir à deviner lequel des deux a pris la main.
- 📱 Sur écran étroit (< 520 px), la ligne d'état **passe sous le nom** au lieu
  de disparaître. Elle était masquée depuis la 2.24.0 pour gagner de la place —
  ce qui rendait justement ce message invisible sur téléphone.

---

## 2.25.1 — L'automate en pause reste visiblement armé

- 🎚️ L'interrupteur d'un automate **mis en pause par l'exclusivité** garde son
  curseur **à droite**, simplement **grisé**, au lieu de basculer à gauche comme
  s'il avait été coupé. Il est armé, c'est le jeu qui l'a suspendu — et on ne
  croit plus l'avoir éteint par erreur. Sa ligne est aussi moins estompée qu'une
  ligne coupée à la main.
- 📝 Les deux cartes l'expliquent maintenant dans leur description : le
  Contremaître « met l'Ingénieur en pause », l'Ingénieur « met le Contremaître
  en pause ».

Correctif d'affichage uniquement, aucun changement de comportement.

---

## 2.25.0 — Contremaître et Ingénieur deviennent exclusifs

- 🔀 Le partage automatique du minerai introduit en 2.24.0 (moitié du stock au
  Contremaître) donnait de bons chiffres mais **restait illisible en jouant** :
  on voit ses structures ralentir sans comprendre pourquoi. Les deux automates
  sont désormais **exclusifs** — **allumer l'un met l'autre en pause**,
  l'interrupteur le montre, et c'est toi qui décides lequel travaille.
- ⏸️ Une ligne coupée par l'exclusivité affiche « **en pause** » et non
  « coupé » : on distingue au premier coup d'œil ce qu'on a coupé soi-même de ce
  que le jeu a mis en attente.
- 🆕 Un automate qu'on vient de **payer démarre allumé** et prend la main sur son
  exclusif : pas de surprise du genre « je viens de l'acheter et il ne fait rien ».
- ↩️ Le Contremaître **n'a plus aucun frein** : il achète sa cible dès qu'il a
  le prix, point.
- 🗃️ Une sauvegarde d'avant la 2.25.0 où les deux tournaient ensemble est
  rattrapée au chargement : l'**Ingénieur garde la main**, son travail étant fini
  une fois les 73 améliorations achetées.

Aucun nouveau champ de sauvegarde.

---

## 2.24.0 — Contremaître et Ingénieur se partagent le minerai

- ⚖️ Depuis la 2.23.0 les deux automates se marchaient dessus : le Contremaître
  achète dès que le minerai couvre sa cible, donc le stock ne montait jamais et
  l'**Ingénieur était affamé en permanence** — plus aucune amélioration un peu
  chère ne devenait payable. Le Contremaître ne dépense désormais que **la
  moitié du stock** tant que l'Ingénieur a encore quelque chose à acheter, et la
  totalité une fois qu'il a fini. Aucun réglage à comprendre.
  Mesuré sur 30 minutes simulées en milieu de partie, cible Drone :
  **15 améliorations et 56 267/s** au lieu de 13 et 37 515/s, pour 2 structures
  de moins.
- 🚫 Le **plafond de dépense en %** disparaît de l'interface : il ne servait
  qu'à ce partage, que la règle ci-dessus fait maintenant toute seule. Le champ
  `S.autoPart` reste dans l'état pour que les sauvegardes et les exports
  antérieurs restent symétriques.
- 🎛️ Les **interrupteurs remontent dans les Réglages**, une ligne par automate
  possédé (icône, nom, état, interrupteur). La liste du bas ne sert plus qu'à
  l'achat et aux niveaux des Satellites.
- ✂️ Explications des réglages **raccourcies**.

Une piste intermédiaire a été essayée puis écartée : réserver le **prix exact**
de la prochaine amélioration bloquait le Contremaître à **zéro achat** sans rien
gagner à l'Ingénieur — le stock ne dépasse jamais durablement ce prix, puisque
l'Ingénieur l'achète dès qu'il l'atteint.

---

## 2.23.0 — Le Contremaître vise la structure de ton choix

- 🎯 Le **Contremaître** rachetait toujours la structure **la moins chère**
  payable. C'est l'inverse de ce qu'on fait à la main : les structures tardives
  rapportent beaucoup plus par minerai dépensé, et laisser l'automate empiler
  des drones revient à gaspiller la production. Il a désormais un **menu
  déroulant** dans les Réglages, listant les structures **déjà révélées** avec
  leur icône : il n'achète plus que celle-là, une par seconde.
- 💰 Il n'est **plus soumis au plafond de dépense en %**. Viser une grosse
  structure n'aurait aucun sens si l'automate n'avait pas le droit d'y mettre
  tout le minerai nécessaire. Le plafond ne concerne donc plus que
  l'**Ingénieur**, et son intitulé le dit maintenant explicitement.
- ⏳ Si la cible n'est pas payable, il **n'achète rien et attend** — pas de repli
  sur une structure moins chère, c'est tout l'intérêt du « une seule à la fois ».
  La ligne du panneau donne le prix visé, ce qu'il manque et une estimation de
  temps, et la carte de l'automate rappelle sa cible en permanence.
- ⬆️ Les **Réglages sont passés en haut de l'onglet**, avant la liste des
  automates. Une fois ceux-ci achetés, la liste ne bouge plus alors que le
  panneau est ce qu'on revient consulter : il fallait faire défiler tout
  l'onglet à chaque ajustement.
- 🔒 Le menu ne propose que les structures déjà découvertes (`S.seen`) : rien ne
  se dévoile d'avance. Tant que tu n'as rien choisi, il vise la **dernière
  structure révélée** — le meilleur défaut, et ça évite qu'un automate acheté
  tard se mette à empiler des drones.

Nouveau champ `S.autoGen`, purement additif : les sauvegardes antérieures se
chargent sans conversion et repartent sur le défaut ci-dessus.

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
