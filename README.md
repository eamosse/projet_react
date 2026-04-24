# Mini-projet — CineTrack

**Durée : 3 semaines** · **Équipes : 3 à 4 étudiants** · **Agents IA : autorisés (voir règles dédiées)** · **Livraison : repo Git + démo live 10 min + Q&A 10 min**

>> Les dates de rendu et de soutenances vous seront communiquées par la direction

## 1. Contexte

Vous allez concevoir et réaliser **CineTrack**, une application web qui permet à un utilisateur de tenir le journal de son visionnage : suivre les films et séries qu'il veut voir, qu'il a vus, les noter, rédiger des avis, et consulter des statistiques personnelles. L'application s'appuie sur l'API publique **TMDB** (The Movie Database) pour les données de films.

Ce projet est volontairement **plus ambitieux que Chirpy**. Puisque vous avez le droit d'utiliser des agents (Claude Code, Cursor, Copilot, v0…), l'échelle a été calibrée pour que la valeur ajoutée humaine — architecture, choix produit, UX, débogage, compréhension — reste le cœur du travail. **Un groupe qui se contente de "prompter" une solution complète s'en sortira mal à la soutenance.**

## 2. Objectifs pédagogiques

- Concevoir une application React de taille réaliste, de bout en bout.
- Défendre vos choix techniques à l'oral (architecture, découpage, dépendances).
- Collaborer en équipe : Git, branches, PR, revues, découpe des tickets.
- Évaluer, encadrer et vérifier la production d'un agent IA plutôt que la subir.
- Mesurer la qualité (UX, perf, accessibilité) plutôt que l'affirmer.

## 3. Stack imposée

- **React 18** + **TypeScript strict** (pas de `any` non justifié, pas de `@ts-ignore` sans commentaire).
- **Vite** pour le bundler.
- **TanStack Query** pour toute donnée serveur (aucun `useEffect` pour fetcher).
- **React Router v6** pour la navigation.
- **Backend** : au choix — (a) Express + JWT à la manière de Chirpy, ou (b) une alternative BaaS (Supabase, PocketBase, Firebase…) à condition de la **justifier dans `DECISIONS.md`**. Dans tous les cas, auth et persistance doivent fonctionner.
- **Persistance** : SQLite (`better-sqlite3`) ou fichier JSON côté Express, ou la base du BaaS choisi.
- **Styles** : CSS Modules, vanilla CSS avec variables, ou Tailwind. **Interdit** : une librairie de composants tout-faits (MUI, Chakra, Ant Design, shadcn complet…). Vous devez écrire vos composants. Vous *pouvez* utiliser des libs ciblées pour une feature précise : `recharts` pour des graphes, `@dnd-kit` pour du drag & drop, `react-window` pour la virtualisation, etc.

## 4. Fonctionnalités obligatoires (socle — ~70 % de la note technique)

### 4.1 Comptes & authentification

- Inscription, connexion, déconnexion.
- Session persistée (localStorage ou cookie, au choix, à justifier).
- Routes protégées : `/library`, `/stats`, `/settings` inaccessibles sans session.
- Route publique de profil : `/u/:username` consultable par tous (ou même déconnecté).

### 4.2 Recherche de films (TMDB)

- Champ de recherche global (dans le header) avec **debounce 300 ms**.
- Affichage d'une liste de résultats avec poster, titre, année, note TMDB.
- Gestion propre des états : `idle` / `loading` / `empty` / `error` / `success`.
- Fiche détaillée `/movie/:tmdbId` : synopsis, durée, genres, casting principal, note.

### 4.3 Ma bibliothèque

- Ajouter un film à sa bibliothèque avec un **statut** : `à voir`, `en cours`, `vu`, `abandonné`.
- Noter un film (0-10) et rédiger un avis texte (markdown basique accepté).
- Liste `/library` avec :
  - Filtres par statut, genre, année, note minimale.
  - Tri (date d'ajout, titre, note perso).
  - Recherche full-text sur ma bibliothèque (debounced, côté client acceptable).
- Modifier / supprimer une entrée de bibliothèque.

### 4.4 Statistiques personnelles

- Page `/stats` avec au moins **trois visualisations** (compteurs ou graphes — `recharts` autorisé) :
  - Nombre de films par mois sur les 12 derniers mois.
  - Répartition par genre (bar ou pie).
  - Temps de visionnage cumulé (somme des durées des films avec statut `vu`).
- Calculs côté client avec `useMemo`, pas de recalcul à chaque render.

### 4.5 Profil public

- `/u/:username` accessible sans auth, affiche le pseudo, l'avatar, la bio, et la liste publique des films `vu` avec la note perso.
- Un utilisateur peut rendre sa bibliothèque privée (toggle dans `/settings`) — dans ce cas, `/u/:username` affiche un message explicite.

### 4.6 Thème clair / sombre

- Toggle dans le header, persisté.
- Implémenté via `data-theme` + variables CSS (pas de double classe `.dark` partout).

### 4.7 Qualité générale

- Responsive utilisable de 360 px à 1440 px.
- Layout cohérent, navigation claire, états vides soignés (pas de page blanche).
- `npm run build` vert. `npm run typecheck` vert. ESLint sans warning sur le code que vous avez écrit.

## 5. Fonctionnalités optionnelles (choisir **au moins 3** — ~20 % de la note technique)

Chaque groupe **doit** en livrer au minimum trois, avec une difficulté cumulée ≥ 5 étoiles. Choisissez pour montrer votre profil.

| # | Feature | Complexité |
|---|---|---|
| O1 | **Recommandations** : films suggérés à partir de mes genres favoris (API TMDB `/discover` + filtrage de ce que j'ai déjà) | ⭐⭐ |
| O2 | **Listes thématiques partageables** : je crée "Mes 10 films cyberpunk", URL publique `/list/:id` | ⭐⭐ |
| O3 | **Import / export JSON** de ma bibliothèque (pour migrer ou sauvegarder) | ⭐ |
| O4 | **PWA** : manifest + service worker, installable, fiches consultables offline | ⭐⭐⭐ |
| O5 | **Social** : follow d'autres utilisateurs + feed d'activité des gens que je suis | ⭐⭐⭐ |
| O6 | **Commentaires publics** sur les reviews des autres (avec modération basique) | ⭐⭐ |
| O7 | **Pagination infinie** sur la recherche TMDB avec `useInfiniteQuery` + IntersectionObserver | ⭐⭐ |
| O8 | **Virtualisation** (`react-window`) de la bibliothèque quand > 500 entrées, avec preuve chiffrée du gain | ⭐⭐ |
| O9 | **Accessibilité** : score Lighthouse a11y ≥ 95, support clavier complet, live regions, tests avec lecteur d'écran (capture à l'appui) | ⭐⭐⭐ |
| O10 | **Tests** : Vitest + React Testing Library, au moins un test d'intégration sur un flux critique (auth ou ajout bibliothèque) + tests unitaires sur 3 hooks custom | ⭐⭐ |
| O11 | **Déploiement CI/CD** : preview par PR via Vercel/Netlify/Cloudflare Pages, workflow GitHub Actions qui bloque sur typecheck/build/lint | ⭐⭐ |
| O12 | **Drag & drop** pour réordonner une liste thématique (`@dnd-kit`) | ⭐⭐ |
| O13 | **Notifications toast** maison, avec file, auto-dismiss, actions | ⭐ |
| O14 | **Recherche avancée** : filtres combinés (genre + année + note TMDB) côté TMDB avec sync URL querystring | ⭐⭐ |
| O15 | **Feature surprise** : proposez la vôtre dans `DECISIONS.md`, acceptée si elle apporte une compétence réelle | variable |

## 6. Organisation d'équipe

### Rôles suggérés (pas imposés)

Les 3-4 membres se répartissent par affinité. Exemples :

- **Lead architecture / DX** : setup, types partagés, lint, scripts, CI, DECISIONS.md.
- **Backend / API** : endpoints, auth, persistance, seed, contrats de données.
- **Frontend / routing / state** : layout, router, context d'auth, React Query, hooks custom.
- **UX / data viz / polish** : design, thèmes, composants réutilisables, stats, accessibilité, tests.

### Règles de collaboration

- **Branches + PR** obligatoires. Aucun push direct sur `main`. Chaque PR doit être **revue** par au moins un autre membre avant merge.
- Chaque membre ouvre **au moins une PR par semaine** et fait au moins **une revue par semaine**. Le graph de contribution sera examiné.
- **Kanban** tenu à jour (GitHub Projects, Linear, Notion — au choix). Les tickets sont dans l'outil, pas seulement dans vos têtes.
- **Commits descriptifs** (format `type: message`, cf. Conventional Commits — pas imposé, mais cohérent).
- Un fichier **`DECISIONS.md`** à la racine : journal chronologique (date + paragraphe) des choix techniques structurants et de leurs raisons. *Exemple d'entrée attendue : "2026-05-04 — On passe de context pur à Zustand pour le thème parce que 3 composants distincts lisent/écrivent, la re-render rate explose en dark-mode animé."*
- Un fichier **`CONTRIBUTIONS.md`** listant qui a fait quoi, avec lien vers les PR principales.

## 7. Règles sur les agents IA

- **Autorisés sans restriction** : Claude Code, Cursor, Copilot, v0, ChatGPT, Gemini, Windsurf…
- **Vous restez responsables de votre code.** À la soutenance, *n'importe quel membre doit pouvoir expliquer n'importe quelle partie de la codebase* — y compris celle qu'il n'a pas écrite. 
- Dans `DECISIONS.md`, signalez explicitement **les décisions prises avec un agent** et pourquoi vous les avez validées (ou contre-validées).
- **Plagiat entre groupes = 0**, même si c'est un agent qui a copié. Vous êtes responsables de ce que vous committez.
- Règle d'or : **l'agent code, vous comprenez**. Si un membre ne comprend pas un morceau de code du projet, c'est au groupe de lui expliquer avant la soutenance — pas à l'enseignant pendant.

## 8. Calendrier

### Semaine 1 — Fondations

- Repo, setup Vite/TS, ESLint, Prettier, CI minimal.
- Design system minimal : palette, typo, spacings, composants `<Button>`, `<Input>`, `<Card>`.
- Routing + layout (Header, Footer, pages vides).
- Auth bout-en-bout : inscription, login, logout, session persistée, route protégée.
- Modèles de données et premiers endpoints : users, library entries.
- **Jalon M1 (fin S1)** : un utilisateur peut s'inscrire, se connecter, et ajouter **un** film mocké dans sa bibliothèque. Déployé en preview ou lançable en local sans friction.

### Semaine 2 — Cœur applicatif

- Intégration TMDB (clé API via variable d'env, JAMAIS commit).
- Recherche, fiche détaillée, ajout/édition/suppression dans la bibliothèque.
- Liste filtrable et triable.
- Statistiques.
- Thème clair/sombre.
- **Jalon M2 (fin S2)** : toutes les features obligatoires marchent sur le cas nominal. 

### Semaine 3 — Polish & ambition

- Features optionnelles choisies.
- Edge cases : erreurs réseau, tokens expirés, entrée dupliquée, input vide, offline.
- Performance : passage Profiler, au moins **une optimisation mesurée** (avant/après, captures).
- Accessibilité de base : focus management, alt, aria minimal, navigation clavier.
- Déploiement public OU script de démo local impeccable.
- Préparation de la démo (storyboard, comptes de démo, scénario).
- **Jalon M3 (fin S3)** : livraison + démo + Q&A.

## 9. Livrables

1. **Repo Git** (lié à l'assignation).
2. **`README.md`** : pitch en 3 lignes, stack, installation, lancement, variables d'env, URL déployée si applicable, captures d'écran.
3. **`DECISIONS.md`** : journal des choix techniques.
4. **`CONTRIBUTIONS.md`** : qui a fait quoi, lien vers PR.
5. **Build fonctionnel** : déployé OU `npm install && npm run dev` impeccable sur une machine neuve en < 5 min.
6. **Démo live** de 10 min + **Q&A** de 10 min.

## 10. Évaluation (sur 20)

Grille volontairement construite pour **résister à la "sous-traitance totale" aux agents** : la moitié de la note dépend de ce que vous expliquez en direct et de décisions observables sur le processus, pas juste du produit final.

| Critère | Points | Détail |
|---|---|---|
| **Compréhension en live** | **/6** | Pendant la Q&A, chaque membre explique une partie qu'il **n'a pas codée**. Questions type : "pourquoi ce hook custom ?", "que se passe-t-il si le token expire pendant ce mutate ?", "comment tu gérerais ce bug si je le reproduisais là ?". Chaque non-réponse ou réponse vague coûte. |
| **Qualité du code** | **/4** | Typage strict, découpage en composants et hooks lisibles, pas de duplication flagrante, nommage, structure du repo. Pénalités pour dépendances non justifiées. |
| **UX et polish** | **/3** | États de chargement, messages d'erreur utiles, états vides soignés, responsive, feedback après action, accessibilité de base. |
| **Performance mesurée** | **/2** | **Une** optimisation documentée avec preuve : capture DevTools Profiler ou Lighthouse avant/après, dans `DECISIONS.md`. Pas de `React.memo` saupoudrés sans profil. |
| **Processus et collaboration** | **/3** | Commits réguliers et équilibrés, PR avec revues réelles, Kanban tenu, `DECISIONS.md` vivant, répartition lisible dans l'historique Git. |
| **Ambition & créativité** | **/2** | Features optionnelles choisies et réussies, ou apport original (design, feature inédite, qualité au-dessus du lot). |

**Bonus (jusqu'à +2)** : accessibilité Lighthouse ≥ 95 sur 3 pages, suite de tests non triviale, déploiement CI avec preview par PR, PWA réellement installable.

**Malus** :
- Agent non compris en soutenance : -1 par question non répondue (plafonné).
- Secret TMDB committé : -2 (et rotation immédiate de la clé).
- `any` ou `@ts-ignore` non justifié : -0,25 chacun.
- `useEffect` pour fetcher : -1 par occurrence (la formation entière dit non).

## 11. Ressources

- **TMDB** — inscription gratuite : <https://www.themoviedb.org/signup>, docs API : <https://developer.themoviedb.org/reference/intro/getting-started>. Rate limit très large, images servies via CDN.
- **Alternatives** si TMDB bloque (proxy d'entreprise, etc.) : OMDb API, ou dataset local exporté depuis TMDB. À justifier dans `DECISIONS.md`.
- **Recharts** pour les graphes : <https://recharts.org>.
- **@dnd-kit** pour drag & drop : <https://dndkit.com>.
- **react-window** pour virtualiser : <https://github.com/bvaughn/react-window>.
- **Backend Chirpy** : référence de pattern Express + JWT à réutiliser ou adapter.
- **Snippets de cours** : tous les decks `slides/*.md` sont des sources légitimes. Les solutions `solutions/jour-N/` aussi — mais ne les recopiez pas sans comprendre, on le verra.

---

Bon travail. L'objectif n'est pas de tout faire : c'est de faire propre, de comprendre ce que vous livrez, et de le défendre.
