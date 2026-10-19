# Kotlin (invoice preview)

Bob's bounded proof that the invoice-preview feature can be rebuilt in Kotlin: a small HTTP preview server (`src/main/kotlin/PreviewServer.kt`) with its own test runner (`src/test/kotlin/PreviewTests.kt`). It is one feature, not a full migration.

## Build and test

Needs the Kotlin compiler (`kotlinc`) and a Java 21 JDK. From the repository root:

```powershell
kotlinc targets/kotlin/src/main/kotlin/PreviewServer.kt -include-runtime -d targets/kotlin/build/preview-service.jar
kotlinc targets/kotlin/src/main/kotlin/PreviewServer.kt targets/kotlin/src/test/kotlin/PreviewTests.kt -include-runtime -d targets/kotlin/build/preview-tests.jar
java -cp targets/kotlin/build/preview-tests.jar PreviewTestsKt
```

Verified here: compiles with kotlinc 2.4.21 on Temurin 21; the test runner prints `17 passed, 0 failed`.

## Run

`java -jar targets/kotlin/build/preview-service.jar --host 127.0.0.1 --port 18082`
