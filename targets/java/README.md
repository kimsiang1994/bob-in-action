# Java 8 to Java 21 (invoice preview)

Bob's bounded migration of one feature, invoice preview, from the legacy Java 8 app to Java 21. It is a strangler-style change, not a full migration:

- `preview-sidecar/` — a new Spring Boot service on Java 21 that calculates invoice previews.
- `app/` — the legacy Java 8 app, changed only so its preview route calls the sidecar. Everything else (saving invoices, credit notes, jobs) is unchanged.

Bob wrote the code. Build, API, browser, rollback and unaffected-workflow checks were run on it independently; human production acceptance is still pending.

## Build and test

Needs Maven, a Java 21 JDK for the sidecar and a Java 8 JDK for the app. From the repository root:

```powershell
# sidecar (Java 21): 12 tests
$env:JAVA_HOME = '<path to JDK 21>'
mvn -B -f targets/java/preview-sidecar/pom.xml verify

# gateway app (Java 8): 1 test
$env:JAVA_HOME = '<path to JDK 8>'
mvn -B -f targets/java/app/pom.xml verify
```

Verified here: both builds pass (sidecar 12 tests, app 1 test) with Temurin 21 and 8 and Maven 3.10.

## Run

Start the sidecar (`java -jar targets/java/preview-sidecar/target/preview-sidecar-1.0.0.jar`, port 18081, set `SIDECAR_PORT` to change it), then the app (`java -jar targets/java/app/target/base-desk-1.0.0.jar`, port 18080; it finds the sidecar through `SIDECAR_URL`). Both bind to `127.0.0.1`.

Known and kept on purpose: the legacy preview applies the bulk discount at quantity 100, while saving applies it above 100. The migration preserves that difference rather than silently changing business behaviour.
