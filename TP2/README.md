# TP2

## Qu'est-ce que les testcontainers?

les Testcontainers servent à démarrer et arrêter des conteneurs Docker depuis les tests d’intégration.

## CI

Dossier .github/workflows avec main.yml :

```yaml
name: CI devops 2023
on:
  #to begin you want to launch this job in main and develop
  push:
    branches: #TODO 
  pull_request:

jobs:
  test-backend: 
    runs-on: ubuntu-22.04
    steps:
     #checkout your github code using actions/checkout@v2.5.0
      - uses: actions/checkout@v2.5.0

     #do the same with another action (actions/setup-java@v3) that enable to setup jdk 17
      - name: Set up JDK 17
        #TODO

     #finally build your app with the latest command
      - name: Build and test with Maven
        run: #TODO
```    

On push sur les branches main et develop : 

```yaml
on:
  #to begin you want to launch this job in main and develop
  push:
    branches: 
      - main
      - develop
  pull_request:

```

On installe jdk17

```yaml
- name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          distribution: 'corretto'
          java-version: '17'
```

Commande pour compiler et tester :

```yaml
- name: Build and test with Maven
        run: mvn clean install
```