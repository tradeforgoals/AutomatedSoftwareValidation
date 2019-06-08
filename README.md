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
De backend is geschreven in Java en er wordt gebruikt gemaakt van het Spring framework. 
Om in de backend CRUD operaties uit te voeren maken wij gebruik van de CRUD repository. 
De CRUDrepository is een interface die gebruikt kan worden met een aantal vaste methodes.
In de services worden de methodes uit de repository uitgewerkt.

Een Unit test moet de functionaliteit van een class afzonderlijk kunnen testen.
Met de geimplementeerde repository is er binnen de services een afhankelijk gecreeerd.
Om deze afhankelijkheden na te bootsen gebruiken wij de library 'Mockito'.
Met Mockito kan je een 'Mock' maken van bijvoorbeeld de Spring repository om zo de functionaliteit van je class te kunnen testen.

Om de library te importeren door middel van dependecy injection zet je de volgende dependecy in je Pom.xml`

  <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-all</artifactId>
        <version>1.10.19</version>
        <scope>test</scope>
   </dependency>
   
Het Unit testen doen wij met JUnit5.
Om de library te importeren door middel van dependecy injection zet je de volgende dependecy in je Pom.xml`

 <dependency>
     <groupId>org.junit.jupiter</groupId>
     <artifactId>junit-jupiter-api</artifactId>
     <version>RELEASE</version>
     <scope>test</scope>
 </dependency>

In de architectuur binnen de packages is een testmap aanwezig. Het path naar deze map is als volgt:
Project\TradeForGoals\TradeForGoals\tradeforgoals\src\test\java\com\hva\tradeforgoals\persistence

1. Maak de benodigde Unit testen aan.
2. Zorg dat de naam van je testclass eindigd op 'Test'.
3. Zorg dat de annotatie '@RunWith(MockitoJUnitRunner.class)' boven de public class staat.
4. Maak een instance aan van de class die je wilt testen. Het is gebruikelijk om deze class 'object under test' te noemen.
5. Creeer een 'mock' van de benodigde repository en zet hier de @Mock annotatie nodig.
6. Als de auto-import aan staat zal je zien dat de volgende imports gedaan zijn:
- import static org.mockito.Mockito.*;
- import org.mockito.Mock;
- import org.mockito.MockitoAnnotations;
- import org.mockito.junit.MockitoJUnitRunner; 
7. Creeer een public void setup met de @Before annotatie. 

In deze methode kan je aangeven welke instanties en repositories hij tot zijn beschikking moet hebben voor het uitvoeren van de test.
Dat ziet er bijvoorbeeld als volgt uit:

		MockitoAnnotations.initMocks(this);
        objectUnderTest = new CustomerService();
        objectUnderTest.setCustomerRepository(customerRepositoryMock); 
	

8. Zet boven elke methode die je wilt testen de annotatie @Test.
Als de auto-import aan staat zal je zien dat inmiddels ook de volgende imports gedaan zijn:

- import org.junit.Before;
- import org.junit.Test;
- import org.junit.runner.RunWith;

Het is handig om, wanneer je veel testdata moet aanmaken, een testobject aan te maken met de fields van de originele class.
In de CustomerService test wordt er bijvoorbeeld een methode aangeroepen die elke keer als dat nodig is een testcustomer aangemaakt.

Het is belangrijk om te testen om te kijken of de code werkt en beschermd is tegen bijvoorbeeld foutieve invoer.
In de methode findById() wil je bijvoorbeeld controleren of de juiste data inderdaad is gevonden.
Hierna wil je ook de inhoud van het gevonden object vergelijken met een verwachte waarde.

Onderaan je test kan je gebruik maken van methodes van Junit als 'assertNotNull' en 'assertEquels' om de verwachte waarde en exacte waarde met elkaar te vergelijken.
Wanneer je op de public testclass klikt en kiest voor 'run with coverage' zal de test laten zien hoeveel methodes er zijn aangeroepen.
Ook zal Junit laten zien hoeveel lines code van het objectUnderTest daadwerkeljk zijn getest.

Bij het pushen naar Git wordt er automatisch een nieuwe build gedaan zoals beschreven in opdracht 4.
Bij het inchecken in Sonar worden de testen automatisch opnieuw uitgevoerd.

###Screenshots:

CustomerService:
![CustomerService test](images/CustomerServiceTest.jpg)

ProductService:
![Productservice test](images/ProductserviceTest.jpg)

AdvertisementService:
![AdvertisementService test](images/AdvertisementService.jpg)

TradeService:
![TradeService test](images/TradeServiceTest.jpg)

### Resultaat code coverage Sonar

Before Unit tests:
![Sonarcloudbefore tests](images/Sonarcloudbeforetests.jpg)

After Unit tests:
![Sonarcloudaftertests](images/Sonarcloudaftertests.jpg)

Na het uploaden van de Unit tests voor de services binnen de back-end applicatie is de code-coverage met 31.1% toegenomen.


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
2. `sonar-scanner` wordt uitgevoerd waarmee de kwaliteit van het hele back-end project wordt gecontrolleerd.
3. Er wordt een Docker container aangemaakt
    1. `mvn -f pom.xml` er wordt een build gemaakt van het project
    2. Tomcat wordt opgestart waarmee de applicatie geserveerd wordt.
6. Er wordt een rapport naar SonarCloud gestuurd van de kwaliteit van de code.
7. De Docker container wordt uitgerold naar een Azure Container Registry.
8. Een trigger van een Azure App Service wordt aangeroepen waardoor de nieuwe image wordt uitgerold naar de App Service die gekoppeld is aan het domein.
8. De pipeline is voltooid. 

## Opdracht 5: Implementatie BDD
[?]
