# DevU

DevU is an automated software-grading platform being developed at the University at Buffalo. DevU aims to be incredibly
extensible, allowing professors to add any functionality they desire without reaching a dead end. It will eventually
replace Autolab and other services.

This repo contains everything necessary to run the entirety of DevU in Docker:

* The DevU back-end API
* The build process for the DevU front-end web client
* An Nginx web server to host the front-end files
* A PostgreSQL database
* A configuration builder for the back-end API

## Getting Started

This process might seem like a lot if you're new to many these technologies, but I promise it's at least 10 times
easier than manually getting DevU running on your system without Docker.

### 1. Install Docker
You'll need to have [Docker](https://docs.docker.com/get-docker/) installed, which now includes Docker Compose.
Docker allows everyone to run software in an equivalent environment, so we don't run into differences between operating
systems or tool versions. The installation varies depending on your operating system, so follow the appropriate guide on
their website.

### 2. Clone this repo to your computer.
Open a terminal.
* On Windows, press `Windows + R` to open a run prompt, type "`cmd`", and press enter.
* On MacOS, open the Launchpad (on your Dock) and launch "Terminal".
* On Linux, you know.

You'll need to have Git installed. It's usually preinstalled on Linux and MacOS. There's a good chance you've already
installed Git if you're using Windows. You can verify the installation by running `git --version` in the terminal.

If you need to install Git, you can follow the appropriate instructions for your operating system [here](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

I highly recommend [setting up an SSH key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)
for GitHub. It's not strictly necessary, but I'll assume you've properly configured an SSH key for this guide.

In the terminal, (optionally create and) navigate to the directory you'd like to clone this repo to.
```bash
mkdir demo
cd demo
```

Clone the repo.

```bash
git clone git@github.com:makeopensource/devU.git
```

Enter the project repo.

```bash
cd devU
```

### 3. Run the Project with Docker Compose
```bash
docker compose up --build
```

### 4. Connect to the Web Client
In your browser, connect to `http://localhost:9000`, and you should see the login screen.

If you only see the text "error," wait a few seconds; the backend takes some time to start running. If it takes longer
than a minute, review the output in your terminal for errors, most notably: [Why is the backend service failing to start right after cloning the repo?](#why-is-the-backend-service-failing-to-start-right-after-cloning-the-repo)

## Frequently Asked Questions

### Why is the backend service failing to start right after cloning the repo?
It seems that sometimes the database migrations aren't made when they should be. If you see the following, try
re-running `docker compose up --build` a few times.
```
Error during migration run:
Error: Configuration property "auth.jwt.activeKeyId" is not defined
at Config.get (/app/node_modules/config/lib/config### Why is the devU-client/devU-api repo empty?
```

If this is the case, you would get an error similar to this:

```
[+] Building 0.0s (0/0)
unable to prepare context: path "<repo>/<filename>" not found
    We are using `submodules` for this project, which means that both `devU-client/` and `devU-api/` are links to other repos. Make sure you follow the directions for cloning carefully: use the `--recurse-submodules` option when cloning.

    **If you already cloned the project**, run the command `git submodule update --init --recursive`, which should pull the appropriate submodules for you.
    .js:182:11)
```

It usually works within 5 runs. I haven't been able to narrow down why this happens. Further investigation is welcome!

You should see the following once it successfully makes the migrations:
```
query: SELECT * FROM "information_schema"."tables" WHERE "table_schema" = current_schema() AND "table_name" = 'migrations'
query: CREATE TABLE "migrations" ("id" SERIAL NOT NULL, "timestamp" bigint NOT NULL, "name" character varying NOT NULL, CONSTRAINT "PK_8c82d7f526340ab734260ea46be" PRIMARY KEY ("id"))
query: SELECT * FROM "migrations" "migrations" ORDER BY "id" DESC
0 migrations are already loaded in the database.
8 migrations were found in the source code.
8 migrations are new migrations that needs to be executed.
query: START TRANSACTION
query: CREATE TABLE "users" ("id" SERIAL NOT NULL, "createdAt" TIMESTAMP NOT NULL DEFAULT now(), "updatedAt" TIMESTAMP NOT NULL DEFAULT now(), "deletedAt" TIMESTAMP, "username" character varying(32) NOT NULL, "name" character varying(128) NOT NULL, "email" character varying(128) NOT NULL, CONSTRAINT "users_primary_key_constraint" PRIMARY KEY ("id"))
```
