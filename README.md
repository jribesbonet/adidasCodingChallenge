# Adidas coding challenge
Step by step instructions to make itinerary platform run on docker containers and as a jar file.

## Technologies used
* **SpringBoot 2.0** - This framework provides an easy way to develop and deploy microservice with a cloud aproach
* **H2 DB** - On memory database that is only available on runtime. Used to store the city connections information. It's easy to integrate with SpringBoot and used with JPA make the tables to be created automatically.
* **Spring Data JPA** - Provides great abstration from the BD and makes the queries to the DB very easy. The integrations with SpringBoot is perfect.
* **Netflix Config Server** - Provides the capability of centralizing all the properties on the same place. Changes only have to be made on one place instead on each microservice.
* **Netflix Eureka Naming Server** - Gives the capability of registering the microservices that are being executed. The purpose of this server is to serve the microservices the information of which services of the same kind are running in order to balance the calls between all of them.
* **Netflix Feign Client** - REST client that provides great integrations with SpringBoot.
* **Ribbon Client LoadBalancing** - It's not used in this project because I was not able to integrate it when running in docker containers. Without docker container it was running correctly. The problem I faced and I don't had enough time to solve was that Eureka resolves the DNS without the port and that made call to fail.
* **Swagger 2** - Documentation of the REST API's with annotations. Very usefull and easy to integrate with SpringBoot.
* **Docker** - Give the capability of generating containers.
* **Docker Spotify Maven Plungin** - Gives the capability to generate docker images files with Maven. With this approach there is no need to be changing the Dockerfile every time that the projet version changes.
* **Dijkstra algorith** - Downloaded classes to use Dijkstra algorith in order to get the shortest path between nodes.

## Related projects
Those are the projects that need to be imported to make the software run correctly:
* [itinerary-config-server](https://github.com/jribesbonet/itinerary-config-server)
* [itinerary-eureka-server](https://github.com/jribesbonet/itinerary-eureka-server)
* [itinerary-lib](https://github.com/jribesbonet/itinerary-lib)
* [city-connections-service](https://github.com/jribesbonet/city-connections-service)
* [itinerary-service](https://github.com/jribesbonet/itinerary-service)

## Docker images
if you don't want to build the images yourself they are available on docker hub. Just run the docker run commands on each service and the images will be downloaded from docker hub automatically.
* [itinerary-config-server image](https://hub.docker.com/r/jribes/itinerary-config-server/)
* [itinerary-eureka-server image](https://hub.docker.com/r/jribes/itinerary-eureka-server/)
* [city-connections-service image](https://hub.docker.com/r/jribes/city-connections-service/)
* [itinerary-service image](https://hub.docker.com/r/jribes/itinerary-service/)

## Execution order
The order in which the services must be executed is the following:
* run itinerary-config-server
* run itinerary-eureka-server
* run city-connections-service (itinerary-lib dependency needs to be installed)
* run itinerary-service (itinerary-lib dependency needs to be installed)

## How to build/run the itinerary platform.
All docker run commands listed below are exposing a port outside docker environment. This is done like this to be able to enter the SwaggerUi interface of each service and check Eureka port if the people who will check this challenge want's to see it.
For a live environment only container *itinerary-service* should be exposed outside the docker environment. 

### 1. itinerary-config-server
This project runs the config server. All the properties from each service will be stored and served from this service.
All properties that config server loads are stored in [this repository](https://github.com/jribesbonet/configserver-properties).
Project repository can be find in [here](https://github.com/jribesbonet/itinerary-config-server)

##### Build / Run instructions
To build the binary file and create the docker image, on the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file **itinerary-config-server-1.0.jar** and the docker image **jribes/itinerary-config-server**

To run the service locally with the **binary jar file** execute:
```
java -jar target/itinerary-config-server-1.0.jar
```
To run the application inside **docker container** execute:
```
docker container run -p 8888:8888 --name itinerary-config-server jribes/itinerary-config-server
```

### 2. itinerary-eureka-server
This project runs the eureka naming server. All microservices will be registered in the server.
Project repository can be find in [here](https://github.com/jribesbonet/itinerary-eureka-server)

#### Build / Run instructions
To build the binary file and create the docker image, on the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file **itinerary-eureka-server-1.0.jar** and the docker image **jribes/itinerary-eureka-server**

To run the service locally with the **binary jar file** execute:
```
java -jar -Dspring.profiles.active=local target/itinerary-eureka-server-1.0.jar
```
To run the application inside **docker container** execute:
```
docker container run -p 8761:8761 --name itinerary-eureka-server --link itinerary-config-server:itinerary-config-server jribes/itinerary-eureka-server
```

### 3. itinerary-lib
Library with common functionalities that will be used in the itinerary service.
Project repository can be find in [here](https://github.com/jribesbonet/itinerary-lib)

#### Build / Run instructions
On the path where the project is downloaded execute:
```
mvn clean install
```
This will install the common functionlities library that will be used by other microservices

### 4. city-connections-service
This project runs the City Connections Service. This microservice exposes the methods to query the H2 database in order to get the city connections.
Project repository can be find in [this repository](https://github.com/jribesbonet/city-connections-service)

#### Build / Run instructions
To build the binary file and create the docker image, on the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file **city-connections-service-1.0.jar** and the docker image **jribes/city-connections-service**

To run the service locally with the **binary jar file** execute:
```
java -jar -Dspring.profiles.active=local target/city-connections-service-1.0.jar
```
To run the application inside **docker container** execute:
```
docker container run -p 8082:8082 --name city-connections-service --link itinerary-config-server:itinerary-config-server --link itinerary-eureka-server:itinerary-eureka-server jribes/city-connections-service
```

##### Documentation
This service is documented with Swagger, this can be checked on [http://localhost:8081/swagger-ui.html](http://localhost:8081/swagger-ui.html) once the process is up and running.

#### 5. itinerary-service
This project runs the City Connections Service. This microservice exposes the mehtod to obtain the path with less connections and the path with less time.
Project repository can be find in [here](https://github.com/jribesbonet/itinerary-service)

##### Build / Run instructions
To build the binary file and create the docker image, on the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file **itinerary-service-1.0.jar** and the docker image **jribes/itinerary-service**

To run the service locally with the **binary jar file** execute:
```
java -jar -Dspring.profiles.active=local target/itinerary-service-1.0.jar
```
To run the application inside **docker container** execute:
```
docker container run -p 8081:8081 --name itinerary-service --link itinerary-config-server:itinerary-config-server --link itinerary-eureka-server:itinerary-eureka-server --link city-connections-service:city-connections-service jribes/itinerary-service
```

##### Documentation
This service is documented with Swagger, this can be checked on [http://localhost:8082/swagger-ui.html](http://localhost:8082/swagger-ui.html) once the process is up and running.

## Itinerary calculation

* To check that the platform is calculating both itineraries correctly, we will use the swagger interface to check it.
* Once the whole platform is up and running, enter to the following link [http://localhost:8082/swagger-ui.html](http://localhost:8082/swagger-ui.html).
* Open the itinerary-controller section
* Open the /route/origin/{originName}/destination/{destinationName} section
* Write a 3 capital letter city name on the originName and destinationName.
* Click on "Try it out!" button.
* The response with both paths should appear inside the section "Response Body", the fields are called pathWithLessTime and pathWithLessConnections.

### Change city connections information on H2 DB
Before starting **city-connections-service**, you can edit **data.sql** file inside src/main/resources. Once the process is up and running the rows in data.sql will be inserted on H2 DB and used in itinerary platform.
To make the changes take effect, project need to be recompiled and executed.
The file stored right now, contains the following cities:
* Possible origin cities: BCN, MAD, VAL, BIL, MAD, VAL, ZAZ, HSK
* Possible destination cities: MAD, VAL, BIL, ZAZ, IBZ, HSK

## Next steps

The next step will be to bring itinerary platform to Kubernetes. This way, config server, eureka server and ribbon won't be necessary on the solution because Kubernetes provides all the autoscaling, load balancing and config information.
