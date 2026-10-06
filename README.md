# Introduction étape par étape au framework mobile Ionic

📖 **Lire le tutoriel : [https://stahe.github.io/ionic-etape-par-etape-oct-2026/](https://stahe.github.io/ionic-etape-par-etape-oct-2026/)**

Ce cours vous apprend à écrire une application **mobile** avec le framework [Ionic](https://ionicframework.com) 9, associé à [Angular](https://angular.dev) 22 et à [Capacitor](https://capacitorjs.com) 8 : une application Android, dont les écrans sont fabriqués **sur le téléphone** à partir des données JSON d'un serveur. C'est une application web (HTML, CSS, TypeScript) que Capacitor emballe dans une application Android : le même code tourne aussi dans un navigateur.

Il reprend le plan du cours [Introduction étape par étape au framework mobile Flutter](https://stahe.github.io/flutter-etape-par-etape-sept-2026/) (et des cours [React](https://stahe.github.io/react-etape-par-etape-sept-2026/), [Vue.js](https://stahe.github.io/vuejs-etape-par-etape-sept-2026/) et [Angular](https://stahe.github.io/angular-etape-par-etape-sept-2026/)) : même serveur, mêmes écrans, écrits à la manière d'Ionic, pour un téléphone.

| Cours Flutter | Cours Ionic |
|---|---|
| le langage Dart | TypeScript |
| le moteur de Flutter dessine chaque pixel | des composants web à l'aspect natif (Material Design sur Android), affichés par la WebView du téléphone |
| des widgets, une méthode `build()`, `setState` | des composants Angular, des gabarits HTML, des signaux (`signal`, `computed`, `effect`) |
| `InheritedWidget`, provider (`ChangeNotifier`) | l'injection de dépendances d'Angular, des services à signaux |
| `Form` et `FormField` | les formulaires réactifs d'Angular |
| go_router, `ShellRoute` + `NavigationBar` | le routeur d'Angular, `ion-tabs`, des gardes `CanActivateFn` |
| `Future`, `Stream` | `Promise`, `Observable` (RxJS), `resource()` |
| `shared_preferences` | `@capacitor/preferences` |
| un client HTTP qui gère les cookies (sur le téléphone) | `HttpClient` ; sur le téléphone, le greffon CapacitorHttp range et renvoie le cookie |

Le serveur, lui, ne change pas : c'est le serveur JSON de l'application **RdvMedecins** déjà utilisé par les clients Flutter, React, Vue.js et Angular.

## L'approche : de nombreux petits exemples, puis une étude de cas

Le cours s'articule autour de **25 petits exemples**, chacun centré sur une notion, qui portent les mêmes numéros (et montrent les mêmes écrans) que ceux des cours Flutter, React, Vue.js et Angular. Ils forment un seul projet Ionic : une seule commande `npm install`, puis `npm start` ; on choisit l'exemple dans le menu de l'application (ou par son adresse : http://localhost:8100/ex05).

| Chapitre | Contenu | Exemples |
|---|---|---|
| Premiers pas | un projet Ionic + Angular + Capacitor, les composants d'Ionic, les signaux et les valeurs calculées, les gabarits (`@if`, `@for`), les événements, tous les types de champs, un réducteur, la validation, les formulaires réactifs, la mise en forme (pipes, `Intl`) | 01–09 |
| Les composants | entrées (`input`), sorties (`output`, `model`), projection de contenu (`ng-content`, `ng-template`), un tableau générique, cycle de vie, injection de dépendances, directives et animations, fenêtre de confirmation (`AlertController`) | 10–16 |
| Le routage | le routeur d'Angular avec Ionic : routes, paramètres, `ion-tabs`, chargement différé, page introuvable, gardes | 17–18 |
| L'asynchrone et l'état partagé | `Promise`, `Observable`, générateurs asynchrones, minuteries, anti-rebond, réponses périmées (`switchMap`), `resource()`, services à signaux, `@capacitor/preferences`, thème sombre | 19–20 |
| Internationalisation | dictionnaires JSON, paramètres, pluriels (`Intl.PluralRules`), dates, montants, calendrier traduit (`ion-datetime`) | 21 |
| Le serveur, une boîte noire | installation du serveur JSON, son API, 48 exemples `curl`, ce qui change pour un client mobile | – |
| Dialoguer avec le serveur | `HttpClient`, le proxy de développement, l'adresse du serveur (émulateur, téléphone, web), CapacitorHttp et le cookie du jeton, une couche d'accès à l'API, un modèle de vue, les erreurs du serveur attachées aux champs | 22–25 |

Chaque exemple est présenté avec son code complet, commenté ligne par ligne, et des copies d'écran de son exécution (fabriquées automatiquement par des scripts [Playwright](https://playwright.dev), dossiers `captures/`).

## Le serveur : une boîte noire

Le serveur est le serveur NestJS des cours précédents, dont les contrôleurs renvoient du **JSON**. Le cours le traite comme une **boîte noire** : on l'installe, on étudie son API, on l'interroge avec `curl` — mais on n'a pas besoin de lire son code (fourni et commenté pour les curieux).

- toutes les erreurs ont la même forme : `{ "statusCode": 409, "cle": "ERRORS.LOGIN_TAKEN", "params": {...}, "champs": {...} }` — des **clés** de traduction, jamais de texte ;
- authentification par jeton JWT dans un cookie `httpOnly` : dans le navigateur, c'est le navigateur qui le garde ; sur le téléphone, c'est le greffon CapacitorHttp ;
- un « mode test » du captcha pour pouvoir interroger l'API avec `curl` (et fabriquer les copies d'écran automatiquement).

## L'étude de cas : le client Ionic de RdvMedecins

Une application complète de **prise de rendez-vous dans un cabinet médical**, dont **tous** les fichiers sont listés et commentés.

- **Ionic et Angular modernes** : composants autonomes, signaux, application sans zone.js, nouveau flux de contrôle des gabarits, `inject()`, intercepteur HTTP fonctionnel, gardes `CanActivateFn`, une architecture en couches (accès à l'API, état, interface).
- **Trois rôles** : `ADMIN` (gère les médecins et les clients), `DOCTOR` (prend et annule les rendez-vous), `USER` (le patient : réserve pour lui-même, gère son compte).
- **Confidentialité** : un patient ne reçoit jamais le nom des autres patients — le serveur ne l'envoie pas.
- **Tout l'état de l'écran dans son adresse** : `/agenda?idMedecin=1&jour=2026-10-13&reserver=7` ; sur le téléphone, le bouton Retour ferme la fenêtre de réservation ; sur le web, F5 et Précédent / Suivant fonctionnent.
- **Validation par le serveur** : les formulaires affichent sous chaque champ les erreurs renvoyées par l'API ; verrou optimiste, homonymes, login déjà pris...
- **Session** : rétablie au démarrage (`GET /api/auth/moi`), expiration gérée en un seul endroit (réponse 401, dans l'intercepteur).
- **Adapté au téléphone** : menu en tiroir (`ion-menu`), permanent sur grand écran (`ion-split-pane`), listes plutôt que tableaux.
- **Français / anglais**, y compris le calendrier.
- **Déploiement** : la version web compilée, servie par le serveur JSON lui-même, et l'application Android (APK).

## Le contenu du dépôt

```
exemples_ionic/            les 25 petits exemples (un seul projet Ionic)
rdvmedecins-nestjs-json/   le serveur JSON RdvMedecins (la « boîte noire »)
rdvmedecins_ionic/         le client Ionic de l'étude de cas
```

Le dossier `android/` de chaque projet n'est pas fourni : il est créé par `npx cap add android` (cf. le cours).

## Technologies

Ionic 9 · Angular 22 · Capacitor 8 · TypeScript 6 · RxJS 7 · @capacitor/app · @capacitor/preferences · ionicons · Playwright (copies d'écran) · côté serveur : NestJS 10 · TypeORM · MySQL 8 / MariaDB · Passport JWT · svg-captcha

## Prérequis

- Les bases de TypeScript et d'Angular, présentées dans les cours [Introduction au langage TypeScript par l'exemple](https://stahe.github.io/typescript-sept-2026/) et [Introduction étape par étape au framework Angular](https://stahe.github.io/angular-etape-par-etape-sept-2026/) ; les notions utilisées sont cependant rappelées au fil des exemples.
- Node.js 24 (ou 22.22.3 et plus, exigé par Angular CLI 22), Visual Studio Code et ses extensions Angular Language Service et Ionic, Chrome, Android Studio (pour le SDK Android et l'émulateur) ou un téléphone Android ; un serveur MySQL (par exemple Laragon sous Windows) pour le serveur JSON. Les instructions d'installation sont données dans le cours.

## Auteur

Ce cours, ses exemples, le client Ionic et l'adaptation du serveur JSON ont été rédigés par **Claude**, l'IA d'[Anthropic](https://www.anthropic.com) (octobre 2026), à la demande de **Serge Tahé**.
