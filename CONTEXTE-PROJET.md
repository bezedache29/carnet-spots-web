# Carnet de spots — Web (React + Vite)

> Document de référence pour Claude Code sur ce repo. Ce repo est la SPA de **préparation** de road trip : consomme l'API Laravel (`carnet-spots-api`) comme client HTTP.

## 1. Contexte & besoin du projet

App personnelle (usage solo en V1) pour marquer des points GPS précis (parkings, spots de nuit, emplacements repérés) même sans adresse existante (ex. en pleine forêt), avec une note libre, pour les retrouver et les consulter une fois sur le terrain sans réseau.

Ce repo couvre la phase **préparation** (maison, ordinateur, wifi stable) : recherche de spots pour un road trip, ajout de points sur une carte plein écran avec note. La phase terrain (offline, GPS) est gérée par `carnet-spots-mobile`, pas par ce repo — pas de contrainte offline ici.

## 2. Rôle de ce repo dans l'architecture globale

**3 repos séparés**, une seule API comme source de vérité :

- `carnet-spots-api` (Laravel) — source de vérité, consommée par ce repo en HTTP
- `carnet-spots-web` (ce repo, React + Vite) — SPA de préparation
- `carnet-spots-mobile` (React Native CLI bare, Android) — usage terrain offline

## 3. Décisions techniques actées pour ce repo

| Sujet     | Décision                                                   | Raison                                                                                                                  |
| --------- | ---------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| Framework | **React + Vite**, pas Next.js, pas Inertia                 | App privée derrière login, pas de besoin SEO/SSR en V1 ; évite la duplication de logique entre client web et API mobile |
| Carte     | **MapLibre GL JS**                                         | Gratuit, pas de coût à l'usage contrairement à Google Maps API ; cohérence avec MapLibre Native côté mobile             |
| Auth      | Token **Laravel Sanctum** côté API, consommé par ce client | Écosystème Laravel natif                                                                                                |
| Offline   | **Aucun besoin offline sur ce repo**                       | Usage exclusivement à la maison / wifi stable en phase préparation                                                      |

**Note pour plus tard** : si ouverture publique avec landing page/blog, ce sera un **4ᵉ repo séparé en Next.js** (`carnet-spots-landing`), indépendant de cette SPA — pas de fusion des deux techs dans un même projet.

## 4. Modèle de données consommé (via l'API)

Ce repo ne possède pas de base de données propre — il consomme les ressources exposées par `carnet-spots-api` :

- **`points`** : `id`, `owner_id`, `title`, `latitude`, `longitude`, `note`, `status` (`a_tester` / `valide` / `a_eviter` / `deja_dormi`), `visited_at`, `visibility` (`private` / `public`)
- **`tags`** : `id`, `owner_id`, `name` (unique par owner)
- **`point_tag`** : relation many-to-many entre points et tags
- **`photos`** (V2, hors scope V1) : relation à un point

## 5. Fonctionnalités attendues pour ce repo (roadmap V1)

- Carte plein écran (MapLibre GL JS)
- Ajout de point par **clic direct sur la carte** (pas de recherche d'adresse — c'est le point différenciant du projet par rapport à Park4Night/Mapstr)
- Formulaire d'ajout/édition : titre, note, statut, tags
- Liste des points avec **filtre par tag et par statut**
- Auth via token Sanctum (login simple, un seul compte en V1)

## 6. Hors scope V1 (à ne pas anticiper dans le code maintenant)

- Inscription multi-utilisateurs, policies d'accès croisé, modération des points publics
- Landing page / blog (4ᵉ repo Next.js séparé si besoin confirmé)
- Photos attachées aux points
- Notifications
- Toute logique offline (PWA, service workers, cache local) — hors scope de ce repo, géré uniquement côté mobile
