

# Architecture du système
## Vue d’ensemble

La conception du système utilise une architecture structurée autour d'une séparation claire entre modèle, contrôleurs et vues (MVC), ce qui favorise une compréhension simple du fonctionnement global et une possible évolution simplifiée du logiciel. Elle adopte une architecture en couches exposant une API REST.

 Cet API REST est développée de  manière orienté objet avec une approche modulaire. Le choix de ce type d'architecture correspond permet de répondre aux exigences du client surtout en ce qui concerne la clarté de l'application et puisque notre application doit s'occuper de la gestion de plusieurs entités différentes.

De plus, l'application est facile à tester avec un logiciel comme Postman, ce qui donne une réduction du coût de validation. L'interface du client est aussi séparée du backend ''serveur'', cette indépendance assure un couplage faible entre la vue et le controlleur. 
La séparation en modules permet aussi un couplage faible et une forte cohésion. 

Il est aussi plus facile de détecter les problèmes d'une partie du code avec cette approche contrairement à une architecture monolithique.Une API REST utilise aussi le protocole d'appels HTTP qui fourni une cache intégrée, ce qui améliore les performances de l'application.L'application est aussi plus stable puisque ce type d'architecture est utilisé partout.

## Composants principaux
### Module d’authentification:
  - **Classes clés :** `AuthentificationController`, `VueAuthentification`, `Utilisateur`
  - **Rôle :** Gestion de la connexion/déconnexion à la plateforme, créé une session utilisateur et accès aux fonctionnalités de la plateforme. 
  - **Responsabilité :** vérifier les identifiants, créer la session utilisateur.
 
### Gestion des utilisateurs:
  - **Classes clés :** `Utilisateur`, `Étudiant`, `ProfilController`, `VueProfil`
  - **Rôle :** Gestion du profil étudiant (rpogramme, préférences, filtres, cours effectués)
  - **Responsabilités :** Encapsuler les données et oopérations qui sont liées aux utilisateurs et fournir des opérsations pour modifier ou sauvegarder le profil.

### Interface (frontend)
  - **Classes clés :** `VueProfil`, `VueSelectionCours`,`VueRechrcherCours`,`VueConsulterCours`,`VueComparaisonCours`, `Menu`, `App`
  - **Rôle :** Présentation des informations et interaction avec l'utilisateur (affiché les listes de cours, formulaire de recherche, tableau de comparaison, etc)
  - **Responsabilités :** Appeler les contrôleurs via l'API REST, récupérer les résultats et les afficher dans l'interface 

### API backend
  - **Classes clés :** Contrôleurs métiers (`SelectionController`, `RechecrherCoursController`, `ConsulterCoursController`, `ComparaisonController`) et modèle de domaine (`Cours`, `SelectionCours`, `RechercheCours`, `RésultatsAgrégées`, `Avis`, `Horaire`, etc)
  - **Rôle :** Implémenter la logique applicative (Sélection, recherche, comparason, calcul de charge de travail, Éligibilté, etc)
  - **Responsabilités :** Intégrer les sources de données 

## Communication entre composants
Les échanges entre les composantes se font quasi exclusivement sous forme d'appels HTTP sauf pour quelques appels entre le bot et discord qui se font en format Websocket. Les données sont en format JSON.

**Mécanismes d’échange : appels HTTP**
- Principalement GET, POST, PUT/DELETE
- Chaque vues invoque les endpoints correspondants.




**Format des données : JSON**
Les entiers métiers (par exemple, `Cours`, `Étudiant`, `RésultatsAgrégées`,`TableauComparaison`) sont sérialisés en JSON et renvoyés au client, ce qui facilite l'interopérabilité avec d'Autres clients ou services potentiels (par exemple, Discord, Les résultats scolaires, etc).

## Diagramme d’architecture (Modèle C4)
![Alt text](../Images/C1.jpg)
![Alt text](../Images/C2.jpg)
![Alt text](../Images/C3.jpg)
