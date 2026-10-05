# 🥚 Steal an Egg (clone Roblox)

Vole les œufs de créatures gardés par des boss, ramène-les dans ton enclos et deviens le joueur le plus rapide du serveur.

## Concept

- **Carte en T** : la barre du T contient la Prairie (gauche), le **Hub avec les enclos** (centre) et le Désert (droite). Le pied du T enchaîne Glacier → Volcan → Royaume du Ciel.
- **Enclos** : chaque joueur reçoit un enclos (6 emplacements). Les œufs déposés rapportent des pièces chaque seconde.
- **Tapis roulant** : placé en face de l'enclos, il faut **courir dessus** pour gagner de la vitesse.
- **Boss** : chaque biome a un boss qui garde un œuf. Chaque boss impose une **vitesse minimale** pour voler l'œuf.
  Le boss court à 90 % de cette vitesse : un joueur assez rapide peut donc toujours le semer.
- Si un boss te touche, tu perds de la vie et tu **lâches l'œuf** (il retourne sur son piédestal).

| Biome | Vitesse min. | Revenu de l'œuf |
|---|---|---|
| Prairie | 22 | 1/s |
| Désert | 32 | 3/s |
| Glacier | 45 | 8/s |
| Volcan | 60 | 20/s |
| Ciel | 80 | 60/s |

Tout l'équilibrage se règle dans `src/shared/Config.lua`.

## Installation

1. Installer [Rojo](https://rojo.space) (plugin Studio + CLI, par exemple via [Rokit](https://github.com/rojo-rbx/rokit)).
2. Dans Roblox Studio, créer un place vide (template *Baseplate*, supprime la Baseplate : la carte est générée par script).
3. Lancer `rojo serve` dans ce dossier, puis cliquer sur **Connect** dans le plugin Rojo.
4. Dans *Game Settings → Players*, mettre **Max Players = 8**.
5. Dans *Game Settings → Security*, activer **Enable Studio Access to API Services** (sauvegarde des données).
6. Appuyer sur Play.

## Envoyer sur GitHub

```bash
cd steal-an-egg
git init
git add .
git commit -m "Premier commit : Steal an Egg"
git branch -M main
git remote add origin https://github.com/TON_PSEUDO/steal-an-egg.git
git push -u origin main
```

## Déploiement automatique (GitHub Actions)

À chaque `push` sur `main`, le workflow `.github/workflows/deploy.yml` construit le jeu avec Rojo puis le publie sur Roblox via l'API Open Cloud. Tu peux aussi le lancer à la main (onglet **Actions → Run workflow**) en choisissant `Published` (en ligne) ou `Saved` (brouillon).

> ⚠️ **La création de l'expérience ne peut pas être automatisée.** L'API Roblox ne sait que publier dans une expérience qui existe déjà (la seule méthode de création est une API privée basée sur ton cookie `.ROBLOSECURITY`, que je déconseille : elle donne un accès complet à ton compte et peut changer sans préavis). Il faut donc la créer **une seule fois** à la main.

### Configuration (une seule fois)

1. **Créer l'expérience** : pousse le code (ou lance le workflow à la main) : il construit le fichier et saute la publication avec un avertissement. Télécharge l'artefact `StealAnEgg-place`, ouvre le `.rbxl` dans Studio puis **File → Publish to Roblox As… → Create new**.
2. **Récupérer les IDs** sur [create.roblox.com/dashboard/creations](https://create.roblox.com/dashboard/creations) : menu **⋯** de l'expérience → *Copy Universe ID* et *Copy Start Place ID*.
3. **Créer la clé API** sur [create.roblox.com/dashboard/credentials](https://create.roblox.com/dashboard/credentials) :
   - API System : **universe-places** → expérience concernée → opération **Write**
   - Accepted IP Addresses : **`0.0.0.0/0`** (les IP des runners GitHub changent à chaque exécution)
4. **Ajouter dans GitHub** (*Settings → Secrets and variables → Actions*) :

| Type | Nom | Valeur | Obligatoire |
|---|---|---|---|
| Secret | `ROBLOX_API_KEY` | La clé API créée à l'étape 3 | Oui |
| Variable | `ROBLOX_UNIVERSE_ID` | Universe ID de l'expérience | Oui |
| Variable | `ROBLOX_PLACE_ID` | Start Place ID de l'expérience | Oui |
| Variable | `ROBLOX_VERSION_TYPE` | `Published` (défaut) ou `Saved` | Non |

5. `git push` : le jeu est publié automatiquement.

Le publish **remplace entièrement** le contenu du place sur Roblox par le fichier construit par Rojo : tout ce que tu modifies dans Studio sans le mettre dans `src/` sera écrasé.

## Structure

```
default.project.json          Projet Rojo
.github/workflows/deploy.yml  Build Rojo + publication Roblox (Open Cloud)
src/shared/Config.lua         Équilibrage : zones, boss, vitesses, revenus
src/server/MapBuilder.lua     Génère la carte en T, enclos, tapis, boss
src/server/Main.server.lua    Logique : entraînement, vol d'œufs, boss, sauvegarde
src/client/HUD.client.lua     Interface : vitesse, pièces, messages
```

## Idées pour la suite

- Boutique : acheter des emplacements, des tapis plus efficaces, des boosts de vitesse
- Éclosion des œufs avec un minuteur et des créatures à collectionner
- Vol d'œufs entre joueurs, avec un système de verrouillage d'enclos
- Vrais modèles 3D et animations pour les boss et les créatures
- Événements de serveur (œuf légendaire, boss géant)

## ⚠️ Propriété intellectuelle

Les créatures et les noms d'œufs sont **originaux** volontairement. Pokémon est une marque de Nintendo / Creatures / GAME FREAK / The Pokémon Company : utiliser leurs noms, designs ou assets dans un jeu Roblox publié expose à un retrait du jeu et à des sanctions sur le compte. Crée tes propres créatures.
