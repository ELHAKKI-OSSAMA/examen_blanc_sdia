# 🎓 Examen blanc SDIA — Architecture Microservices

![Java](https://img.shields.io/badge/Java-17-orange?logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.4.0-6DB33F?logo=springboot&logoColor=white)
![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-2024.0.0-6DB33F?logo=spring&logoColor=white)
![Keycloak](https://img.shields.io/badge/Keycloak-OIDC-4D4D4D?logo=keycloak&logoColor=white)
![Angular](https://img.shields.io/badge/Angular-client-DD0031?logo=angular&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-blue.svg)

Application microservices complète (sujet d'examen blanc SDIA) : gestion de **conférences** et
**keynotes**, avec **Config Server**, **Eureka**, **API Gateway**, sécurité **OAuth2/Keycloak**
et un front **Angular**.

> 📄 Compte rendu détaillé : [`Comte_Rendu.pdf`](./Comte_Rendu.pdf)

---

## 🏛️ Architecture

```
                         ┌────────────────┐
        Angular (4200) ─►│  Gateway :8888  │─► routes vers les services
                         └───────┬─────────┘
                                 │ (service discovery)
                    ┌────────────┴───────────┐
              ┌─────▼─────┐            ┌──────▼──────┐
              │ keynote   │◄───Feign───│ conference  │
              │  :8081    │            │   :8082     │
              └───────────┘            └─────────────┘
        Config Server :8088     Discovery (Eureka) :8761     Keycloak :8080 (realm exam-realm)
```

| Service | Port | Rôle |
|---------|------|------|
| `config-service` | 8088 | **Spring Cloud Config Server** (configuration centralisée) |
| `discovery-service` | 8761 | **Eureka Server** (annuaire de services) |
| `gateway-service` | 8888 | **Spring Cloud Gateway** (routage + CORS) |
| `keynote-service` | 8081 | Gestion des keynotes (JPA/H2, resource server JWT) |
| `conference-service` | 8082 | Gestion des conférences ; appelle keynote via **OpenFeign** ; resource server JWT |
| `angular-front-app` | 4200 | Client Angular |

Sécurité : chaque service métier est un **resource server JWT** (realm Keycloak `exam-realm`),
`JwtAuthConverter` mappe les rôles, et `FeignInterceptor` propage le jeton entre services.

---

## 🚀 Démarrage

Ordre recommandé : **config → discovery → gateway → keynote → conference**.

```bash
cd config-service    && ./mvnw spring-boot:run
cd discovery-service && ./mvnw spring-boot:run
cd gateway-service   && ./mvnw spring-boot:run
cd keynote-service   && ./mvnw spring-boot:run
cd conference-service && ./mvnw spring-boot:run
cd angular-front-app && npm install && ng serve
```

Prérequis : **JDK 17+**, **Keycloak** sur `:8080` (realm `exam-realm`).

> ⚙️ **Config Server** : le dépôt Git de configuration est paramétrable via la variable
> d'environnement `CONFIG_REPO_URI` (par défaut `file://${user.home}/config-repo`).
>
> Les services métier démarrent aussi de façon autonome (`config`/`discovery` désactivés par
> défaut, base **H2** en mémoire) ; `./mvnw test` s'exécute **sans Keycloak**.

---

## 🛠️ Corrections & améliorations apportées

- 🔧 **Alignement Java 21 → 17** sur les 5 services (JDK cible disponible). **Les 5 services
  compilent et leurs tests passent.**
- 🧹 Suppression d'un **scaffold IntelliJ résiduel** à la racine (pom `examem_blanc` + classe
  `Main` « Hello world »).
- 🌍 **Config Server portable** : remplacement d'un chemin Windows absolu codé en dur
  (d'un autre utilisateur) par une URI surchargeable via `CONFIG_REPO_URI`.
- 📄 Ajout de `.gitignore`, `LICENSE` et de ce README (architecture, ports, guide de lancement).

---

## 👤 Auteur

**EL HAKKI Ossama** — Master SDIA, ENSET.

## 📄 Licence

Distribué sous licence **MIT**. Voir [`LICENSE`](LICENSE).
