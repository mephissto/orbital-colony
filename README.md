# Colonie Orbitale

Idle game spatial, bilingue FR / EN, installable en application (PWA).

## Fichiers

| Fichier | Rôle |
|---|---|
| `index.html` | le jeu entier — logique, styles, favicon |
| `manifest.webmanifest` | nom, couleurs et icônes de l'application installée |
| `sw.js` | service worker : rend le jeu jouable hors connexion |
| `icon-192.png`, `icon-512.png` | icônes d'application |
| `icon-maskable-512.png` | icône adaptative Android (recadrable) |
| `apple-touch-icon.png` | icône iOS |

Tous les fichiers vont **à la racine du dépôt**, à plat, à côté les uns des autres.

## Publier

Dépose les fichiers sur GitHub (ou glisse le dossier sur Netlify). L'installation
exige HTTPS — GitHub Pages et Netlify le fournissent automatiquement.

## Installer le jeu

- **Android / Chrome** : menu ⋮ → « Installer l'application »
- **iOS / Safari** : Partager → « Sur l'écran d'accueil »
- **Bureau** : icône d'installation dans la barre d'adresse

Une fois installé, le jeu s'ouvre en plein écran et fonctionne sans connexion.

## Mettre à jour le jeu

En ligne, le service worker va toujours chercher la dernière version de
`index.html` sur le réseau : un simple rechargement suffit après un déploiement.

Si tu modifies les **icônes ou le manifeste**, incrémente `CACHE` en haut de
`sw.js` (`colonie-orbitale-v1` → `colonie-orbitale-v2`) pour forcer le
renouvellement du cache chez les joueurs.

## Sauvegardes

La partie est stockée dans le `localStorage` du navigateur, donc liée au domaine.
Le bouton Export / Import permet de transférer une partie d'un appareil à l'autre.
