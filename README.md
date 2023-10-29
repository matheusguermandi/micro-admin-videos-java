<center>
    <p align="center">
      <img src="https://icon-library.com/images/java-icon-png/java-icon-png-15.jpg" width="150" />
    </p>
    <h1 align="center">🚀 Microservice: Video Catalog Admin with Java</h1>
    <p align="center">
      Microservice referring to the Video Catalog Management backend<br />
      Using Clean Architecture, DDD, TDD and current market best practices
    </p>
</center>
<br />



## Tools required

-JDK 17
- IDE of your choice
- Docker

## How to run?

1. Clone the repository:
```sh
git clone https://github.com/codeedu/micro-admin-videos-java.git
```

2. Upload the MySQL database with Docker:
``` shell
docker-compose up -d
```

3. Perform MySQL migrations with Flyway:
``` shell
./gradlew flywayMigrate
```

4. Run the application as a SpringBoot app:
``` shell
GOOGLE_CLOUD_CREDENTIALS=A \
GOOGLE_CLOUD_PROJECT=A \
./gradlew bootRun
```

> It is also possible to run as a Java application through the
> main() method in Main.java class
## Database
The main database is a MySQL and to upload it locally we will use
Docker. Run the following command to bring up MySQL:

``` shell
docker-compose up -d
```

Ready! Wait that in a moment MySQL will be ready to be consumed
on port 3306.

### Database Migrations with Flyway

#### Run the migrations

If it is the first time you are uploading the database, it is necessary
running the SQL migrations with the `flyway` tool.
Run the following command to run the migrations:

``` shell
./gradlew flywayMigrate
```

Ready! Now the MySQL database is ready to be used.

<br/>

#### Clear database migrations

It is possible to clean (delete all tables) your database, just
run the following command:

```shell
./gradlew flywayClear
```

BUT remember: "With great power comes great responsibility".

<br/>

#### Repairing database migrations

There are two ways to generate an inconsistency in the Flyway leaving it in the transformed state:

1. Some migration SQL file with error;
2. Some already applied migration file has been changed (by modifying the `checksum`).

When this happens, the flyway will be in a controlled state.
with a record in the `flyway_schema_history` table in error (`success = 0`).

To run the build, fix the files and run:
``` shell
./gradlew flywayRepair
```

With the above command, Flyway will clean the records with error from the `flyway_schema_history` table,
then execute the FlywayMigrate command to try to migrate them again.

<br/>

#### Other useful Flyway commands

In addition to the commands already shown, we have some other very useful ones such as info and validate:

``` shell
./gradlew flywayInfo
./gradlew flywayValidate
```

To know all available commands: [Flyway Gradle Plugin](https://flywaydb.org/documentation/usage/gradle/info)

<br/>

#### To run the commands in another environment

There in `build.gradle` we configure Flyway to first read the variables from
environment `FLYWAY_DB`, `FLYWAY_USER` and `FLYWAY_PASS` and then use a default value
if you can't find it. With that, to point to another environment just overwrite
these variables when executing the commands, for example:

``` shell
FLYWAY_DB=jdbc:mysql://prod:3306/adm_videos FLYWAY_USER=root FLYWAY_PASS=123h1hu ./gradlew flywayValidate
```

### Running with Docker
To run the application locally with Docker, we will use `docker compose` and it only takes three steps:
<br/>

#### 1. Generating the productive extractor (jar)

To generate the productive output, just run the command:
```
./gradlew bootJar
```
<br/>

#### 2. Running the independent containers

To run MySQL and Rabbit, just run the command below.
```
docker-compose up -d
```
<br/>

#### 3. Running the application along with other containers

After seeing that the other containers are up, to run your application together, just run the command:
```
docker-compose --profile app up -d
```

> **Note:** If you need to rebuild the image of your application, an additional command is required:
>```
> docker compose build --no-cache app
>```
#### Stopping containers
To close the containers, just run the command:
```
docker compose --stop profiling app
```

### Keycloak

#### Setup
1. Add Keycloak container inside docker-compose
    ```
      keycloak:
        container_name: adm_videos_keycloak
        image: quay.io/keycloak/keycloak:20.0.3
        environment:
          - KEYCLOAK_ADMIN=admin
          - KEYCLOAK_ADMIN_PASSWORD=admin
        ports:
          - 8443:8080
        command:
          - start-dev
    ```
2. Upload the container and navigate to `http://localhost:8443/`
3. Create a new realm for the project: `fc3-codeflix`
4. Navigate to Realm settings > General > Endpoints
     - These endpoints are important for integration
5. Navigate to Realm settings > Keys
     - We will use the public key of the RS256 algorithm to verify the token
6. Create the client:
     - Client Id: fc3-admin-catalogo-de-videos
     - Client authentication: ON -- this makes access confidential
     - Redirect URL: confidential
     - Comment on the `client and secret` credentials that we will use for manual login
7. Create the role:
     - Role: catalogo-admin
     - Description: Role that gives admin permission to users
8. Create a group:
     - Name: catalogo-admin
     - Role mapping: assign `catalogo-admin`
9. Create a user:
     - Name: myuser
     - Groups: add to `catalogo-admin`
     - Create a credentials: `123456`
     

#### Integration
1. Add spring boot starter:
   ```
    implementation("org.springframework.boot:spring-boot-starter-security")
    implementation("org.springframework.boot:spring-boot-starter-oauth2-resource-server")

    testImplementation('org.springframework.security:spring-security-test')
   ```
2. Config Properties:
   ```properties
       keycloak:
           realm: fc3-codeflix
           host: http://localhost:8443
      
       spring:
           security:
               oauth2:
                   resourceserver:
                       jwt:
                           jwk-set-uri: ${keycloak.host}/realms/${keycloak.realm}/protocol/openid-connect/certs
                           issuer-uri: ${keycloak.host}/realms/${keycloak.realm}
   ```

## README.md PT-BR
- [README.md PT-BR](README-pt.md)

