# Feuille de route

État au **2026-08-22**, version **2.13.0**. Ce document sert de mémoire entre
deux sessions : ce qui est décidé, ce qui reste à faire, et pourquoi.

---

## Où on en est

Le plan d'ensemble pour allonger la durée de vie du jeu comportait quatre
étapes. Les deux premières sont faites.

| | Étape | État |
|---|---|---|
| 0 | Corriger la linéarité du bonus d'antimatière | ✅ v2.2.0 |
| 1 | Automatisation | ✅ v2.3.0 → 2.8.0 |
| 2 | **Défis** | ⬜ à faire — étape suivante |
| 3 | **Effondrement + astres** | ⬜ à faire |

Entre-temps : succès rangés en catégories et portés à 71 (v2.9 → 2.11),
statistiques refaites en tuiles (v2.12.0), licence GPL 3.0 (v2.12.1).

Le diagnostic de départ reste la référence : maxer les 8 recherches coûte
**106 434 antimatière**, après quoi l'antimatière n'avait plus aucun débouché.
L'automatisation en a ouvert un premier (104 650, dont 102 300 pour les dix
niveaux du Bras automatique). Les défis et l'effondrement
doivent ouvrir la suite.

---

## Étape 2 — Les défis

**Validé tel quel.** Y compris le point qui avait été corrigé après réflexion :
**entrer dans un défi encaisse d'abord le cycle en cours** et crédite
l'antimatière en attente, *puis* la remise à zéro a lieu. Sans ça le joueur
attend toujours « le bon moment » et lancer un défi devient une corvée à
planifier.

### La boucle

- Nouvel onglet **Défis**, révélé après une dizaine de cycles.
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

| Défi | Règle cassée | Récompense permanente |
|---|---|---|
| 📵 Silence radio | aucune anomalie n'apparaît | anomalies +20 % plus fréquentes |
| 🤐 Mains liées | le clic ne rapporte rien | production +25 % |
| 📈 Inflation | prix en ×1,25 au lieu de ×1,15 | −8 % sur tous les prix |
| 🏚️ Colonie naine | seules les 4 premières structures existent | les 4 dernières produisent ×2 |
| 💨 Fuite de confinement | production −2 % par tranche de 30 s écoulée | gain d'antimatière +15 % |
| 🕳️ Le Vide | ni succès ni antimatière dans le multiplicateur | exposant antimatière 1,50 → 1,55 |

### Notes d'implémentation

- Chaque contrainte est **un seul interrupteur inversé** dans le code existant :
  `globalMult`, `clickVal`, `genCost`, `anomInterval`, `amGain`. Aucune économie
  nouvelle à équilibrer.
- État : `S.chal` (id du défi actif ou `null`) et `S.chalDone` ({id:1}).
  Champs additifs → sauvegardes antérieures compatibles sans conversion.
- Objectifs en **valeurs fixes**, pas relatives au meilleur cycle. Un joueur
  avancé les expédie : c'est la récompense d'avoir progressé, et comme chaque
  défi ne se valide qu'une fois il n'y a rien à exploiter.
- Prévoir des succès de la catégorie Défis, et une tuile de statistiques
  « Défis réussis ».

---

## Étape 3 — L'effondrement et les astres

**Validé.** Deuxième couche de prestige, débloquée à **5 000 antimatière
produites au total**.

### L'effondrement

| Remis à zéro | Conservé |
|---|---|
| minerai, structures, améliorations, **antimatière, recherches** | succès, défis réussis, **automates**, Éclats |

Les automates sont conservés délibérément : perdre son bras automatique à chaque
effondrement serait une punition, pas un défi, et découragerait d'effondrer.
Les recherches, elles, repartent à zéro — le puits de 106 434 antimatière se
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
  contre le code.
- **Licence** — GPL 3.0 ou ultérieure. Remplacer `Guilhem` par le nom complet
  dans `index.html`, `sw.js` et le README si souhaité.

## Petits points en suspens

- Rien n'annonce l'apparition de l'onglet **Auto** au premier cycle : proposition
  d'ajouter un message façon toast de succès. En attente de décision.
- Catégories de succès **Extraction** (8) et **Production** (5) : ce sont de purs
  paliers de nombres, la partie la plus mécanique de la liste. À retravailler
  éventuellement en même temps que les défis.
