# formation-spring-tutorial
Tutoriel API Cocktail


## Création du projet 

###### Création dans Git 
Vous allez créer un repository dans github en prennant soin de créer également un fichier README.md 

<!-- [![Github 01](https://github.com/fconsigny-github/formation-spring-tutorial/blob/master/annexes/pictures/01-creation-repository-github.PNG](https://github.com/fconsigny-github/formation-spring-tutorial/blob/master/annexes/pictures/01-creation-repository-github.PNG) -->



Dans un premier temps vous allez créer un projet maven

```
mvn archetype:generate -DgroupId=nom-du-package-DartifactId=nom-de-l-application -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Vous allez ensuite editer le pom.xml  pour y ajouter les dépendances liées à Spring 

Dans un premier temps le pom parent de springboot :

```
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.2.RELEASE</version>
    <relativePath />
  </parent>
```

Puis le starter WEB qui va amener les dépendances Spring MVC , REST et Configurer par défaut Tomcat en tant que Serveur d'application par défaut.
```
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Afin de pouvoir générer  un librable qui va embarquer le tomcat, il est nécessaire d'ajouter le plugin suivant : 
```
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
```

Pour finir vous allez éditer votre point d'entrée de l'application pour qu'elle puisse prendre en compte la configuration minimale de spring boot: 
```
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

Compilez l'application en ligne de commande 
```
mvn clean install
```
Démarrez l'application 


```
java -jar target/nom-du-jar
```

Vous obtiendrez ensuite les logs suivants: 

```
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

* Vous pouvez y voir le nom du jar exécuté : v1.0-SNAPSHOT
* Le processus créé : PID 11044 
* Le profil lancé :  No active profile set, falling back to default profiles: default
* Le serveur d'application et le port utilisé : Tomcat initialized with port(s): 8080 (http)
* Le context de l'application :  with context path ''
* Le temps de démarrage de l'application :  Started App in 2.991 seconds (JVM running for 3.551)


## Instance des beans et Scopes

Pour comprendre comment s'instancie les beans, vous allez créer une classe qui permet de lister tous les beans présents dans le contexte de Spring 

Importez tout d'abord la librairie SL4J 
```
 <!-- Logger -->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.30</version>
    </dependency>
```


créez dans un nouveau package une classe SpringBeanViewer 
```
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
```
@Component
@Scope("session")
public class SpringBeanViewer
```

Recompilez et redémarrez l'application. 
Vous constaterez qu'en modifiant le scope d'un composant, la classe n'est pas chargée dans le contexte de spring.

Changez le scope avec la valeur singleton 
```
@Component
@Scope("singleton")
```

Redémarrez l'application. 

Editer la classe SpringBeanViewer 
```
*@Component
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

```
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

```
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

```
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

Redémarrez l'application et connectez vous à l'url http://localhost:8080/h2-console 

###### Création d'un modèle persisté en base

Avant toute chose, ajouez le starter spring data jpa 
```
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


