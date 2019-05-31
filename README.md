# Automated Software Validation 
> Jorik Barten - _500622155_ <br />
> Simon van Zwoll - _500766699_ <br />
> Thomas Jüsche - _500504300_ <br />
> Marc Veens - _500758673_

## Opdracht 1: Ontdek nieuwe requirements
[Simon]

## Opdracht 2: Nulmeting via SonarCloud
Backend:
![Backend nulmeting](images/backend-nulmeting.png)

Front-end:
![Front-end nulmeting](images/frontend-nulmeting.png)

## Opdracht 3: Ontwikkel unit testen
[?]

## Opdracht 4: Opzetten van een CI/CD pipeline
We hebben Travis CI gebruikt om de CI/CD pipeline op te zetten. De stappen voor de front-end zijn als volgt: 
1. Git trigger op de master branch naar Travis CI
2. Er wordt een `npm install` gedaan om alle packages te installeren
3. Er wordt een `tslint-reporter` gedraaid, zodat ook tslint foutmeldingen in SonarCloud naar voren komen. Dit gebeurt standaard niet.
4. `sonar-scanner` wordt uitgevoerd waarmee de kwaliteit van het hele front-end project wordt gecontrolleerd.
5. Er wordt een Docker container aangemaakt
    1. `npm install` binnen de container
    2. `npm run build-production`: er wordt een build gemaakt die voor productie bedoeld is.
    3. `npm run tslint`: er wordt gecheckt of er geen errors in de tslint voorkomen. Warnings worden weergegeven maar gaat de build niet op stuk.
    4. `npm run test`: alle testen van de front-end code worden uitgevoerd.
    5. `npm run run-server`: om de NodeJS server te draaien die de front-end serveert. 
6. Er wordt een rapport naar SonarCloud gestuurd van de kwaliteit van de code.
7. De Docker container wordt uitgerold naar een Azure Container Registry.
8. Een trigger van een Azure App Service wordt aangeroepen waardoor de nieuwe image wordt uitgerold naar de App Service die gekoppeld is aan het domein.
8. De pipeline is voltooid. 

Voor de backend is er een soortgelijke implementatie.
1. Git trigger op de master branch naar Travis CI
2. `sonar-scanner` wordt uitgevoerd waarmee de kwaliteit van het hele front-end project wordt gecontrolleerd.
3. Er wordt een Docker container aangemaakt
    1. `mvn -f pom.xml` er wordt een build gemaakt van het project
    2. Tomcat wordt opgestart waarmee de applicatie geserveerd wordt.
6. Er wordt een rapport naar SonarCloud gestuurd van de kwaliteit van de code.
7. De Docker container wordt uitgerold naar een Azure Container Registry.
8. Een trigger van een Azure App Service wordt aangeroepen waardoor de nieuwe image wordt uitgerold naar de App Service die gekoppeld is aan het domein.
8. De pipeline is voltooid. 

## Opdracht 5: Implementatie BDD
[?]
