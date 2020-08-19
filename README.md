# formation-spring-tutorial
Tutoriel API Cocktail


# Création du projet 

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











