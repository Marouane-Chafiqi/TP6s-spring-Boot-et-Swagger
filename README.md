# TP6 : Student Management (Spring Boot, MySQL et Swagger)

Petite application de gestion d'étudiants développée avec Spring Boot. Elle expose une API REST qui permet d'ajouter, lister, compter et supprimer des étudiants, et de savoir combien d'étudiants sont nés chaque année. Les données sont stockées dans une base MySQL, et l'API est documentée et testable depuis Swagger.

## Sommaire

1. Technologies utilisées
2. Création du projet et dépendances
3. Configuration de la base de données
4. Entité et repository
5. Service et contrôleur REST
6. Lancement de l'application
7. Test des endpoints
8. Tests unitaires
9. Documentation avec Swagger
10. Problème rencontré

## 1. Technologies utilisées

- Java 17
- Spring Boot 3.2.0 (Web, Data JPA, Validation, DevTools)
- MySQL via WampServer
- springdoc-openapi 2.3.0 pour Swagger
- JUnit 5 et Mockito pour les tests unitaires
- Maven et IntelliJ IDEA

## 2. Création du projet et dépendances

Le projet a été généré avec Spring Initializr, avec les paramètres suivants :

- Project : Maven
- Language : Java
- Group : `com.example`
- Artifact : `student-management`
- Packaging : Jar

Les dépendances principales du `pom.xml` sont `spring-boot-starter-web` pour les contrôleurs REST, `spring-boot-starter-data-jpa` pour la persistance, `spring-boot-starter-validation`, `spring-boot-devtools`, `mysql-connector-j` pour la connexion à MySQL, `springdoc-openapi-starter-webmvc-ui` pour Swagger et `spring-boot-starter-test` pour les tests.

Structure du projet :

```
src/main/java/com/example/demo
    StudentManagementApplication.java
    entities/Student.java
    repositories/StudentRepository.java
    services/StudentService.java
    controllers/StudentController.java
src/main/resources/application.properties
src/test/java/com/example/demo/StudentControllerTest.java
```

Chaque couche a un rôle précis. L'entité décrit la table, le repository parle à la base, le service porte la logique métier et le contrôleur expose les routes HTTP.

## 3. Configuration de la base de données

Le fichier `src/main/resources/application.properties` contient la connexion à MySQL :

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/studentdb?createDatabaseIfNotExist=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=

spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.hibernate.ddl-auto=update
```

Avec WampServer, l'utilisateur `root` n'a pas de mot de passe par défaut. La base `studentdb` est créée automatiquement au premier lancement grâce à `createDatabaseIfNotExist=true`, et Hibernate crée la table `student` à partir de l'entité grâce à `ddl-auto=update`.

## 4. Entité et repository

L'entité `Student` représente un étudiant avec son identifiant, son nom, son prénom et sa date de naissance. L'identifiant est généré automatiquement par la base.

```java
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String nom;

    private String prenom;

    @Temporal(TemporalType.DATE)
    private Date dateNaissance;
}
```

Le repository `StudentRepository` étend `JpaRepository`, ce qui donne accès aux opérations de base sans écrire de SQL. Une requête JPQL personnalisée compte les étudiants par année de naissance :

```java
@Query("SELECT YEAR(s.dateNaissance), COUNT(s) FROM Student s GROUP BY YEAR(s.dateNaissance)")
Collection<Object[]> findNbrStudentByYear();
```

## 5. Service et contrôleur REST

`StudentService` fait le lien entre le contrôleur et le repository. Il propose l'enregistrement, la suppression (qui renvoie `false` si l'identifiant n'existe pas), la liste, le comptage et la répartition par année.

`StudentController` expose les routes sous le préfixe `/students` :

| Méthode | URL | Rôle | Code attendu |
|---------|-----|------|--------------|
| POST | `/students/save` | Ajouter un étudiant | 201 |
| GET | `/students/all` | Lister tous les étudiants | 200 |
| GET | `/students/count` | Compter les étudiants | 200 |
| GET | `/students/byYear` | Nombre d'étudiants par année de naissance | 200 |
| DELETE | `/students/delete/{id}` | Supprimer un étudiant | 204 ou 404 |

## 6. Lancement de l'application

1. Démarrer WampServer et attendre que l'icône devienne verte.
2. Ouvrir le projet dans IntelliJ (`File > Open`, puis choisir le dossier qui contient le `pom.xml`).
3. Lancer la classe `StudentManagementApplication`.

Dans la console, Hibernate crée la table `student`, puis le message `Started StudentManagementApplication` confirme que l'application a démarré :

![Création de la table par Hibernate et démarrage de l'application](https://github.com/user-attachments/assets/6a5faacd-435a-4965-b1e1-4c0170c5466c)

Dans phpMyAdmin, la table `student` apparaît dans la base `studentdb` :

![Table student dans phpMyAdmin](https://github.com/user-attachments/assets/fcf37dbb-bdff-4c64-97b8-67d9f2bbe577)

Tant qu'aucun étudiant n'a été ajouté, la requête `SELECT * FROM student` ne renvoie aucun résultat :

![Table student vide avant les tests](https://github.com/user-attachments/assets/bafa1caf-6826-43d0-8bda-b656ca01f7dc)

## 7. Test des endpoints

Les endpoints ont été testés depuis l'interface Swagger, disponible à l'adresse :

```
http://localhost:8080/swagger-ui.html
```

La page liste toutes les routes du contrôleur :

![Page d'accueil de Swagger](https://github.com/user-attachments/assets/591c4930-f8bf-49ad-b721-ee386dbc0723)

### Ajouter un étudiant (POST)

Dans `POST /students/save`, après avoir cliqué sur Try it out, on saisit le corps de la requête :

```json
{
  "nom": "CHAFIQI",
  "prenom": "Marouane",
  "dateNaissance": "2004-09-18"
}
```

![Corps de la requête POST](https://github.com/user-attachments/assets/f13b811e-ac65-43e7-994a-eb8d68d83b30)

Le serveur répond avec le code 201 et renvoie l'étudiant enregistré, avec son identifiant généré :

![Réponse 201 du serveur](https://github.com/user-attachments/assets/942e5911-c2d1-434c-9aaa-75af1660af17)

La suite de la réponse montre les codes et les formats documentés par Swagger :

![Suite de la réponse du POST](https://github.com/user-attachments/assets/5b16178d-2c11-401b-b43c-02b63b4a7680)

### Compter les étudiants (GET)

`GET /students/count` renvoie le nombre total d'étudiants enregistrés, ici 2, car la requête d'ajout a été envoyée deux fois.

![Appel de /students/count](https://github.com/user-attachments/assets/231ad0bd-f69a-491d-b9f1-aaf045f9fe48)

![Détail de la réponse de /students/count](https://github.com/user-attachments/assets/16cc69bc-693b-4289-bfd6-93deb14fc02c)

### Répartition par année de naissance (GET)

`GET /students/byYear` renvoie des paires année et effectif. Ici, deux étudiants sont nés en 2004.

![Appel de /students/byYear](https://github.com/user-attachments/assets/5984a761-a1ce-4397-9c31-7b3886f0be6b)

![Détail de la réponse de /students/byYear](https://github.com/user-attachments/assets/695c460a-7ff4-4362-9c7c-ed73e4ec7cee)

### Lister les étudiants (GET)

`GET /students/all` renvoie la liste complète. On retrouve les deux enregistrements créés, avec les identifiants 1 et 2.

![Liste des étudiants](https://github.com/user-attachments/assets/34833b09-6f4b-4eef-aadb-b43f43e028f2)

### Supprimer un étudiant (DELETE)

`DELETE /students/delete/{id}` supprime l'étudiant dont l'identifiant est passé dans l'URL. Le serveur répond 204 si la suppression a eu lieu et 404 si l'identifiant n'existe pas.

## 8. Tests unitaires

Les tests du contrôleur se trouvent dans `StudentControllerTest`. Le service est simulé avec Mockito, donc ces tests ne demandent ni base de données ni serveur. Ils couvrent l'ajout, la suppression (avec et sans résultat), la liste, le comptage et la répartition par année.

Pour les lancer, faire un clic droit sur `StudentControllerTest` dans IntelliJ puis Run, ou utiliser Maven :

```
mvn test
```

## 9. Documentation avec Swagger

La dépendance `springdoc-openapi-starter-webmvc-ui` génère automatiquement la documentation de l'API à partir des contrôleurs. Chaque méthode du contrôleur est décrite avec l'annotation `@Operation`, ce qui rend la documentation lisible directement dans Swagger. Elle permet aussi de tester chaque route depuis le navigateur, sans passer par un outil externe comme Postman.

