# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project purpose

Teaching skeleton for ITU's *Introduction to Database Systems* course: a small coffee shop web app (Javalin + DuckDB over JDBC). Several data-access methods in `App.java` are deliberately left as `// TODO` stubs for students to implement (`insertUser`, `insertPurchase`, `getUserPurchases`). Don't fill them in unless explicitly asked — they are the exercise.

## Commands

Requires Java 17+, Maven, and the DuckDB CLI.

```bash
duckdb coffee.db < init.sql   # create/reset the database (drops and recreates all tables)
mvn compile                   # build
mvn exec:java                 # run (main class introdb.App), serves http://localhost:8080
```

There are no tests and no linter configured. Run from the repo root: the JDBC URL is the relative path `jdbc:duckdb:./coffee.db`. `coffee.db` is gitignored.

## Architecture

Everything lives in `src/main/java/introdb/App.java`, organized in three layers:

1. **SQL query methods** — each opens its own `DriverManager.getConnection(CONNECTION)` in try-with-resources, runs one statement, and maps rows into the private records `Product` / `Purchase`. Methods taking user input use `PreparedStatement`; errors are caught, printed, and turned into `false`/empty list.
2. **Page rendering methods** — build HTML by hand with `StringBuilder`, wrapped by the shared `header(title, username)` / `footer()` helpers (nav links change depending on login state).
3. **Form handlers** — read `ctx.formParam(...)`, call the SQL layer, then redirect or return an error status.

Routes are registered in `main()`. Static files (`index.html`, `login.html`, `register.html`, `style.css`, images) are served from the classpath root (`src/main/resources`). The static HTML pages duplicate the header/footer markup by hand, so nav or footer changes need to be made both in `header()`/`footer()` and in those files.

Auth state is a single session attribute, `"username"`; pages check it for `null` to decide logged-in behavior.

## Database

Schema and seed data are in `init.sql`: tables `user(username PK, email, password)`, `product(productname PK, price, description)`, `purchase(purchasetime, productname, username)` with a composite primary key over all three. Column names are lowercase with no underscores; types use DuckDB's `string`.

## Code style

Google Java style (VS Code is pointed at the Google Eclipse formatter in `.vscode/settings.json`), with the existing file using 4-space indentation and 100-column lines. Every method has a Javadoc comment.
