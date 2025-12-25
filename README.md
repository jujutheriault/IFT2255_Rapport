## Description du projet

L’application de gestion d’horaire scolaire vise à aider les étudiants à prendre des décisions éclairées dans le choix de leurs cours. Elle leur permet de consulter des informations complètes et structurées pour chaque cours, incluant la description, les prérequis et co-requis,ainsi que des avis d’étudiants ayant déjà suivi ces cours. Les étudiants ont également accès à des statistiques académiques agrégées, telles que la moyenne de classe, le nombre d’inscriptions et le taux d’échec.

Il y a aussi la possibilité d’affiner les résultats selon différents critères, notamment le sigle, le titre, la description ou le trimestre. L’outil permet également aux étudiants de vérifier leur éligibilité à un cours en fonction de leur cycle d’études et des exigences académiques.

Enfin, l’application propose une fonctionnalité de comparaison de plusieurs cours, permettant de visualiser clairement les différences entre les options possibles et d’évaluer la charge de travail globale d’une combinaison de cours, afin d’aider l’étudiant à faire le meilleur choix possible.

## Liste des fonctionnalités de l'application par rôle
**Étudiant**
- **Rechercher des cours** avec plusieurs critères :
  - Par **sigle** (ex: IFT2255)
  - Par **nom** (ex: génie logiciel)
  - Par **description** (mots-clés : ajax, java, algorithmes, etc.)
  - Par **trimestre** (H25, A24, E24)
- **Consulter les informations détaillées** d'un cours :
  - Description complète
  - Nombre de crédits
  - Prérequis et corequis
  - Sessions disponibles (Automne, Hiver, Été)
    
- **Visualiser les résultats agrégés** :
  - Moyenne de classe
  - Score moyen sur 5.0
  - Nombre de participants total
  - Données sur plusieurs trimestres

- **Consulter les avis** d'étudiants ayant suivi le cours :
  - Note moyenne des étudiants (sur 5 étoiles)
  - La charge de travail réelle
  - La qualité de l'enseignement
  - La difficulté du cours
    
- **Poster des avis sur des cours déjà suivis**
  
- **Vérifier automatiquement** si vous êtes éligible à un cours selon :
  - Votre cycle d'études (Baccalauréat, Maîtrise, Doctorat)
  - Le cycle requis pour le cours

- **Comparer plusieurs cours**
  - **Visualisation synthétique** incluant : Sigles et noms des cours, Nombre de crédits de chaque cours, Charge de travail totale (somme des crédits)
    
- **Afficher l'horaire**

**Bot Discord**
- **Collecter et soumettre des avis étudiants à l'API REST**

## Oracle de tests
Pour chaque test, nous définissons les entrées, les sorties attendues et les effets de bord sur le système. Les tableaux ci-dessous présentent l'oracle de 12 de nos tests unitaires.

**Tests dans UserControllerTest.java**

| **Entrée** | **Sortie attendue** | **État après l'appel** | **Type** | **Description** |
|:-----------|:-------------------|:----------------------|:---------|:----------------|
| **Test 1 : `getAllUsers()`**<br>`mockService.getAllUsers()` retourne<br>`[User(1,"Alice","alice@email.com"),`<br>`User(2,"Bob","bob@email.com")]` | Liste JSON :<br>`[{"id":1,"name":"Alice",...},`<br>`{"id":2,"name":"Bob",...}]`<br>Status HTTP 200 | Aucun changement<br>(lecture seule) | **Succès** | Si le service retourne une liste d'utilisateurs, le contrôleur retourne cette liste en JSON |
| **Test 2 : `getUserById()` - Succès**<br>`pathParam("id") = "1"`<br>`mockService.getUserById(1)` retourne<br>`Optional.of(User(1,"Alice","alice@email.com"))` | JSON :<br>`{"id":1,"name":"Alice","email":"alice@email.com"}`<br>Status HTTP 200 | Aucun changement<br>(lecture seule) | **Succès** | Si l'utilisateur existe dans le système, retourne l'utilisateur en JSON |
| **Test 3 : `getUserById()` - Not Found**<br>`pathParam("id") = "1"`<br>`mockService.getUserById(1)` retourne<br>`Optional.empty()` | JSON :<br>`{"error":"Aucun utilisateur ne correspond à l'ID: 1"}`<br>Status HTTP 404 | Aucun changement | **Échec** | Si l'utilisateur n'existe pas, retourne status 404 avec message d'erreur |
| **Test 4 : `getUserById()` - ID invalide**<br>`pathParam("id") = "abc"` | JSON :<br>`{"error":"ID invalide"}`<br>Status HTTP 400 | Aucun changement<br>`service.getUserById()` non appelé | **Échec** | Si l'ID n'est pas numérique, lance `NumberFormatException`, retourne status 400 |
| **Test 5 : `createUser()`**<br>Body HTTP :<br>`User(null,"Alice","alice@email.com")` | JSON de l'utilisateur créé<br>Status HTTP 201 | `service.createUser(user)` appelé<br>Utilisateur ajouté au système | **Succès** | Si les données sont valides, crée l'utilisateur et retourne status 201 |
| **Test 6 : `deleteUser()` - Succès**<br>`pathParam("id") = "1"` | Aucun contenu<br>Status HTTP 204 | `service.deleteUser(1)` appelé<br>Utilisateur ID=1 supprimé | **Succès** | Si l'ID est valide, supprime l'utilisateur et retourne status 204 |
| **Test 7 : `deleteUser()` - ID invalide**<br>`pathParam("id") = "abc"` | JSON :<br>`{"error":"ID invalide"}`<br>Status HTTP 400 | Aucun changement<br>`service.deleteUser()` non appelé | **Échec** | Si l'ID n'est pas numérique, retourne status 400, aucune suppression |

---

**Tests dans EnsembleControllerTest.java**

| **Entrée** | **Sortie attendue** | **État après l'appel** | **Type** | **Description** |
|:-----------|:-------------------|:----------------------|:---------|:----------------|
| **Test 1 : `createEnsemble()` - Création**<br>`pathParam("idEnsemble") = "ENS-1"`<br>(ensemble n'existe pas) | JSON :<br>`{"id":"ENS-1","list":[]}`<br>Status HTTP 201 | `ensembles = {"ENS-1" -> EnsembleCours(id="ENS-1", list=[])}` | **Succès** | Si l'ensemble n'existe pas, le crée avec une liste vide et retourne status 201 |
| **Test 2 : `createEnsemble()` - Déjà existant**<br>`pathParam("idEnsemble") = "ENS-1"`<br>(ensemble existe déjà) | JSON :<br>`{"error":"Ensemble déjà existant"}`<br>Status HTTP 400 | `ensembles` inchangé<br>(contient déjà "ENS-1") | **Échec** | Si l'ensemble existe déjà, refuse la création et retourne status 400 |
| **Test 3 : `addCourse()` - Ensemble inexistant**<br>`pathParam("idEnsemble") = "ENS-NonExistent"`<br>`pathParam("courseId") = "IFT-1015"` | JSON :<br>`{"error":"Ensemble non trouvé"}`<br>Status HTTP 404 | Aucun changement | **Échec** | Si l'ensemble n'existe pas, retourne status 404, aucun cours ajouté |
| **Test 4 : `addCourse()` - ID cours invalide**<br>`pathParam("idEnsemble") = "ENS-1"`<br>`pathParam("courseId") = "A"`<br>(< 6 caractères) | JSON :<br>`{"error":"ID du cours invalide"}`<br>Status HTTP 400 | Ensemble "ENS-1" inchangé<br>`list = []` | **Échec** | Si l'ID du cours est trop court (< 6 car.), retourne status 400 |
| **Test 5 : `addCourse()` - Succès**<br>`pathParam("idEnsemble") = "ENS-1"`<br>`pathParam("courseId") = "IFT-1015"`<br>(≥ 6 caractères) | JSON :<br>`{"id":"ENS-1","list":["IFT-1015"]}`<br>Status HTTP 200 | `ensembles.get("ENS-1").getList() = ["IFT-1015"]` | **Succès** | Si l'ensemble existe et l'ID cours valide, ajoute le cours et retourne ensemble mis à jour |


## Organisation du repertoire
Le projet est organisé comme suit : <br>
docs/Images/ : Contient toutes les images et diagrammes. <br>
docs/besoins/ : contient les pages modifiés.<br>
cas-utilisations.md <br>
exigences.md <br>
flux-principaux.md <br>
glossaire.md <br>
index.md <br>
risques.md <br>

## Prérequis

Assurez-vous d’avoir les outils suivants installés :

- Python **3.11** ou plus récent
- `pip` (gestionnaire de paquets Python)
- `pipenv` ou équivalent (gestion d’environnement virtuel) 
  - Évite de polluer votre système et les conflits de version.
  - Installez-le avec `pip install pipenv`.


## Utilisation

> Avant toute utilisation, assurez-vous que l’environnement virtuel est activé (`pipenv shell`).

### Déploiement

Pour déployer automatiquement le site sur GitHub Pages (branche `gh-pages`)

```bash
mkdocs gh-deploy
```

> Cette commande pousse automatiquement le contenu du site sur la branche `gh-pages`. Si la branche n'existe pas, elle est crée automatiquement.

## Structure du projet

- `docs/` : Contient tous les fichiers Markdown du site
- `mkdocs.yml` : Configuration de MkDocs
- `requirements.txt` : Dépendances Python
- `site/` : Site généré (créé lors de la construction) -- *optionnel*

## Personnalisation

1. Modifiez `mkdocs.yml` pour changer la configuration du site
2. Ajoutez/modifiez les fichiers Markdown (`.md`) dans `docs/`
3. Personnalisez le thème en modifiant les paramètres dans `mkdocs.yml`

## Licence

Ce projet est sous licence MIT. Voir le fichier `LICENSE` pour plus de détails.

## Ressources utiles

- Documentation officielle MkDocs
- Thème Material for MkDocs
