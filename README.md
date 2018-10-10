# Adidas coding challenge
Coding challenge instructions
Step to step instructions to make itinerary service run on docker containers or as a jar file.

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

## Related projects
Those are the projects that need to be imported to make the software run correctly

The order in which the services must be executed is the following:
* run itinerary-config-server
* run itinerary-eureka-server
* install dependency itinerary-lib
* run city-connections-service
* run itinerary-service

## How to run the itinerary service.
### 1. itinerary-config-server
This project runs the config server. All the properties from each service will be stored and served from this service.
All properties this config server loads are stored in [this repository](https://github.com/jribesbonet/configserver-properties).
Project repository can be find in [this repository](https://github.com/jribesbonet/itinerary-config-server)

##### Build / Run instructions
On the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file itinerary-config-server-1.0.jar

To run the service locally **without** the docker container execute:
```
java -jar target/itinerary-config-server-1.0.jar
```
To run the application inside **docker container** execute:
```
docker container run -p 8888:8888 --name itinerary-config-server itinerary-config-server:1.0
```

### 2. itinerary-eureka-server
This project runs the eureka naming server. All microservices will be registered in the server.
Project repository can be find in [this repository](https://github.com/jribesbonet/itinerary-eureka-server)

#### Build / Run instructions
On the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file itinerary-eureka-server-1.0.jar

To run the service locally **without** the docker container execute:
```
java -jar -Dspring.profiles.active=local target/itinerary-eureka-server-1.0.jar
```
To run the application inside **docker container** execute:
```
docker container run -p 8761:8761 --name itinerary-eureka-server --link itinerary-config-server:itinerary-config-server itinerary-eureka-server:1.0
```

### 3. itinerary-lib
Library with common functionalities that will be used in the itinerary service.
Project repository can be find in [this repository](https://github.com/jribesbonet/itinerary-lib)

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
On the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file city-connections-service-1.0.jar

To run the service locally **without** the docker container execute:
```
java -jar -Dspring.profiles.active=local target/city-connections-service-1.0.jar
```
To run the application inside **docker container** execute:
```
docker container run -p 8082:8082 --name city-connections-service --link itinerary-config-server:itinerary-config-server --link itinerary-eureka-server:itinerary-eureka-server city-connections-service:1.0
```

#### 5. itinerary-service
This project runs the City Connections Service. This microservice exposes the mehtod to obtain the path with less connections and the path with less time.
Project repository can be find in [this repository](https://github.com/jribesbonet/itinerary-service)

##### Build / Run instructions
On the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file itinerary-service-1.0.jar

To run the service locally **without** the docker container execute:
```
java -jar -Dspring.profiles.active=local target/itinerary-service-1.0.jar
```
To run the application inside **docker container** execute:
```
docker container run -p 8081:8081 --name itinerary-service --link itinerary-config-server:itinerary-config-server --link itinerary-eureka-server:itinerary-eureka-server --link city-connections-service:city-connections-service itinerary-service:1.0
```
