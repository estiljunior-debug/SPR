[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/hwkFGDeh)
# TP Spring Boot - Gestion des Étudiants

## 📚 Objectif du TP

Dans ce TP, vous allez développer une application web complète de gestion d'étudiants en utilisant Spring Boot. Vous apprendrez à :
- Modéliser des entités Java
- Créer des repositories pour la gestion des données en mémoire
- Développer des services métier
- Exposer des APIs REST avec des controllers

## 🎯 Entités à modéliser

Votre application devra gérer les entités suivantes :

### 1. **Étudiant**
- `id` : identifiant unique (Long)
- `nom` : nom de famille (String)
- `prenom` : prénom (String)
- `email` : adresse email (String)
- `dateNaissance` : date de naissance (LocalDate)
- `promotion` : promotion à laquelle l'étudiant appartient (Promotion)

### 2. **Professeur**
- `id` : identifiant unique (Long)
- `nom` : nom de famille (String)
- `prenom` : prénom (String)
- `email` : adresse email (String)
- `specialite` : domaine de spécialité (String)

### 3. **Cours**
- `id` : identifiant unique (Long)
- `intitule` : nom du cours (String)
- `code` : code du cours (String)
- `nombreHeures` : nombre d'heures du cours (Integer)
- `professeur` : professeur responsable (Professeur)

### 4. **Promotion**
- `id` : identifiant unique (Long)
- `nom` : nom de la promotion (String)
- `annee` : année universitaire (Integer)
- `niveau` : niveau d'études (String, ex: "Licence 1", "Master 2")

### 5. **Note**
- `id` : identifiant unique (Long)
- `etudiant` : étudiant concerné (Etudiant)
- `cours` : cours évalué (Cours)
- `valeur` : note obtenue (Double)
- `dateExamen` : date de l'examen (LocalDate)

## 📁 Structure du projet

Organisez votre code selon l'architecture suivante :

```
src/main/java/ht/ueh/first/spring/student_management/
├── model/              # Entités (POJO)
│   ├── Etudiant.java
│   ├── Professeur.java
│   ├── Cours.java
│   ├── Promotion.java
│   └── Note.java
├── repository/         # Repositories (gestion des données)
│   ├── IEtudiantRepository.java        # Interface
│   ├── EtudiantRepository.java         # Implémentation
│   ├── IProfesseurRepository.java      # Interface
│   ├── ProfesseurRepository.java       # Implémentation
│   ├── ICoursRepository.java           # Interface
│   ├── CoursRepository.java            # Implémentation
│   ├── IPromotionRepository.java       # Interface
│   ├── PromotionRepository.java        # Implémentation
│   ├── INoteRepository.java            # Interface
│   └── NoteRepository.java             # Implémentation
├── service/           # Services métier
│   ├── IEtudiantService.java           # Interface
│   ├── EtudiantService.java            # Implémentation
│   ├── IProfesseurService.java         # Interface
│   ├── ProfesseurService.java          # Implémentation
│   ├── ICoursService.java              # Interface
│   ├── CoursService.java               # Implémentation
│   ├── IPromotionService.java          # Interface
│   ├── PromotionService.java           # Implémentation
│   ├── INoteService.java               # Interface
│   └── NoteService.java                # Implémentation
└── controller/        # Controllers REST
    ├── EtudiantController.java
    ├── ProfesseurController.java
    ├── CoursController.java
    ├── PromotionController.java
    └── NoteController.java
```

### ⚠️ Important : Programmation par Interface

Dans ce TP, vous **devez obligatoirement** :
1. Créer une **interface** pour chaque repository et service
2. Créer une **classe d'implémentation** pour chaque interface
3. Utiliser les interfaces dans vos injections de dépendances

**Pourquoi utiliser des interfaces ?**
- ✅ Facilite les tests unitaires
- ✅ Permet de changer l'implémentation sans modifier le code client
- ✅ Respecte les principes SOLID (notamment le principe d'inversion de dépendance)
- ✅ Bonne pratique professionnelle en Java/Spring

## ⚙️ Configuration de l'application

Avant de commencer le développement, vous devez configurer votre application Spring Boot.

### Modifier le fichier `application.properties`

Le fichier se trouve dans : `src/main/resources/application.properties`

Ajoutez les configurations suivantes :

```properties
# Configuration du port d'écoute
server.port=8081

# Configuration du context path
server.servlet.context-path=/student-management
```

#### Explications :

1. **`server.port`** : Change le port sur lequel l'application écoute (par défaut 8080)
   - Utilisez le port **8081** pour éviter les conflits avec d'autres applications

2. **`server.servlet.context-path`** : Définit le chemin de base de l'application
   - Toutes vos URLs commenceront par `/student-management`
   - Exemple : `http://localhost:8081/student-management/api/etudiants`

### ⚠️ Important

Avec cette configuration, vos endpoints seront accessibles à l'adresse :
```
http://localhost:8081/student-management/api/{entite}
```

Par exemple :
- `http://localhost:8081/student-management/api/etudiants`
- `http://localhost:8081/student-management/api/professeurs`
- `http://localhost:8081/student-management/api/cours`

## 🚀 Étapes de réalisation

### Étape 1 : Créer les entités (Model)

Créez des classes Java simples (POJO) dans le package `model`.

**Exemple pour la classe Etudiant :**

```java
package ht.ueh.first.spring.student_management.model;

import java.time.LocalDate;

public class Etudiant {
    private Long id;
    private String nom;
    private String prenom;
    private String email;
    private LocalDate dateNaissance;
    private Long promotionId; // Référence à la promotion
    
    // Constructeurs
    public Etudiant() {}
    
    public Etudiant(Long id, String nom, String prenom, String email, 
                    LocalDate dateNaissance, Long promotionId) {
        this.id = id;
        this.nom = nom;
        this.prenom = prenom;
        this.email = email;
        this.dateNaissance = dateNaissance;
        this.promotionId = promotionId;
    }
    
    // Getters et Setters
    // À compléter...
}
```

### Étape 2 : Créer les interfaces des repositories

Avant de créer les implémentations, définissez les **interfaces** de vos repositories.

**Exemple pour IEtudiantRepository :**

```java
package ht.ueh.first.spring.student_management.repository;

import java.util.List;
import java.util.Optional;

public interface IEtudiantRepository {
    Etudiant save(Etudiant etudiant);

    List<Etudiant> findAll();

    Optional<Etudiant> findById(Long id);

    void deleteById(Long id);
}
```

### Étape 3 : Créer les implémentations des repositories

Les repositories gèrent le stockage des données **en mémoire** (sans base de données).

**Exemple pour EtudiantRepository (implémentation de l'interface) :**

```java
package ht.ueh.first.spring.student_management.repository;

import org.springframework.stereotype.Repository;

import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import java.util.concurrent.atomic.AtomicLong;

@Repository
public class EtudiantRepository implements IEtudiantRepository {
    private final List<Etudiant> etudiants = new ArrayList<>();
    private final AtomicLong idGenerator = new AtomicLong(1);

    @Override
    public Etudiant save(Etudiant etudiant) {
        if (etudiant.getId() == null) {
            etudiant.setId(idGenerator.getAndIncrement());
            etudiants.add(etudiant);
        } else {
            // Mise à jour
            deleteById(etudiant.getId());
            etudiants.add(etudiant);
        }
        return etudiant;
    }

    @Override
    public List<Etudiant> findAll() {
        return new ArrayList<>(etudiants);
    }

    @Override
    public Optional<Etudiant> findById(Long id) {
        return etudiants.stream()
                .filter(e -> e.getId().equals(id))
                .findFirst();
    }

    @Override
    public void deleteById(Long id) {
        etudiants.removeIf(e -> e.getId().equals(id));
    }
}
```

⚠️ **Points importants :**
- La classe `EtudiantRepository` **implémente** l'interface `IEtudiantRepository`
- L'annotation `@Repository` est sur la classe d'implémentation, **pas sur l'interface**
- Utilisez `@Override` pour chaque méthode de l'interface

### Étape 4 : Créer les interfaces des services

Définissez les **interfaces** de vos services métier.

**Exemple pour IEtudiantService :**

```java
package ht.ueh.first.spring.student_management.service;

import java.util.List;
import java.util.Optional;

public interface IEtudiantService {
    Etudiant creerEtudiant(Etudiant etudiant);

    List<Etudiant> obtenirTousLesEtudiants();

    Optional<Etudiant> obtenirEtudiantParId(Long id);

    Etudiant modifierEtudiant(Long id, Etudiant etudiant);

    void supprimerEtudiant(Long id);
}
```

### Étape 5 : Créer les implémentations des services

Les services contiennent la logique métier de l'application.

**Exemple pour EtudiantService (implémentation de l'interface) :**

```java
package ht.ueh.first.spring.student_management.service;

import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

@Service
public class EtudiantService implements IEtudiantService {
    private final IEtudiantRepository etudiantRepository;

    public EtudiantService(IEtudiantRepository etudiantRepository) {
        this.etudiantRepository = etudiantRepository;
    }

    @Override
    public Etudiant creerEtudiant(Etudiant etudiant) {
        return etudiantRepository.save(etudiant);
    }

    @Override
    public List<Etudiant> obtenirTousLesEtudiants() {
        return etudiantRepository.findAll();
    }

    @Override
    public Optional<Etudiant> obtenirEtudiantParId(Long id) {
        return etudiantRepository.findById(id);
    }

    @Override
    public Etudiant modifierEtudiant(Long id, Etudiant etudiant) {
        etudiant.setId(id);
        return etudiantRepository.save(etudiant);
    }

    @Override
    public void supprimerEtudiant(Long id) {
        etudiantRepository.deleteById(id);
    }
}
```

⚠️ **Points importants :**
- La classe `EtudiantService` **implémente** l'interface `IEtudiantService`
- Le service dépend de l'**interface** `IEtudiantRepository`, pas de la classe concrète
- L'annotation `@Service` est sur la classe d'implémentation, **pas sur l'interface**
- Utilisez `@Override` pour chaque méthode de l'interface

### Étape 6 : Créer les controllers REST

Les controllers exposent les fonctionnalités via une API REST.

**Exemple pour EtudiantController :**

```java
package ht.ueh.first.spring.student_management.controller;

import ht.ueh.first.spring.student_management.service.IEtudiantService;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/etudiants")
public class EtudiantController {
    private final IEtudiantService etudiantService;

    public EtudiantController(IEtudiantService etudiantService) {
        this.etudiantService = etudiantService;
    }

    // GET /api/etudiants - Récupérer tous les étudiants
    @GetMapping
    public List<Etudiant> obtenirTous() {
        return etudiantService.obtenirTousLesEtudiants();
    }

    // GET /api/etudiants/{id} - Récupérer un étudiant par ID
    @GetMapping("/{id}")
    public ResponseEntity<Etudiant> obtenirParId(@PathVariable Long id) {
        return etudiantService.obtenirEtudiantParId(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    // POST /api/etudiants - Créer un nouvel étudiant
    @PostMapping
    public ResponseEntity<Etudiant> creer(@RequestBody Etudiant etudiant) {
        Etudiant nouveau = etudiantService.creerEtudiant(etudiant);
        return ResponseEntity.status(HttpStatus.CREATED).body(nouveau);
    }

    // PUT /api/etudiants/{id} - Modifier un étudiant
    @PutMapping("/{id}")
    public ResponseEntity<Etudiant> modifier(@PathVariable Long id,
                                             @RequestBody Etudiant etudiant) {
        Etudiant modifie = etudiantService.modifierEtudiant(id, etudiant);
        return ResponseEntity.ok(modifie);
    }

    // DELETE /api/etudiants/{id} - Supprimer un étudiant
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> supprimer(@PathVariable Long id) {
        etudiantService.supprimerEtudiant(id);
        return ResponseEntity.noContent().build();
    }
}
```

⚠️ **Point important :**
- Le controller dépend de l'**interface** `IEtudiantService`, pas de la classe concrète
- Spring injecte automatiquement l'implémentation appropriée

## 🔧 Fonctionnalités à implémenter

Pour chaque entité, créez les opérations CRUD suivantes dans l'API :

| Méthode HTTP | Endpoint | Description |
|--------------|----------|-------------|
| GET | `/api/{entite}` | Récupérer toutes les instances |
| GET | `/api/{entite}/{id}` | Récupérer une instance par ID |
| POST | `/api/{entite}` | Créer une nouvelle instance |
| PUT | `/api/{entite}/{id}` | Modifier une instance existante |
| DELETE | `/api/{entite}/{id}` | Supprimer une instance |

### Fonctionnalités avancées (bonus)

1. **Recherche avancée :**
   - `GET /api/etudiants?nom={nom}` : rechercher des étudiants par nom
   - `GET /api/cours?professeur={professeurId}` : trouver les cours d'un professeur

2. **Statistiques :**
   - `GET /api/etudiants/{id}/moyenne` : calculer la moyenne d'un étudiant
   - `GET /api/cours/{id}/statistiques` : statistiques d'un cours (moyenne, min, max)

3. **Relations :**
   - `GET /api/promotions/{id}/etudiants` : lister les étudiants d'une promotion
   - `GET /api/etudiants/{id}/notes` : lister les notes d'un étudiant

## 🧪 Tester votre API

### Démarrer l'application

L'application démarre sur `http://localhost:8081/student-management`

### Tester avec PostMan

**Créer un étudiant :**
```bash
POST http://localhost:8081/student-management/api/etudiants \
  {
    "nom": "Dupont",
    "prenom": "Jean",
    "email": "jean.dupont@example.com",
    "dateNaissance": "2000-05-15",
    "promotionId": 1
  }
```

**Récupérer tous les étudiants :**
```bash
GET http://localhost:8081/student-management/api/etudiants
```

**Récupérer un étudiant par ID :**
```bash
GET http://localhost:8081/student-management/api/etudiants/1
```

**Modifier un étudiant :**
```bash
PUT http://localhost:8081/student-management/api/etudiants/1 \
  -H "Content-Type: application/json" \
  {
    "nom": "Dupont",
    "prenom": "Jean-Pierre",
    "email": "jp.dupont@example.com",
    "dateNaissance": "2000-05-15",
    "promotionId": 1
  }
```

**Supprimer un étudiant :**
```bash
DELETE http://localhost:8081/student-management/api/etudiants/1
```

## 📝 Livrables attendus

1. **Code source complet** avec toutes les entités, repositories, services et controllers
2. **Tests manuels** : démonstration du fonctionnement de l'API
3. **Documentation** : 
   - Liste complète des endpoints disponibles
   - Exemples de requêtes/réponses pour chaque endpoint

## 💡 Conseils

- **Commencez toujours par créer l'interface avant l'implémentation** (bonne pratique)
- Commencez par une entité simple (ex: Promotion) avant de passer aux entités avec relations
- Testez chaque couche (repository, service, controller) au fur et à mesure
- **N'oubliez jamais** d'ajouter `implements NomInterface` dans vos classes
- **Utilisez toujours l'interface** lors de l'injection de dépendances (pas la classe concrète)
- Utilisez l'annotation `@RestController` pour les controllers REST
- Utilisez `@Service` pour les services (sur la classe d'implémentation)
- Utilisez `@Repository` pour les repositories (sur la classe d'implémentation)
- Utilisez `@Override` pour chaque méthode qui implémente une interface
- N'oubliez pas les constructeurs, getters et setters dans vos entités
- Gérez les erreurs (ex: étudiant non trouvé → erreur 404)

## 🔍 Annotations Spring Boot importantes

| Annotation | Usage |
|------------|-------|
| `@SpringBootApplication` | Classe principale de l'application |
| `@RestController` | Indique qu'une classe est un controller REST |
| `@Service` | Indique qu'une classe est un service |
| `@Repository` | Indique qu'une classe est un repository |
| `@RequestMapping` | Définit le chemin de base d'un controller |
| `@GetMapping` | Gère les requêtes HTTP GET |
| `@PostMapping` | Gère les requêtes HTTP POST |
| `@PutMapping` | Gère les requêtes HTTP PUT |
| `@DeleteMapping` | Gère les requêtes HTTP DELETE |
| `@PathVariable` | Extrait une variable du chemin de l'URL |
| `@RequestBody` | Extrait le corps de la requête HTTP |

## ✅ Critères d'évaluation

- [ ] Le fichier `application.properties` est correctement configuré (port et context path)
- [ ] **Toutes les interfaces sont créées** (IEtudiantRepository, IEtudiantService, etc.)
- [ ] **Toutes les classes implémentent leurs interfaces respectives**
- [ ] **Les injections de dépendances utilisent les interfaces, pas les classes concrètes**
- [ ] Toutes les entités sont correctement modélisées
- [ ] Les repositories gèrent les données en mémoire
- [ ] Les services contiennent la logique métier
- [ ] Les controllers exposent les APIs REST
- [ ] Toutes les opérations CRUD sont implémentées
- [ ] L'API répond correctement aux requêtes HTTP
- [ ] Le code est propre et bien organisé
- [ ] Les bonnes annotations Spring Boot sont utilisées
- [ ] L'annotation `@Override` est utilisée dans toutes les méthodes qui implémentent une interface

## 🗂️ Tâches pour les étudiants

Ci-dessous une checklist ordonnée que chaque étudiant/groupe doit suivre pour réaliser le TP. Cochez les éléments au fur et à mesure.

1. Préparation
   - [ ] Cloner le dépôt et ouvrir le projet dans votre IDE (IntelliJ / VS Code)
   - [ ] Vérifier/mettre à jour `src/main/resources/application.properties` :
     - `server.port=8081`
     - `server.servlet.context-path=/student-management`

2. Modélisation des entités
   - [ ] Créer les POJOs dans `src/main/java/.../model/` : `Etudiant`, `Professeur`, `Cours`, `Promotion`, `Note`
   - [ ] Ajouter constructeurs, getters et setters pour chaque classe
   - [ ] Pour les relations, utilisez des références par identifiant (ex: `promotionId`) pour simplifier

3. Repositories (par interface)
   - [ ] Définir une interface `I<Entity>Repository` pour chaque entité dans `repository/`
   - [ ] Implémenter la classe concrète (ex: `EtudiantRepository`) qui gère le stockage en mémoire
   - [ ] Annoter l'implémentation avec `@Repository`

4. Services (par interface)
   - [ ] Définir une interface `I<Entity>Service` pour chaque entité dans `service/`
   - [ ] Implémenter le service (ex: `EtudiantService`) et injecter le repository via l'interface
   - [ ] Annoter l'implémentation avec `@Service` et utiliser `@Override` pour les méthodes

5. Controllers REST
   - [ ] Créer un controller pour chaque entité dans `controller/` (ex: `EtudiantController`)
   - [ ] Exposer les endpoints CRUD suivants :
     - GET `/api/{entite}`
     - GET `/api/{entite}/{id}`
     - POST `/api/{entite}`
     - PUT `/api/{entite}/{id}`
     - DELETE `/api/{entite}/{id}`
   - [ ] Retourner les statuts HTTP appropriés (200, 201, 204, 404)

6. Tests et vérification
   - [ ] Démarrer l'application et tester les endpoints avec Postman / curl
   - [ ] Vérifier la persistance en mémoire et le comportement des mises à jour
   - [ ] Gérer les cas d'erreur (ex: ressource non trouvée → 404)

7. Documentation finale et rendu
   - [ ] Compléter le README : lister tous les endpoints et fournir des exemples de requêtes/réponses
   - [ ] Préparer une brève note expliquant les choix techniques et les difficultés rencontrées
   - [ ] Faire des commits clairs et bien commentés

8. Bonus (optionnel)
   - [ ] Recherches avancées (ex: `GET /api/etudiants?nom={nom}`)
   - [ ] Endpoints de statistiques (moyenne, min, max pour un cours)
   - [ ] Relations supplémentaires (ex: `GET /api/promotions/{id}/etudiants`)

Conseils pratiques :
- Travaillez par petites itérations : modélisation → repository → service → controller
- Utilisez systématiquement les interfaces pour l'injection de dépendances
- Écrivez des tests manuels et commentez vos commits

---

**Bon courage ! 🚀**
