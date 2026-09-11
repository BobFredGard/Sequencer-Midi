# Sequencer MIDI (Sequencer 4)

Séquenceur MIDI en grille verticale, **100 % local** (un seul fichier `index.html`, sans dépendance ni build) :
playlist de morceaux, édition d'événements MIDI (CC + SysEx), 3 sorties MIDI (Light / Wing / Pedal),
rampes de tempo, groupes de mesures colorés, démarrage sur détection audio, sauvegarde auto.

> `SPEC.md` = spécification d'origine du projet. Le présent README décrit le **comportement réel vérifié
> dans `index.html`** (voir « Écarts avec SPEC.md » en bas de page).

- J'ai développé cet outil pour pouvoir piloter mes lumières, ma WING (Console) et les pédaliers des gratteux !
- L'idée était de créer un séquencer simple dans lequel je créerais des mesures à la volée, 2 temps, 3 temps, 4 temps, 5 temps etc... Que chaque mesure pourrait être divisée un subdivisions, à la noire , la croche, triolets etc...
- L'appli le permet et bien plus. On peut réduire le tempo, l'augmenter, mettre des couleurs par couplet, refrain etc...
- Et surtout, on peut lancer la lecture via un PC (Program Change) venant de ma WING quand je mets en lecture mon playback. Pour un tempo bien en place, pas de soucis, j'ai juste mis une compensention ou retard à l'allumage (250ms était la bonne valeur) car le démarrage de la lecture n'était pas instantané sur ma console !
- Si par contre on veut piloter via un clic audio, cela est possible aussi avec un clic bien propre bien sûr !

## Interface
<img width="1903" height="990" alt="image" src="https://github.com/user-attachments/assets/a40cdae0-5f73-4c3f-a96e-2b343d922e8b" />

## Démarrage

1. Ouvrir `index.html` dans Chrome ou Edge (double-clic suffit pour l'édition et la lecture).
2. Pour le **MIDI** et le **micro** (Audio In), utiliser un contexte sécurisé : `https://`, `http://localhost`
   ou une extension type *Live Server* / `npx serve`. L'API Web MIDI n'est pas disponible en `file://` dans tous les cas.
3. Choisir les périphériques dans les listes **MIDI IN**, **LIGHT MIDI OUT**, **WING MIDI OUT**, **PEDAL MIDI OUT**
   (bouton `↻` pour rafraîchir). Activer l'accès SysEx quand le navigateur le propose.

Aucune installation, aucune donnée envoyée en ligne : tout est stocké dans le `localStorage` du navigateur.

## Interface

| Zone | Contenu |
|---|---|
| **Playlist (gauche)** | Liste des morceaux, `+ New`, `Clear`, verrou `🔒` |
| **Barre de contrôle** | Nom du morceau, BPM, nombre de mesures, subdivision, groupes, détails de l'événement sélectionné |
| **Barre de transport** | Audio In + VU-mètre, Audio Start, décalage de démarrage (ms), `▶`/`⏸`, LED, position, affichage MIDI OUT |
| **Grille** | Une ligne = une mesure ; colonnes = temps puis subdivisions ; têtes de mesure `X/4` ; ligne d'aide en bas |
| **Éditeurs** | Panneau Channel/CC/Value + éditeur CC flottant selon le type d'événement (+ champ SysEx) |

## Lecture / Transport

- `▶` ou `Espace` : lecture depuis la mesure 1, **ou** depuis la tête de lecture si elle a été
  positionnée manuellement (clic sur une case vide). `Espace` ou `⏸` : stop.
- LED de lecture, affichage `Measure: …`, beat-LEDs et tête de lecture suivent la position.
- **Start + … ms** : délai ajouté avant le démarrage effectif.
- **Audio In + Audio Start** : la lecture démarre automatiquement quand une séquence audio est détectée
  (VU-mètre + statut *« Waiting for audio sequence… »*). Le micro doit être autorisé.
- **BPM** : 20 à 300 (décimales acceptées). **Mesures** : 10 à 1000.
- **Subdivision globale** : `¼` (4), croches (8), `12/8` (3 par temps), doubles-croches (16).
  La subdivision et la signature peuvent aussi être réglées **par mesure** (clic droit).

## Événements : les 4 types

| Type | Couleur | Création | Sortie MIDI | Éditeur ouvert à la sélection |
|---|---|---|---|---|
| **Mute** (standard) | orange | `Alt` + clic droit | Wing (OUT 2) | Éditeur Wing Pilot (Ch1 Volume + Ch2 Mute) + SysEx |
| **Volume** | vert | `Alt` + clic gauche | Light (OUT 1) | Éditeur Light (Ch1) |
| **Pilot** | violet | `Alt` + clic molette | Pedal (OUT 3) | Éditeur Pilot (Ch1 FRED + Ch2 SEB + Program Change) |
| **WingPilot** | orange | (collage d'un événement de ce type) | Wing (OUT 2) | Éditeur Wing Pilot + SysEx |

Chaque événement contient : canal (1–16), une **liste de CC** `{cc, value}` 0–127 sur 2 colonnes,
et une chaîne **SysEx hexadécimal** (`F0 … F7`) envoyée sur la sortie au déclenchement.
Le canal/CC/valeur affichés dans la barre de contrôle reprennent le premier CC de la liste.
`Entrée` valide une saisie ; dans le champ SysEx, `Shift + Entrée` insère un saut de ligne.

## Raccourcis clavier

Actifs partout **sauf** dans les champs de saisie (`input`, `select`, `textarea`).

| Touches | Action |
|---|---|
| `Espace` | Lecture / Stop |
| `Ctrl` + `C` | Copier le(s) événement(s) sélectionné(s) |
| `Ctrl` + `V` | Coller à la **tête de lecture** (avec décalage relatif si multi-sélection) |
| `Ctrl` + `Z` | Annuler le dernier collage |
| `Alt` + `Suppr` | Supprimer l'événement sélectionné |
| `D` | Marquer la mesure sous le curseur comme **début** de rampe de tempo |
| `F` | Ouvrir le dialogue de rampe entre la mesure marquée (`D`) et celle sous le curseur |
| `S` | **Supprimer** la rampe qui commence à la mesure sous le curseur |
| `Entrée` | Valider le champ en cours (nom, CC, SysEx…) |

## Souris

### Sur une case vide de la grille
| Geste | Action |
|---|---|
| Clic gauche | Déplace la **tête de lecture** (la prochaine lecture part d'ici) |
| `Alt` + clic gauche | Crée un événement **Volume** (vert) |
| `Alt` + clic molette | Crée un événement **Pilot** |
| `Alt` + clic droit | Crée un événement **Mute** standard |
| Clic droit (sans `Alt`) | Menu mesure : signature 2/4 → 8/4, subdivision 4/8/12/16/32, **insérer** / **supprimer** une mesure |

### Sur un événement
| Geste | Action |
|---|---|
| Clic gauche | Sélectionne (ouvre l'éditeur CC / SysEx adapté) |
| `Ctrl` + clic | Ajoute/retire de la **multi-sélection** (copie et couleurs groupées) |
| `Shift` + clic | **Supprime** l'événement |
| Glisser-déposer | **Déplace** (magnétisme sur la subdivision la plus proche) |
| Bouton `✕` | Supprime l'événement sélectionné |

### Sur l'en-tête d'une mesure (`X/4`)
| Geste | Action |
|---|---|
| `Ctrl` + `Shift` + clic | Ajoute/retire la mesure au **groupe en cours** ; le nom et la couleur se règlent dans la barre (`Group` + pastilles) ; `✕` supprime le groupe |
| Clic droit | Même menu mesure que ci-dessus |

## Rampes de tempo

1. Placer la souris sur la mesure de départ, touche `D` (surlignage vert).
2. Placer la souris sur la mesure d'arrivée, touche `F` → dialogue **Tempo Ramp** (BPM départ / BPM arrivée) → `Apply`.
3. Une ligne verte → orange relie les deux mesures, avec badges `120→` cliquables pour rééditer.
4. Touche `S` sur la mesure de départ pour supprimer la rampe.
5. Le tempo suivi pendant la lecture interpole entre les deux BPM.

## Groupes de mesures

- `Ctrl` + `Shift` + clic sur des en-têtes pour sélectionner des mesures (compteur affiché à droite de la barre).
- Nom (8 caractères max) + une des 10 pastilles de couleur → appliquée immédiatement, affichée sur les en-têtes.
- Les événements multi-sélectionnés peuvent aussi recevoir la couleur du groupe.
- `✕` (Delete Group) : dissout le groupe.

## Playlist

- `+ New` : nouveau morceau · `Clear` : vide la playlist (avec confirmation).
- Glisser-déposer pour réordonner ; renommage direct dans la liste (`Entrée` pour valider) ou via le champ `Song`.
- Suppression d'un morceau avec confirmation.
- Verrou `🔒` : bloque le réordonnancement **et** active le pilotage par **Program Change MIDI** :
  un message PC reçu sur MIDI IN sélectionne le morceau correspondant et démarre la lecture.
- Bouton **`📂 Liste`** : charge une liste depuis un fichier JSON — formats acceptés : export playlist
  `{playlist, activeSongId}`, tableau brut `[...]`, ou morceau seul `{id, name, events}` (ajouté à la liste).
  Les fichiers incomplets sont normalisés (valeurs par défaut) au lieu de casser la grille.
- **Clic droit sur la playlist** : tri `A→Z`, `Import` / `Export` (JSON), `+ Load Song` (fichier morceau),
  `Export CSV` (format type `Gotha!.csv`).

## Import / Export / Sauvegarde

| Fonction | Format |
|---|---|
| Sauvegarde auto | `localStorage` à chaque modification |
| `Export` / `Import` (menu playlist) | JSON playlist complète `{playlist: [...]}` |
| `+ Load Song` | JSON d'un seul morceau |
| `Export CSV` | CSV `N°;Titre` prêt pour tableur |
| Fichiers fournis | `Gotha!.json` (playlist complète ~370 Ko), `sequencer4_playlist (10).json` (variante), `Gotha!.csv` |

Structure d'un morceau : `id, name, bpm, measures, subdivision, measureBeats[], measureSubdivisions[]`,
`events[]` (`id, measure, subdivision, channel, type, ccs[{cc, value}], ccs2, sysex`),
`tempoRamps[]` (`startMeasure, endMeasure, startTempo, endTempo`), `customGroups[]` (`name, color, measures[]`).

## MIDI en détail

- 4 routages indépendants : **IN**, **LIGHT OUT**, **WING OUT**, **PEDAL OUT**, avec LEDs d'activité
  et affichage de la dernière valeur envoyée.
- À chaque événement atteint en lecture : envoi des CC (canal + contrôleur + valeur) **puis** du SysEx
  s'il est renseigné, sur la sortie correspondant au type (Pilot → Pedal, Volume → Light, autres → Wing).
- L'éditeur Pilot expose en plus deux **Program Change** (Ch1/Ch2, 0–127).
- MIDI IN : les **Program Change** pilotent la playlist verrouillée (changement de morceau + lecture auto).

## Écarts connus avec SPEC.md

La spec d'origine a été dépassée par le code ; en cas de contradiction, **le code fait foi** :
suppression = `Shift`+clic (pas `Alt`), démarrage à la mesure 1 ou à la tête manuelle (pas de compte
à rebours 5 clics — la fonction `playClick` existe mais n'est plus appelée), mesures 10–1000
(au lieu de 10–200), subdivisions globales 4/8/12/16 + réglage par mesure jusqu'à 32,
3 sorties MIDI + 4 types d'événements avec SysEx (au lieu d'un type CC simple).
