# TLB Spawn : réglages

[English version](README_EN.md)

Ce document décrit tous les réglages du mod **TLB Spawn** pour DayZ : menu de choix du point d'apparition, points de spawn
vanilla de la carte, attentes par point, blocage après une mort et sacs de couchage de base.

Tous les réglages tiennent dans un seul fichier : `<dossier profil du serveur>/TLB_Spawn/TLB_Spawn_Settings.json`.
Il est créé au premier démarrage avec les valeurs par défaut ([`files/TLB_Spawn_Settings.json`](files/TLB_Spawn_Settings.json)),
puis réécrit à chaque démarrage pour ajouter les nouveaux réglages. Un exemple rempli est dans
[`files/TLB_Spawn_Settings.example.json`](files/TLB_Spawn_Settings.example.json).

**À savoir avant de modifier le fichier**

- Oui / non : `1` = oui, `0` = non.
- Durées en secondes, distances en mètres.
- Positions : `[x, y, z]` (y = hauteur). Copiez-les depuis le menu admin (« Ajouter ma position ») plutôt qu'à la main.
- Presque tout se règle en jeu dans le **menu admin** (touche **Début / Home**, modifiable dans
  Options > Contrôles > TLB Spawn). Il réécrit ce fichier quand vous cliquez sur « Enregistrer ». Seule la liste
  `AdminIDs` ne se modifie que dans le fichier.
- Modifiez le fichier serveur arrêté, ou utilisez le menu admin : un fichier modifié serveur allumé est écrasé à la
  prochaine sauvegarde depuis le menu.

## Installation

1. Chargez le mod **côté client et côté serveur**. Il n'a aucune dépendance (pas besoin de CF).
2. Ajoutez les entrées de [`files/types_tlbspawn.xml`](files/types_tlbspawn.xml) dans le `types.xml` de la mission :
   - `TLBSP_SleepingBag` : sac de couchage plié, trouvable dans le butin (ou à vendre chez un trader) ;
   - `TLBSP_SleepingBag_Deployed` : sac posé, durée de vie 45 jours (rafraîchie par un drapeau de territoire).
3. Démarrez une fois le serveur : le fichier de réglages est créé.
4. Mettez votre SteamID64 dans `AdminIDs`, redémarrez, puis réglez le reste en jeu.

## Fonctionnement

- **Nouveau personnage** (mode `0`) : le menu s'ouvre et le joueur choisit un point. Pendant le choix, il est
  invulnérable. Le choix en attente survit à une déconnexion. Passé `SelectionTimeoutSeconds`, un point aléatoire libre
  est attribué.
- **Points vanilla** : chaque `<group name="…">` de la section `fresh` du `cfgplayerspawnpoints.xml` de la mission devient
  un point du menu. Un groupe dont les positions sont éloignées de plus de `VanillaGroupMaxSpread` est découpé en
  plusieurs points (« North Berezino », « North Berezino 2 »). Une position isolée (erreur du fichier vanilla) est
  ignorée. Les positions hors groupe sont regroupées et nommées d'après la ville la plus proche.
- **Attente** : après une apparition sur un point, ce point est indisponible pour ce joueur pendant `CooldownSeconds`.
  L'attente est enregistrée en heure UTC dans `TLB_Spawn/Players/<joueur>.json` : elle survit au relog et aux
  redémarrages.
- **Blocage après une mort** : si le joueur meurt à moins de `DeathBlockRadius` mètres d'un point, ce point est
  indisponible pour lui pendant `DeathBlockSeconds`.
- **Sacs de couchage** : le joueur pose le sac plié avec l'hologramme. Le sac déplié devient son point de réapparition
  (« Sleeping Bag (Elektrozavodsk) »). Il se replie avec l'action « Replier le sac de couchage ». Le bouton
  « Aléatoire » ne choisit jamais un sac.

## Réglages généraux

| Réglage | Par défaut | Explication |
|---|---|---|
| `Version` | `1` | Version du format du fichier, gérée par le mod : ne pas modifier |
| `Enabled` | `1` | 1 = mod actif ; 0 = le mod ne fait plus rien (apparition vanilla, sacs refusés) |
| `SpawnMode` | `0` | `0` = menu imposé à chaque nouveau personnage ; `1` = apparition vanilla, pas de menu |
| `SelectionTimeoutSeconds` | `300` | Temps laissé pour choisir avant d'attribuer un point aléatoire (0 = pas de limite) |
| `InvulnerableWhileSelecting` | `1` | 1 = le joueur ne subit aucun dégât tant qu'il n'a pas choisi |
| `AllowRandomButton` | `1` | 1 = affiche le bouton « Aléatoire » (point vanilla libre au hasard) |
| `MinDistanceToPlayers` | `80` | Dans un point à plusieurs positions, évite celles à moins de X m d'un autre joueur (0 = désactivé) |
| `AdminIDs` | `[]` | SteamID64 des admins (ex. `"76561198000000000"`). L'identifiant haché du joueur, affiché dans le log du serveur à chaque apparition, est aussi accepté |

## Points vanilla

| Réglage | Par défaut | Explication |
|---|---|---|
| `UseVanillaSpawnPoints` | `1` | 1 = les groupes du `cfgplayerspawnpoints.xml` de la mission apparaissent dans le menu |
| `VanillaSection` | `"fresh"` | Section du fichier vanilla à lire : `"fresh"`, `"hop"` ou `"travel"` |
| `VanillaGroupMaxSpread` | `1500` | Distance (m) au-delà de laquelle un groupe est découpé en plusieurs points |
| `VanillaDefaults.CooldownSeconds` | `600` | Attente par défaut des points vanilla |
| `VanillaDefaults.DeathBlockRadius` | `500` | Rayon de blocage après une mort, par défaut |
| `VanillaDefaults.DeathBlockSeconds` | `1800` | Durée du blocage après une mort, par défaut (0 = jamais bloqué) |
| `VanillaOverrides` | `[]` | Réglages propres à un groupe vanilla (voir ci-dessous). Le menu admin les crée pour vous |
| `VanillaOverrides[].GroupName` | | Nom du groupe dans le fichier vanilla (ex. `"WestCherno"`), ou nom découpé (`"NorthBerezino_2"`) |
| `VanillaOverrides[].DisplayName` | `""` | Nom affiché dans le menu (`""` = nom du groupe, ex. « West Cherno ») |
| `VanillaOverrides[].Enabled` | `1` | 0 = ce point n'apparaît plus dans le menu |
| `VanillaOverrides[].CooldownSeconds` | | Attente de ce point |
| `VanillaOverrides[].DeathBlockRadius` | | Rayon de blocage après une mort pour ce point |
| `VanillaOverrides[].DeathBlockSeconds` | | Durée du blocage après une mort pour ce point |

Groupes de Chernarus (section `fresh`) : `WestCherno`, `EastCherno`, `WestElektro`, `EastElektro`, `Kamyshovo`,
`Solnechny`, `Nizhnee`, `SouthBerezino`, `Svetlojarsk`, `Orlovets`, `NorthBerezino`, `NorthBerezino_2`.

## Points personnalisés

Points créés par l'admin, affichés en tête du menu avec une étoile. Le plus simple : menu admin > Points >
« Nouveau point ici ».

| Réglage | Par défaut | Explication |
|---|---|---|
| `CustomSpawns[].Name` | | Nom affiché (il sert aussi d'identifiant pour les attentes : le renommer remet les attentes à zéro) |
| `CustomSpawns[].Enabled` | `1` | 0 = point masqué |
| `CustomSpawns[].Positions` | | Une ou plusieurs positions `[x, y, z]`. Une est tirée au hasard, en évitant les autres joueurs |
| `CustomSpawns[].ScatterRadius` | `0` | 0 = position exacte, hauteur gardée (utilisable à l'étage d'un bâtiment) ; sinon dispersion aléatoire en mètres, posée au sol |
| `CustomSpawns[].CooldownSeconds` | | Attente de ce point |
| `CustomSpawns[].DeathBlockRadius` | | Rayon de blocage après une mort |
| `CustomSpawns[].DeathBlockSeconds` | | Durée du blocage après une mort |

Un point exact placé sous le sol est automatiquement remonté au niveau du terrain.

## Zone de spawn

| Réglage | Par défaut | Explication |
|---|---|---|
| `SpawnZone.OverrideDefaultSpawn` | `0` | 1 = les nouveaux personnages apparaissent dans la zone (ils y attendent pendant le choix) |
| `SpawnZone.Radius` | `10` | Rayon (m) autour de la position tirée |
| `SpawnZone.Positions` | `[]` | Une ou plusieurs positions de la zone (menu admin > Général > « Ajouter ma position à la zone ») |

## Sacs de couchage

| Réglage | Par défaut | Explication |
|---|---|---|
| `SleepingBags.Enabled` | `1` | 0 = impossible de poser un sac, les sacs existants n'apparaissent plus dans le menu |
| `SleepingBags.DisplayName` | `"Sleeping Bag"` | Nom affiché dans le menu, suivi de la ville la plus proche |
| `SleepingBags.MaxPerPlayer` | `1` | Nombre de sacs posés par joueur (0 = illimité). Au-delà, la pose est refusée avec un message |
| `SleepingBags.CooldownSeconds` | `1800` | Attente entre deux apparitions sur un même sac |
| `SleepingBags.MaxUses` | `0` | Nombre d'apparitions par sac (0 = illimité). Le compte suit le sac quand il est replié |
| `SleepingBags.DestroyWhenUsedUp` | `1` | 1 = le sac disparaît quand `MaxUses` est atteint |
| `SleepingBags.DeathBlockRadius` | `150` | Mourir à moins de X m de son sac le bloque... |
| `SleepingBags.DeathBlockSeconds` | `900` | ...pendant Y secondes (0 = jamais bloqué) |
| `SleepingBags.OnlyOwnerCanPack` | `0` | 0 = n'importe qui peut replier un sac (on peut détruire le point de réapparition d'un autre en raid) ; 1 = seul le propriétaire |
| `SleepingBags.MinDistanceToSpawnZone` | `200` | Interdit de poser un sac à moins de X m de la zone de spawn (0 = pas de limite) |

## Menu admin

Touche **Début (Home)**, réservée aux joueurs de `AdminIDs`. Trois onglets :

- **Points** : liste des points personnalisés (étoile) et vanilla. Pour un point personnalisé : nom, actif, attente,
  blocage après mort, dispersion, « Ajouter ma position », « Retirer la dernière position », « Se téléporter »,
  « Supprimer ». Pour un point vanilla : nom affiché, actif, attente, blocage. « Nouveau point ici » crée un point à
  votre position.
- **Sacs de couchage** : tous les réglages `SleepingBags`.
- **Général** : mod actif, mode, délai de choix, invulnérabilité, bouton aléatoire, distance aux autres joueurs,
  points vanilla, zone de spawn.

« Enregistrer » applique les changements immédiatement et réécrit `TLB_Spawn_Settings.json`.

## Fichiers des joueurs

`TLB_Spawn/Players/<identifiant>.json` : choix en attente, dernière mort (position et heure) et attentes en cours.
Gérés par le mod. Supprimer un fichier remet les attentes de ce joueur à zéro.
