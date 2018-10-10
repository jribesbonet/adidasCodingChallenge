# adidasCodingChallenge
Coding challenge instructions

## Getting Started
This instruction will make you run the itinerary service step to step.

# Related projects
Those are the projects that need to be imported to make the software run correctly

## itinerary-config-server
This project runs the config server. All the properties from each service will be stored and served from this service.
All properties this config server loads are stored in [this repository](https://github.com/jribesbonet/configserver-properties)

#### Build / Run instructions
On the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file itinerary-config-server-1.0.jar

To run the service locally without the docker container execute:
```
java -jar target/itinerary-config-server-1.0.jar
```
To run the application inside docker container execute:
```
docker container run -p 8888:8888 --name itinerary-config-server itinerary-config-server:1.0
```

### itinerary-eureka-server
#### Build / Run instructions
On the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file itinerary-eureka-server-1.0.jar

To run the service locally without the docker container execute:
```
java -jar -Dspring.profiles.active=local target/itinerary-eureka-server-1.0.jar
```
To run the application inside docker container execute:
```
docker container run -p 8761:8761 --name itinerary-eureka-server --link itinerary-config-server:itinerary-config-server itinerary-eureka-server:1.0
```

### itinerary-lib
#### Build / Run instructions
On the path where the project is downloaded execute:
```
mvn clean install
```
This will install the common functionlities library that will be used by other microservices

### city-connections-service
#### Build / Run instructions
On the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file city-connections-service-1.0.jar

To run the service locally without the docker container execute:
```
java -jar -Dspring.profiles.active=local target/city-connections-service-1.0.jar
```
To run the application inside docker container execute:
```
docker container run -p 8082:8082 --name city-connections-service --link itinerary-config-server:itinerary-config-server --link itinerary-eureka-server:itinerary-eureka-server city-connections-service:1.0
```


### itinerary-service
#### Build / Run instructions
On the path where the project is downloaded execute:
```
mvn clean package docker:build
```
This will generate the binary file itinerary-service-1.0.jar

To run the service locally without the docker container execute:
```
java -jar -Dspring.profiles.active=local target/itinerary-service-1.0.jar
```
To run the application inside docker container execute:
```
docker container run -p 8081:8081 --name itinerary-service --link itinerary-config-server:itinerary-config-server --link itinerary-eureka-server:itinerary-eureka-server --link city-connections-service:city-connections-service itinerary-service:1.0
```
