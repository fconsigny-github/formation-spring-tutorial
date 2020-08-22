# formation-spring-tutorial <img src="https://huongdanjava.com/wp-content/uploads/2016/10/spring-boot.png" title="Spring" alt="Spring" width="25" height="25"/>



Tutoriel sur l'utilisation de Spring avec SpringBoot 


---

## Table des matières

- [Prérequis](#Prérequis)
- [Création du projet](#Création)
- [Maven Cycle de vie](#Maven)
- [Instance des beans et Scopes](#Instance)
- [Couche Data avec Hibernate (Spring Data JPA) ](#Couche) 
- [API RESTFUL](#API)
- [Application Web (Spring Web MVC)](#Application)
- [Sécurité (Spring security)](#Sécurité)
- [Documentation avec Swagger](#Documentation)
- [Migration database avec FlywayDB](#Migration)
---

## Prérequis 

Pour ce tutoriel vous aurez besoin
* Maven en ligne de commande  
* Java JDK 8 
* Postman ou SOAP UI 
* un IDE 
 
## Création du projet 

###### Création dans Git 
 * Créer un repository dans http://github.com en prennant soin de créer également un fichier README.md 
 * Cloner le projet en local, privilégier le clone en ssh plutot qu'en https.  

###### Création du projet avec maven 
```cmd
mvn archetype:generate -DgroupId=nom-du-package -DartifactId=nom-de-l-application -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

###### Maven (Cycle de vie)

* **mvn clean:** supprime le répertoire target 
* **mvn validate:** vérifie que la configuration du projet est correcte (Analyse le pom.xml)
* **mvn compile:** compile les sources du projet 
* **mvn test:** exécute les tests unitaires 
* **mvn package:** créer le livrable, JAR/WAR/EAR selon ce qui a été configuré dans la balise <package></package> du pom.xml 
* **mvn install:** déploie le livrable dans le repository local (.m2/repository)
* **mvn deploy:** déploie le livrable dans le repository distant

Pour rappel, tous les paramètres de configuration de maven se situent dans le répertoire **maven/conf/settings.xml**

###### Ajouter le parent Springboot
> Editer le fichier pom.xml 
```xml
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.3.RELEASE</version>
    <relativePath />
  </parent>
```

La dernière version du starter Springboot se trouve sur Maven Central : https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent

###### Ajouter le plugin Maven 

Le plugin maven est nécessaire pour créer un livrable executable

> Editer le fichier pom.xml 
```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
```

###### Configurer le point d'entré de l'application 

```java
package com.excilys.formation.spring.rest;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * Application Entry point
 *
 */
@SpringBootApplication
public class App 
{
    public static void main( String[] args )
    {
        SpringApplication.run(App.class, args);
    }
}

```

###### Démarrer le projet
 > Compiler pour générer le répertoire target
```cmd
mvn install
```
> Nettoyer le répertoire target s'il est déjà présent
```cmd
mvn clean install
```

> Démarrer l'application 
```cmd
java -jar target/nom-du-jar
```

###### Logs de démarrage

> Une fois l'application configurée, compilée, les logs suivant doivent apparaitre

```cmd
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.2.2.RELEASE)

2020-08-19 12:34:58.048  INFO 11044 --- [           main] com.excilys.formation.spring.rest.App    : Starting App v1.0-SNAPSHOT on DESKTOP-H83Q6V0 with PID 11044 (C:\Users\franc\Documents\projects\formations\formation-banking-api\BankingApi\target\BankingApi-1.0-SNAPSHOT.jar started by franc in C:\Users\franc\Documents\projects\formations\formation-banking-api\BankingApi)
2020-08-19 12:34:58.054  INFO 11044 --- [           main] com.excilys.formation.spring.rest.App    : No active profile set, falling back to default profiles: default
2020-08-19 12:34:59.791  INFO 11044 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2020-08-19 12:34:59.814  INFO 11044 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2020-08-19 12:34:59.816  INFO 11044 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.29]
2020-08-19 12:34:59.916  INFO 11044 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2020-08-19 12:34:59.916  INFO 11044 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1771 ms
2020-08-19 12:35:00.174  INFO 11044 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2020-08-19 12:35:00.408  INFO 11044 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2020-08-19 12:35:00.411  INFO 11044 --- [           main] com.excilys.formation.spring.rest.App    : Started App in 2.991 seconds (JVM running for 3.551)
```

> Sont visibles
* Le nom du jar exécuté : v1.0-SNAPSHOT
* Le processus créé : PID 11044 
* Le profil lancé :  No active profile set, falling back to default profiles: default
* Le serveur d'application et le port utilisé : Tomcat initialized with port(s): 8080 (http)
* Le context de l'application :  with context path ''
* Le temps de démarrage de l'application :  Started App in 2.991 seconds (JVM running for 3.551)


## Scopes

Pour comprendre comment s'instancie les beans, vous allez créer une classe qui permet de lister tous les beans présents dans le contexte de Spring 

Importez tout d'abord la librairie SL4J 
```xml
 <!-- Logger -->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.30</version>
    </dependency>
```


créez dans un nouveau package une classe SpringBeanViewer 
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;

import java.util.Arrays;
import java.util.List;

@Component 
public class SpringBeanViewer {

    Logger logger = LoggerFactory.getLogger(SpringBeanViewer.class);

    SpringBeanViewer(ApplicationContext applicationContext) {
        List<String> beanList = Arrays.asList(applicationContext.getBeanDefinitionNames());
        beanList.forEach(bean -> logger.info(bean));
    }

}
```

Recompilez et redémarrez l'application. 
Vous constaterez qu'en annotant @Component, la classe est chargée, et s'éxecute lors du chargement du context de spring.  

Annotez ensuite la class avec un scope  
```java
@Component
@Scope("session")
public class SpringBeanViewer
```

Recompilez et redémarrez l'application. 
Vous constaterez qu'en modifiant le scope d'un composant, la classe n'est pas chargée dans le contexte de spring.

Changez le scope avec la valeur singleton 
```java
@Component
@Scope("singleton")
```

Redémarrez l'application. 

Editer la classe SpringBeanViewer 
```java
@Component
@Scope("session")
public class SpringBeanViewer {

    Logger logger = LoggerFactory.getLogger(SpringBeanViewer.class);

    SpringBeanViewer(ApplicationContext applicationContext) {
        List<String> beanList = Arrays.asList(applicationContext.getBeanDefinitionNames());
        beanList.forEach(bean -> logger.info(bean));
    }

    public void getSpringBeanAnnotation() { }

}
```

Ajouter la class InfoController dans un package controller:

```java
import com.excilys.formation.spring.rest.config.SpringBeanViewer;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Scope;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@Scope("session")
public class InfoController {

    SpringBeanViewer springBeanViewer;

    @GetMapping("/context-infos")
    public ResponseEntity getInfosSession() {
        springBeanViewer.getSpringBeanAnnotation();
        return ResponseEntity.accepted().build();
    }

    @Autowired
    public void setBeanSession(SpringBeanViewer springBeanViewer) {
        this.springBeanViewer = springBeanViewer;
    }
```

Redémarrez l'application,  constatez les logs de démarrage. 

Tapez ensuite dans une url sur le navigateur de votre choix http://localhost:8080/context-infos/. 
Constatez les logs. 

## Couche Data avec Hibernate (Spring Data JPA) 

Hibernate est un ORM (object relationnal mapping). Il permet de faciliter l'utiliser des POJOs en facilitant les requêtes, les créations des objets et des contraintes entre les différentes Table. 

Pour ce POC nous utiliserons une base H2. H2 est une base en mémoire utilisée en générale pour créer des tests unitaires et/ou des POCs. 

###### Installation de H2 

Ajoutez dans le pom.xml la dépendance à H2 

```xml
 <!-- Datasource H2 -->
    <dependency>
      <groupId>com.h2database</groupId>
      <artifactId>h2</artifactId>
      <scope>runtime</scope>
    </dependency>
```

Puis créez un fichier de properties application.properties. 
Ce fichier servira à configurer toutes nos variables d'environnement. 
Ajoutez la configuration suivante 

```yml
# Enabling H2 Console
# Enables the H2 console on http://localhost:8080/h2-console after startup.
spring.h2.console.enabled=true
spring.datasource.username=excilys
spring.datasource.password=croissants

# Sets the platform for use during database initialisation
spring.datasource.platform=h2

# Stops jpa from reinitialising your database. I’m going to be using the scripts.
#spring.jpa.hibernate.ddl-auto=update

# Dialect Definition
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect

# Hibernate Logs
logging.level.org.hibernate.SQL=DEBUG

spring.datasource.url=jdbc:h2:mem:testdb;
```

Redémarrez l'application et connectez vous à l'url http://localhost:8080/h2-console 

###### Création d'un modèle persisté en base

Avant toute chose, ajouez le starter spring data jpa 
```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
```

relancez l'application. Constatez les logs. Vous remarquerez la présence de nouveaux logs 
```
 HikariPool-1 - Starting...
2020-08-19 16:13:23.533  INFO 12428 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
2020-08-19 16:13:23.545  INFO 12428 --- [           main] o.s.b.a.h2.H2ConsoleAutoConfiguration    : H2 console available at '/h2-console'. Database available at 'jdbc:h2:mem:testdb'
```

Pour créer nos tables, il nous suffit Grâce à Hibernate de créer simplement des entités. 
Cet ORM est connecté à notre base H2. 

Créez un package model, et une classe BankAccountEntity
```java
import javax.persistence.*;

@Entity
@Table(name = BankAccountEntity.TABLE_NAME)
public class BankAccountEntity {

    public static final String TABLE_NAME= "tb_bank_account";

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id")
    private Long id;

    @Column(name = "amount")
    private Double amount;
}
```
relancez l'application et vous constaterez que la table a été créée dans H2. 


Il suffit ne reste plus qu'à créer le repository associé dans un nouveau package et le tour est joué ! 
```java
import com.excilys.formation.spring.rest.models.BankAccountEntity;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface BankAccountRepository  extends JpaRepository<BankAccountEntity, Long> {
    
}
```

Pour confirmer que tout fonctionne,  vous allez créer des tests unitaires qui utiliseront H2. 

Ajoutez la dépendance suivante
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

créez la classe de Test suivante 
```java
import com.excilys.formation.spring.rest.models.BankAccountEntity;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;

import static org.junit.Assert.assertEquals;

@RunWith(SpringRunner.class)
@DataJpaTest
public class BankAccountRepositoryTest {

    @Autowired
    BankAccountRepository repository;

    @Test
    public void should_create_a_bank_account() {
        BankAccountEntity bankAccountEntity = new BankAccountEntity(0L, 13500.23);
        repository.save(bankAccountEntity);
        List<BankAccountEntity> result = repository.findAll();
        assertEquals(result.size(), 1);
        assertEquals(bankAccountEntity, result.get(0));
    }
}
```

Compilez le projet. 

Ajoutez un service et et controller pour pouvoir maintenant requêter sur votre application. 

Service 
```java
import com.excilys.formation.spring.rest.models.BankAccountEntity;
import com.excilys.formation.spring.rest.repository.BankAccountRepository;
import org.springframework.stereotype.Service;

@Service
public class BankAccountService {

    private BankAccountRepository bankAccountRepository;

    public BankAccountService(BankAccountRepository bankAccountRepository) {
        this.bankAccountRepository = bankAccountRepository;
    }

    public BankAccountEntity createBankAccount(BankAccountEntity bankAccountEntity) {
        return this.bankAccountRepository.save(bankAccountEntity);
    }
}
```

Controller 
```java
@RestController
@RequestMapping("/account")
public class BankAccountController {

    private BankAccountService bankAccountService;

    public BankAccountController(BankAccountService bankAccountService) {
        this.bankAccountService = bankAccountService;
    }

    @PostMapping
    public ResponseEntity<BankAccountEntity> createOne(@RequestBody BankAccountEntity bankAccountEntity) {
        BankAccountEntity bankAccount = bankAccountService.createBankAccount(bankAccountEntity);
        return ResponseEntity.ok(bankAccount);
    }
}
```

Créez ensuite un Endpoint pour récupérer une ressource

Service 
```java
public Optional<BankAccountEntity> findOne(Long id) {
        return bankAccountRepository.findById(id);
    }
```

Controller 
```java
  @GetMapping("/{id}")
    public ResponseEntity<BankAccountEntity> findOne(@PathVariable Long id) {
        Optional<BankAccountEntity>  bankAccountEntity = bankAccountService.findOne(id);

        return bankAccountEntity
                .map(ResponseEntity::ok)
                .orElseGet(() -> ResponseEntity.notFound().build());

    }
```

En testant avec un Outil tel que SoapUI ou Postman, vous remarquez que les IDs se suivent.  La plupart du temps 
```json
{
    "id": 1,
    "amount": 1200
}

{
    "id": 2,
    "amount": 1200
}
```

Cela représente très souvent une faille de sécurité. Dans une API REST utilise très souvent l'ID dans le chemin de la ressource, telle que "http://mon-api/ma-ressource/1/. 
En modifiant uniquement le chemin de ma ressource. 


Il existe plusieurs façon de sécuriser l'application, l'une d'elles consiste à utiliser des UUIDs à la place d'ID auto-incrémentaux. Modifiez le model: 

```java
   @Id
   @GeneratedValue(generator = "uuid2")
   @GenericGenerator(name = "uuid2", strategy = "org.hibernate.id.UUIDGenerator")
   @Column(name = "id")
   private UUID id;
```

Modifiez le Controller, Le Service et le Repository, Vous obtiendrez ensuite des objets de la forme
```json
{
    "id": "c19b3fb1-6e16-4a91-b789-5e570f5f1ca8",
    "amount": 1200
}

{
    "id": "757c0494-a7b7-4f6e-970c-277fcd59ca77",
    "amount": 1300
}
```

## API RESTFUL

## Application Web (Spring Web MVC)

## Sécurité

### Principes

##### Authentication

L'authentification (authentication en anglais) permet de vérifier que l'utilisateur est bien qui il prétend être

User: Je suis l'administrateur du site web, mon **username** est : admin

Webapp: Très bien, quel est votre mot de passe ?

User: Mon **password** est azerty1234

Webapp: Ok, bienvenue admin

##### Authorization

L'authorisation (authorization en anglais) permet de définir ce que l'utilisateur a le droit de faire

User: Je veux voir les informations de l'utilisateur Michel

Webapp: Vous avez le rôle **USER**, vous n'avez pas la permission de voir les informations des autres utilisateurs

Admin: Je veux voir les informations de l'utilisateur Michel

Webapp: Vous avez le rôle **ADMIN**, vous avez la permission de voir les informations des autres utilisateurs

#### Servlet Filter

Avant de rajouter spring security au projet, nous allons essayer de mettre en place une sécurité pour les endpoints en pur Java

##### C'est quoi un filter ?

Un **Filter** est un composant qui va se positionner entre le browser et un Servlet Java

##### Quel est le rapport avec l'authentification et l'authorization ?

On ne veut pas ajouter la logique de la sécurité dans nos servlets (i.e DispatcherServlet) ou nos @Controllers / @RestControllers 

Le filter va donc exécuter cette logique avant que la requête n'atteigne un @Controller

##### Exemple

Vous allez créer un @Controller HelloController qui expose /api/hello/public, /api/hello/user et /api/hello/admin

Puis créez un filter qui va comparer les paramètres **username** et **password** de la requête aux crédentials : "admin:admin" et "user:password"

L'URL /api/hello/public doit pouvoir être vue sans authentification

L'URL /api/hello/user doit pouvoir être vue par **user** et **admin**

L'URL /api/hello/admin doit pouvoir être vue par **admin**

##### Comment on fait ?

On va d'abord créer un service UserService qui va servir à faire l'authentification et l'authorisation des utilisateurs
```java
package com.excilys.formation.spring.security.service;

import static java.util.Arrays.asList;
import static java.util.Collections.emptyList;

import java.util.HashMap;
import java.util.List;
import java.util.Map;
import org.springframework.stereotype.Service;

@Service
public class UserService {
  private static final Map<String, String> USER_PASSWORD;
  private static final Map<String, List<String>> ROLES;

  static {
    USER_PASSWORD = new HashMap<>();
    USER_PASSWORD.put("user", "password");
    USER_PASSWORD.put("admin", "admin");

    ROLES = new HashMap<>();
    ROLES.put("user", asList("USER"));
    ROLES.put("admin", asList("USER", "ADMIN"));
  }

  public boolean checkUser(String username, String password) {
    return username != null
        && USER_PASSWORD.containsKey(username)
        && USER_PASSWORD.get(username).equals(password);
  }

  public boolean checkRoles(String username, String role) {
    return username != null
        && ROLES.getOrDefault(username, emptyList()).contains(role);
  }
}
```

puis un SecurityFilter

```java
package com.excilys.formation.spring.security.filter;

import com.excilys.formation.spring.security.service.UserService;
import java.io.IOException;
import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpFilter;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.springframework.stereotype.Component;

@Component
public class SecurityServletFilter extends HttpFilter {
  private final UserService userService;

  public SecurityServletFilter(UserService userService) {
    this.userService = userService;
  }

  @Override
  protected void doFilter(HttpServletRequest request, HttpServletResponse response,
      FilterChain chain) throws IOException, ServletException {

    if (request.getRequestURI().equals("/api/hello/public")) { // 1
      return;
    }

    User user = extractUserFromRequest(request); // 2

    if (!authenticated(user)) { // 3
      response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
      return;
    }

    if (!authorized(user, request)) { // 4
      response.setStatus(HttpServletResponse.SC_FORBIDDEN);
      return;
    }

    chain.doFilter(request, response); // 7
  }

  private User extractUserFromRequest(HttpServletRequest request) {
    return new User(request.getParameter("username"), request.getParameter("password"));
  }

  private boolean authenticated(User user) {
    return userService.checkUser(user.username, user.password);
  }

  private boolean authorized(User user, HttpServletRequest request) {
    switch (request.getRequestURI()) {
      case "/api/hello/admin":
        return userService.checkRoles(user.username, "ADMIN"); // 5
      case "/api/hello/user":
        return userService.checkRoles(user.username, "USER"); // 6
      default:
        return true;
    }
  }

  private static class User {
    private final String username;
    private final String password;

    User(String username, String password) {
      this.username = username;
      this.password = password;
    }
  }
}
```

1. on accepte la requête si l'URL est "/api/hello/public"
2. on extrait les informations de l'utilisateur dans une classe User
3. on vérifie que l'utilisateur est bien qui il prétend être (authentification)
4. on vérifie que l'utilisateur a la permission d'accéder à l'URL
5. s'il a le rôle ADMIN pour "/api/hello/admin"
6. s'il a le rôle USER pour "/api/hello/user"
7. si tous les checks passent on peut continuer vers le DispatcherServlet

##### FilterChain

Ce code fonctionne mais n'est pas vraiment maintenable.

On peut séparer le filter en plusieurs filters et les "chainer"

Ex: PublicMethodFilter -> AuthenticationFilter -> AuthorizationFilter -> DispatcherServlet

Ce concept s'appelle "FilterChain"
```java
chain.doFilter(request, response); // 7
```
Cette ligne transmet la requête au filter suivant, ou au DispatcherServlet si c'est le dernier de la chaîne

### Spring Security

##### Dépendances

Ajoutez les dépendances suivantes
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```
et 
```xml
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-test</artifactId>
    <scope>test</scope>
</dependency>
```

Ces dépendances fournissent tout ce qui est nécessaire pour configurer Spring Security et pour tester

En redémarrant l'application, on peut voir des nouvelles lignes dans les logs
```
2020-08-21 16:11:21.553  INFO 15277 --- [           main] o.s.s.web.DefaultSecurityFilterChain     : Creating filter chain: any request, [org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter@381d7219, org.springframework.security.web.context.SecurityContextPersistenceFilter@67a3bd51, org.springframework.security.web.header.HeaderWriterFilter@5e048149, org.springframework.security.web.csrf.CsrfFilter@84487f4, org.springframework.security.web.authentication.logout.LogoutFilter@60859f5a, org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter@1fde0371, org.springframework.security.web.authentication.ui.DefaultLoginPageGeneratingFilter@2ab0702e, org.springframework.security.web.authentication.ui.DefaultLogoutPageGeneratingFilter@49fe3142, org.springframework.security.web.authentication.www.BasicAuthenticationFilter@615e3f51, org.springframework.security.web.savedrequest.RequestCacheAwareFilter@56913163, org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter@14229fa7, org.springframework.security.web.authentication.AnonymousAuthenticationFilter@13fed1ec, org.springframework.security.web.session.SessionManagementFilter@3d5790ea, org.springframework.security.web.access.ExceptionTranslationFilter@29eda4f8, org.springframework.security.web.access.intercept.FilterSecurityInterceptor@1835d3ed]
```

##### DefaultSecurityFilterChain

Spring Security crée par défaut une FilterChain constituée de 15 filters

![DefaultSecurityFilterChain](annexes/pictures/filterchain.png?raw=true "Default Filter Chain")

* BasicAuthenticationFilter: Essaie d'authentifier l'utilisateur en regardant dans les headers HTTP.

* UsernamePasswordAuthenticationFilter: Essaie d'authentifier l'utilisateur en cherchant un username/password dans les paramètres/body de la requête.

* DefaultLoginPageGeneratingFilter: Génère une page de login si l'option est activée (activée par défaut).

* DefaultLogoutPageGeneratingFilter: Génère une page de logout si l'option est activée (activée par défaut).

* FilterSecurityInterceptor: Applique les authorizations.

#### Configuration

Pour configurer Spring Security, on crée une classe de configuration Spring qui hérite de **WebSecurityConfigurerAdapter**
```java
package com.excilys.formation.spring.security.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity // 1
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

  @Override
  protected void configure(HttpSecurity http) throws Exception {  // (2)
        http
            .authorizeRequests()
              .antMatchers("/api/hello/public/**").permitAll() // 3
              .antMatchers("/api/hello/admin/**").hasRole("ADMIN") // 4
              .anyRequest().authenticated() // 5
              .and()
            .formLogin() // 6
              .loginPage("/login")
              .permitAll()
              .and()
            .logout() // 7
              .permitAll()
              .and()
            .httpBasic(); // 8
  }
}
```

1. Permet à Spring de considérer cette classe de configuration comme une configuration de sécurité.
   
2. On override la configuration par défaut
 
    ```java
      // default configuration in WebSecurityConfigurerAdapter
      protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
              .anyRequest().authenticated()
              .and()
            .formLogin()
              .and()
            .httpBasic();
      }
    ``` 
    WebSecurityConfigurerAdapter propose un **DSL** pour configurer plus facilement la sécurité
   
3. Toutes les requêtes arrivant sur /api/hello/public (et /api/hello/public/whatever, /api/hello/public/whatever/whatever, ...) n'ont pas besoin d'être authentifié (.permitAll()).
   
4. Toutes les requêtes arrivant sur /api/hello/admin (et /api/hello/admin/whatever, /api/hello/admin/whatever/whatever, ...) doivent d'être authentifiées et l'utilisateur doit avoir le rôle ADMIN (.hasRole("ADMIN")).
  
5. Toutes les autres requêtes doivent être authentifiées.
   
6. On configure une page de login (username/password dans un formulaire) avec une page custom (/login). On n'a pas besoin d'être authentifié pour arriver sur cette page (sinon on ne peut jamais s'authentifier).
   
7. Pareil pour le logout
   
8. On configure l'[authentification Basic](https://en.wikipedia.org/wiki/Basic_access_authentication).

#### Authentification

Maintenant se pose la question "Comment Spring authentifie-t'il un utilisateur ?"

Créez une table users en base de données
```
create table users (username varchar(255) primary key, password varchar(255));
```
Cette table contient les usernames des utilisateurs et leur password (haché, pas en clair hein)

##### UserDetailsService

Pour savoir comment récupérer les informations d'un utilisateur, on doit configurer un bean de type UserDetailsService
```java
package org.springframework.security.core.userdetails;

public interface UserDetailsService {
  UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```

On crée donc une classe implémentant cette interface
```java
package com.excilys.formation.spring.security.service;

import static java.util.stream.Collectors.toList;
import static java.util.Collections.emptyList;

import java.util.List;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

@Service
public class MyUserDetailsService implements UserDetailsService {
  private final UserRepository userRepository;

  public MyUserDetailsService(UserRepository userRepository) {
    this.userRepository = userRepository;
  }

  @Override
  public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
    return mapToUserDetails(username);
  }

  private UserDetails mapToUserDetails(String username) {
    UserEntity userEntity = userRepository.findUserByUsername(username);
        
    return new User(userEntity.getUsername(), userEntity.getPassword(), emptyList());
  }
}
```

En supposant que UserEntity est une @Entity et UserRepository un @Repository

On peut aussi utiliser des implémentations déjà existantes de UserDetailsService
* JdbcUserDetailsManager : implémentation avec une base de données
```java
@Bean
public JdbcUserDetailsManager jdbcUserDetailsManager(DataSource dataSource) {
  return new JdbcUserDetailsManager(dataSource);
}
```

* InMemoryUserDetailsManager : implémentation en mémoire
```java
@Bean
public InMemoryUserDetailsManager inMemoryUserDetailsManager(PasswordEncoder passwordEncoder) {
  UserDetails user = User.withUsername("user")
      .password(passwordEncoder.encode("password"))
      .roles("USER")
      .build();
  UserDetails admin = User.withUsername("admin")
      .password(passwordEncoder.encode("admin"))
      .roles("ADMIN", "USER")
      .build();
    
  return new InMemoryUserDetailsManager(asList(user, admin));
}
```

##### Password Encoder
Pour que Spring Security puisse hacher les passwords, il faut lui spécifier un PasswordEncoder
```java
@Bean
public PasswordEncoder passwordEncoder() {
  return new BCryptPasswordEncoder();
}
```

#### Authorization

GrantedAuthority est l'interface utilisée par UserDetailsService pour définir les permissions d'un utilisateur

Créez une table authorities en base de données
```
create table authorities (username varchar(255), authority varchar(68), foreign key (username) references users(username));
```
Cette table contient les rôles des utilisateurs

Et on change l'implémentation de UserDetailsService
```java
package com.excilys.formation.spring.security.service;

import static java.util.stream.Collectors.toList;

import java.util.List;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

@Service
public class MyUserDetailsService implements UserDetailsService {
  private final UserRepository userRepository;

  public MyUserDetailsService(UserRepository userRepository) {
    this.userRepository = userRepository;
  }

  @Override
  public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
    return mapToUserDetails(username);
  }

  private UserDetails mapToUserDetails(String username) {
    UserEntity userEntity = userRepository.findUserByUsername(username);
    
    List<GrantedAuthority> authorities = mapAuthorities(userEntity.getRoles());
    
    return new User(userEntity.getUsername(), userEntity.getPassword(), authorities);
  }

  private List<GrantedAuthority> mapAuthorities(List<String> roles) {
    return roles.stream().map(SimpleGrantedAuthority::new).collect(toList());
  }
}
```

#### Configuration

Comme vu plus haut, on peut configurer les roles dans les WebSecurityConfigurerAdapter.
```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests()
          .antMatchers("/api/hello/admin/**").hasAuthority("ROLE_ADMIN")
          .antMatchers("/api/hello/user/**").hasAnyAuthority("ROLE_USER", "ROLE_ADMIN")
          .anyRequest().authenticated()
          .and()
        .formLogin()
          .and()
        .httpBasic();
}
```

Pour accéder à "/api/hello/admin", l'utilisateur doit avoir l'authority "ROLE_ADMIN".
Pour accéder à "/api/hello/user", l'utilisateur doit avoir l'authority "ROLEUSER" ou "ROLE_ADMIN".

On peut aussi écrire 
```java
  .antMatchers("/api/hello/admin/**").hasRole("ADMIN")
  .antMatchers("/api/hello/user/**").hasRole("USER", "ADMIN")        
```

Pour un rôle, Spring Security va chercher une authority nommée "ROLE_" + authority

## Documentation avec Swagger

## Migration database avec FlywayDB
