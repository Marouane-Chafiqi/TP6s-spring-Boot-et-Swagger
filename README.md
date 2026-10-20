TP6 : Student Management

Petite application de gestion d'étudiants développée avec Spring Boot. Elle expose une API REST qui permet d'ajouter, lister, compter et supprimer des étudiants, et de savoir combien d'étudiants sont nés chaque année. Les données sont stockées dans une base MySQL, et l'API est documentée et testable depuis Swagger.

## Technologies

- Java 17
- Spring Boot 3.2.0 (Web, Data JPA, Validation, DevTools)
- MySQL (via WampServer)
- springdoc-openapi 2.3.0 pour Swagger
- JUnit 5 et Mockito pour les tests unitaires
- Maven, IntelliJ IDEA

## Structure du projet

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

## Prérequis

- JDK 17 ou supérieur
- IntelliJ IDEA (Maven est intégré)
- WampServer avec MySQL démarré sur le port 3306

## Configuration

Le fichier `src/main/resources/application.properties` contient la connexion à la base :

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/studentdb?createDatabaseIfNotExist=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=

spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.hibernate.ddl-auto=update
```

Avec WampServer, l'utilisateur `root` n'a pas de mot de passe par défaut. La base `studentdb` est créée automatiquement au premier lancement grâce à `createDatabaseIfNotExist=true`, et Hibernate crée la table `student` tout seul.

## Lancer l'application

1. Démarrer WampServer et attendre que l'icône devienne verte.
2. Ouvrir le projet dans IntelliJ (`File > Open`, puis choisir le dossier qui contient le `pom.xml`).
3. Lancer la classe `StudentManagementApplication`.

Si tout va bien, la console affiche une ligne du type `Started StudentManagementApplication in X seconds`.

<img width="1152" height="496" alt="Capture d&#39;écran 2026-10-20 175525" src="https://github.com/user-attachments/assets/6a5faacd-435a-4965-b1e1-4c0170c5466c" />


Au démarrage, Hibernate crée la table `student` avec ses colonnes :

<img width="679" height="91" alt="Capture d&#39;écran 2026-10-20 175619" src="https://github.com/user-attachments/assets/fcf37dbb-bdff-4c64-97b8-67d9f2bbe577" />

Sur phpMyAdmin, la table apparaît dans la base `studentdb` :

<img width="402" height="107" alt="Capture d&#39;écran 2026-10-20 175645" src="https://github.com/user-attachments/assets/bafa1caf-6826-43d0-8bda-b656ca01f7dc" />


## Les endpoints

| Méthode | URL | Rôle | Code attendu |
|---------|-----|------|--------------|
| POST | `/students/save` | Ajouter un étudiant | 201 |
| GET | `/students/all` | Lister tous les étudiants | 200 |
| GET | `/students/count` | Compter les étudiants | 200 |
| GET | `/students/byYear` | Nombre d'étudiants par année de naissance | 200 |
| DELETE | `/students/delete/{id}` | Supprimer un étudiant | 204 ou 404 |

## Tester avec Swagger

Une fois l'application lancée, l'interface Swagger est disponible à l'adresse suivante :

```
http://localhost:8080/swagger-ui.html
```

Elle liste toutes les routes du contrôleur et permet de les appeler directement depuis le navigateur.

<img width="1113" height="518" alt="Capture d&#39;écran 2026-10-20 175942" src="https://github.com/user-attachments/assets/591c4930-f8bf-49ad-b721-ee386dbc0723" />


### Ajouter un étudiant

Dans `POST /students/save`, après avoir cliqué sur Try it out, on saisit le corps de la requête :

```json
{
  "nom": "CHAFIQI",
  "prenom": "Marouane",
  "dateNaissance": "2004-09-18"
}
```

<img width="1024" height="462" alt="Capture d&#39;écran 2026-10-20 180050" src="https://github.com/user-attachments/assets/f13b811e-ac65-43e7-994a-eb8d68d83b30" />

<img width="984" height="408" alt="Capture d&#39;écran 2026-10-20 180121" src="https://github.com/user-attachments/assets/942e5911-c2d1-434c-9aaa-75af1660af17" />

<img width="986" height="241" alt="Capture d&#39;écran 2026-10-20 180130" src="https://github.com/user-attachments/assets/5b16178d-2c11-401b-b43c-02b63b4a7680" />

<img width="961" height="515" alt="Capture d&#39;écran 2026-10-20 180201" src="https://github.com/user-attachments/assets/231ad0bd-f69a-491d-b9f1-aaf045f9fe48" />

<img width="990" height="381" alt="Capture d&#39;écran 2026-10-20 180211" src="https://github.com/user-attachments/assets/16cc69bc-693b-4289-bfd6-93deb14fc02c" />

<img width="976" height="515" alt="Capture d&#39;écran 2026-10-20 180228" src="https://github.com/user-attachments/assets/5984a761-a1ce-4397-9c31-7b3886f0be6b" />

<img width="962" height="375" alt="Capture d&#39;écran 2026-10-20 180237" src="https://github.com/user-attachments/assets/695c460a-7ff4-4362-9c7c-ed73e4ec7cee" />

<img width="981" height="515" alt="Capture d&#39;écran 2026-10-20 180302" src="https://github.com/user-attachments/assets/34833b09-6f4b-4eef-aadb-b43f43e028f2" />


