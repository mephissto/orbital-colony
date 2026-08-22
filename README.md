# Colonie Orbitale

Idle game spatial, bilingue FR / EN, installable en application (PWA) et jouable
hors connexion. Tout le jeu tient dans `index.html` : aucune dépendance, aucun
serveur, aucune donnée qui sort de ton navigateur.

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
- [Les succès](#les-succès)
- [Les gains hors-ligne](#les-gains-hors-ligne)
- [Sauvegarde](#sauvegarde)
- [Interface](#interface)
- [Modifier l'équilibrage](#modifier-léquilibrage)

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
| 🌟 **Bond temporel** | **1 %** | **30 minutes** de production, d'un coup |
| ⚡ Surtension | 5 % | production ×7 pendant 45 s |
| ✨ Écho quantique | 5 % | clic ×12 pendant 60 s |
| 💎 Filon riche | 44,5 % | 180 secondes de production, d'un coup |
| 📦 Cache abandonnée | 44,5 % | +18 % de ton minerai en réserve |

Les multiplicateurs sont volontairement rares : ils se cumulent avec tout le
reste et deviennent démesurés en fin de partie, alors que le filon et la cache
restent proportionnés à ta progression. Les durées sont allongées de 30 % par
niveau de Détecteur.

### Le bond temporel

C'est le gros lot : un saut de **30 minutes en avant**, crédité instantanément
(`production/s × 1800`). Soit **dix fois** un Filon riche, tout en restant
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

## Les succès

**44 succès**, chacun donnant **+1 % de production** — soit **+44 %** au
complet. Ils portent sur les clics, le nombre de structures, le minerai extrait,
la production par seconde, les anomalies attrapées, les cycles, l'antimatière,
et la complétion des recherches et améliorations. Ils ne sont **jamais perdus**
au prestige.

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

**Toucher** — un achat n'est validé qu'au relâchement du doigt, et seulement si
tu n'as pas bougé de plus de 12 px en moins de 0,9 s : faire défiler ne déclenche
jamais d'achat par erreur. Le clic sur la planète reste instantané. Le zoom par
double-tap est neutralisé.

**Langue** — sélecteur FR / EN en haut à droite ; la langue par défaut suit celle
du navigateur et ton choix est mémorisé. Changer de langue ne touche pas à la
partie en cours.

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
| Anomalies, effets et **probabilités** (`w`) | tableau `ANOMS` |
| Fréquence des anomalies | `anomInterval()` |
| Gain de prestige | `amGain()` |
| Bonus par antimatière | `amBonus()` |
| Hors-ligne | `offlineCap()`, `offlineRate()`, `GRACE` |
| Textes des deux langues | objet `T` |

Les poids `w` du tableau `ANOMS` totalisent 200 : un point vaut 0,5 %.

---

## Licence

Fais-en ce que tu veux.
