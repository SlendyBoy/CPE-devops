# TP1

## Database 

Build l'image postgresql:14.1 alpine

``` docker build -t Documents/4A/devops/tp1/database .```

Création d'un réseau :

```docker network create app-network```

Lancement de la BDD (option -d pour run en background) :

```docker run -d --net=app-network --name database Documents/4A/devops/tp1/database```


Run adminer :

```
docker run \
-p "8090:8080" \
--net=app-network \
--name=adminer \
-d \
adminer
```

Accès à l'interface (localhost:8090) :
serveur : database:5432
user: usr
password : pwd
databse: db

Ajout des scripts sql dans le dossier /docker-entrypoint-initdb.d :

```COPY CreateSchema.sql /docker-entrypoint-initdb.d/
COPY InsertData.sql /docker-entrypoint-initdb.d/ 
```

Persistence des données :

```-v volume:/var/lib/postgresql/data```

## Backend-api

Dockerfile :
```

# image
FROM openjdk:11-jre

# copy files
COPY Main.class /

# compiled class
RUN java Main
```

Build de l'image :

```docker build -t Documents/4A/devops/tp1/backend .```

Run :

```docker run -d --net=app-network --name backend Documents/4A/devops/tp1/backend```

### Question 1-2 : Pourquoi avons-nous besoin d'un build multistage ? Explication du fichier Dockerfile :

On compile avec Maven et on doit ensuite run l'appli.

Image maven pour compiler :
``` 
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build
```

Var d'environnement MYAPP_HOME = /opt/myapp
WORKDIR : dossier où les commandes suivantes seront executées.
```
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
```

Copie de pom.xml dans le répertoire /opt/myapp du conteneur et copie du dossier src dans /opt/myapp.
```
COPY pom.xml .
COPY src ./src
```

Création du jar
```
RUN mvn package -DskipTests
```

Utilisation d'une image
```
FROM amazoncorretto:17
```

Comme écrit prcédemment
```
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME
```

Copie du jar et déinition du point d'entrée à exécuter
```
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

ENTRYPOINT java -jar myapp.jar
```

## http-server

Dockerfile :

Fichier html dans public-html
```
FROM httpd:2.4
COPY ./public-html/ /usr/local/apache2/htdocs/
```

Build :
``` docker build -t Documents/4A/devops/tp1/http-server .```

Run :
```docker run -d --net=app-network -p 80:80 --name http-server Documents/4A/devops/tp1/http-server```
