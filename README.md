# jenkins-project_exam

## Description
Projet Spring Boot avec intégration Jenkins, Docker et déploiement automatisé.

## Technologies
- Java 17
- Spring Boot 2.4.5
- Maven
- Docker
- Jenkins
aazaz
## Utilisation
```bash
mvn clean package
docker build -t spring-boot-demo .
docker run -p 8080:8080 spring-boot-demo
```
aaae
## CI/CD
Le projet utilise Jenkins pour l'intégration continue avec :
- Build Maven
- Tests automatisés
- Construction d'image Docker
- Push vers Docker Hub
- Déploiement automatique
