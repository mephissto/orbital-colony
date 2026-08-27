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
