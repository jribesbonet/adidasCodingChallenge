# adidasCodingChallenge
Coding challenge instructions

## Getting Started
This instruction will make you run the itinerary service step to step.

# Related projects
Those are the projects that need to be imported to make the software run correctly

## itinerary-config-server
This project runs the config server. All the properties from each service will be stored and served from this service.
All properties this config server loads are stored in [this repository](https://github.com/jribesbonet/configserver-properties)

### Build / Run instructions
On the path where the project is downloaded execute:
...
mvn clean package docker:build
...
This will generate the binary file itinerary-config-server-1.0.jar

To run the service execute:
...
java -jar target/itinerary-config-server-1.0.jar
...

### itinerary-eureka-server
### Build / Run instructions

### itinerary-lib
### Build / Run instructions

### city-connections-service
### Build / Run instructions

### itinerary-service
### Build / Run instructions
