<p align="center">
  <img src="src/main/resources/coffeeshop-logo.png" alt="Coffee Shop logo" width="200">
</p>

# Coffee Shop

A small web app for a coffee shop. It's used in ITU's *Introduction to Database Systems* course. Users can register, log in, browse products, buy drinks, and see the purchase history. The app is written in Java with [Javalin](https://javalin.io) as the web server and talks to a [DuckDB](https://duckdb.org) database over JDBC.

## Quick start

You need **Java 17+**, **Maven**, **Git**, and the **DuckDB CLI**. The commands below are for Linux, macOS, and the Windows Subsystem for Linux (WSL). If you use PowerShell or Command Prompt, please ask your teaching assistant.

```bash
git clone git@github.com:ITU-DASYALab/introdb-coffeeshop.git
cd introdb-coffeeshop
duckdb coffee.db < init.sql   # create the database
mvn compile                   # build
mvn exec:java                 # start the server
```

Then open [http://localhost:8080](http://localhost:8080). You can log in as `Anna`, `Martin`, or `Omar`; all three have the password `test`.

Always start the app from the repository root, because it looks for `./coffee.db` in the current directory. To reset the database, run `duckdb coffee.db < init.sql` again. It drops and recreates all tables.

<details>
<summary>Installing the prerequisites</summary>

```bash
sudo apt install default-jdk maven git   # Java, Maven, Git (Debian/Ubuntu/WSL)
curl https://install.duckdb.org | sh     # DuckDB CLI
```

</details>

## Your tasks

Three methods in `App.java` are left for you to implement. They are marked with `// TODO`:

| Method | What it should do |
|---|---|
| `insertUser` | Insert a newly registered user |
| `insertPurchase` | Insert a purchase with the current timestamp |
| `getUserPurchases` | Return one user's purchases, newest first |

Look at the methods that are already done, such as `loginUser` and `getAllPurchases`, and follow the same pattern. Use a `PreparedStatement` whenever a query includes user input.

## How the app works

All of the Java code is in [`src/main/java/introdb/App.java`](src/main/java/introdb/App.java), which is split into three parts:

1. **SQL query methods.** Each one opens its own connection to DuckDB, runs one query, and turns the rows into the `Product` or `Purchase` records.
2. **Page rendering methods.** These build the HTML pages as strings, using the shared `header()` and `footer()` helpers. The links in the navigation bar depend on whether someone is logged in.
3. **Form handlers.** These read the submitted form fields, call the SQL methods, and then redirect or return an error.

The logged-in user is stored in the session attribute `"username"`. The static pages (`index.html`, `login.html`, `register.html`, `style.css`, images) are in [`src/main/resources`](src/main/resources).

| Route | Description |
|---|---|
| `GET /products` | List products, with a *Buy* button when logged in |
| `GET /purchases` | All purchases |
| `GET /my-purchases` | The logged-in user's purchases |
| `POST /login`, `POST /register`, `GET /logout` | Log in, register, and log out |
| `POST /purchase` | Buy a product |

## Database

The schema and sample data are in [`init.sql`](init.sql):

```
user(username PK, email, password)
product(productname PK, price, description)
purchase(purchasetime, productname, username)   -- PK: all three columns
```
