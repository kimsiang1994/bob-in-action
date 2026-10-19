# Microservices (invoice preview service)

Bob's bounded proof that invoice preview can be pulled out of the monolith into its own Spring Boot service on Java 21 (`src/main/java/demo/preview/PreviewApplication.java`, routes `GET /health` and `POST /invoice-preview`), with contract tests. The monolith keeps everything else. It is one feature, not a full migration.

## Build and test

Needs Maven and a Java 21 JDK. From the repository root:

```powershell
mvn -B -f targets/microservices/pom.xml verify
```

Verified here: 43 tests, `BUILD SUCCESS`, with Temurin 21 and Maven 3.10.

## Run

`java -jar targets/microservices/target/preview-service.jar --server.address=127.0.0.1 --server.port=18085`
