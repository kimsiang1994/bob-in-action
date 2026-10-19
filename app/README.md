# Ingram Trials invoice desk (the legacy app)

This is the starting point: a small, deliberately imperfect Java 8 application that Bob assesses. It is a synthetic distributor invoice desk with customer search, invoice preview and save, credit notes and scheduled jobs. It has the kinds of problems real inherited code has: coupling, duplicated rules, stale documentation, weak tests, unsafe source patterns and code that may or may not still be used.

Treat it as read-only. The Bob commands read it and write their reports under `runs/<name>/`; they never change this folder.

## Layout

- `pom.xml`, `src/` — the Spring Boot 2.7 application (Java 8, H2 database, browser UI under `src/main/resources/static/`).
- `docs/`, `scripts/` — material that came with the application. Some of it is out of date on purpose.
- `Build.ps1`, `Start.ps1`, `Stop.ps1`, `Run-Job.ps1` — Windows PowerShell helpers to build, run and stop it locally.

## Build and run (optional)

You do not need to build the app to run the Bob commands. To see it working you need Java 8 (JDK) and Maven.

```powershell
.\app\Build.ps1                 # mvn clean verify with Java 8
.\app\Start.ps1                 # builds, starts on http://127.0.0.1:18080/ and waits for /api/health
.\app\Run-Job.ps1 -Job invoices # trigger a scheduled job by hand
.\app\Stop.ps1                  # stops the server Start.ps1 started
```

The scripts look for Java 8 in `JAVA8_HOME` (default `C:\Program Files\Eclipse Adoptium\jdk-8.0.504.1-hotspot`) and Maven in `MAVEN_CMD`, then `mvn` on `PATH`. On macOS or Linux, run Maven directly with a Java 8 JDK: `mvn -f app/pom.xml clean verify`.

The server binds to `127.0.0.1` only. Data lives in `app/runtime/` (ignored by git). The data is synthetic; there are no real customers or credentials.
