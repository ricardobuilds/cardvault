# CardVault Setup

This guide covers how to set up CardVault for local development.

CardVault runs the Rails application directly on the host machine and runs PostgreSQL in a Docker container.

These instructions have been tested on **macOS with Apple Silicon (ARM64)**.

## Prerequisites

CardVault's local development environment requires the following:

### Required

- **Ruby 3.4.10** — Ruby version used by the application
- **Bundler 2.6.9** — installs and manages the application's Ruby dependencies
- **Docker** — provides the container runtime for PostgreSQL
- **Docker Compose** — manages the local PostgreSQL service defined in `compose.yaml`

### Recommended Development Tools

- **rbenv** — manages and selects the project's Ruby version
- **Git** — used to clone the repository and manage source control
- **Homebrew** — recommended package manager for installing development tools on macOS

### macOS Requirements

- **Apple Command Line Tools** — provides Clang, Make, the linker, macOS SDK, and other development tools that may be required when compiling Ruby and gems with native extensions

On Apple Silicon Macs, native components are compiled for the ARM64 architecture.

### Project Versions

CardVault currently uses the following project-controlled versions:

- **Ruby 3.4.10** — specified by `.ruby-version`
- **Bundler 2.6.9** — specified by `Gemfile.lock`
- **Rails 8.1.3.1** — resolved and locked by `Gemfile.lock`
- **PostgreSQL 18** — specified by `compose.yaml`

Other Ruby gem versions are resolved and locked by `Gemfile.lock`.

> PostgreSQL is currently pinned to major version 18. An exact PostgreSQL 18.x version will be pinned separately after testing.

### Development Environment Tested With

- rbenv 1.3.2
- macOS on Apple Silicon (ARM64)

GitHub CLI (`gh`) is not required to build or run CardVault.

---

## Installation

The following instructions assume macOS and use rbenv to manage Ruby.

### 1. Install the Apple Command Line Tools

```bash
xcode-select --install
```

Verify the configured developer tools directory:

```bash
xcode-select -p
```

On an Apple Silicon Mac, verify the machine architecture:

```bash
uname -m
```

The output should be:

```text
arm64
```

### 2. Install Homebrew

If Homebrew is already installed, verify it with:

```bash
brew --version
```

If Homebrew is not installed, install it before continuing.

### 3. Install rbenv and ruby-build

Install rbenv and ruby-build:

```bash
brew install rbenv ruby-build
```

Initialize rbenv for your shell:

```bash
rbenv init
```

Follow the shell configuration instructions printed by rbenv, then restart the terminal if necessary.

Verify rbenv:

```bash
rbenv --version
```

### 4. Install Ruby

Install the Ruby version used by CardVault:

```bash
rbenv install 3.4.10
```

The repository contains a `.ruby-version` file specifying Ruby 3.4.10. Once inside the CardVault directory, rbenv should automatically select this version.

Verify it with:

```bash
ruby --version
```

### 5. Install Docker

Install Docker Desktop for macOS and start Docker.

Verify Docker:

```bash
docker --version
```

Verify Docker Compose:

```bash
docker compose version
```

### 6. Verify Git

Git is normally available on macOS after installing the Apple Command Line Tools.

Verify it with:

```bash
git --version
```

### 7. Clone CardVault

Clone the CardVault repository:

```bash
git clone <repository-url>
```

Enter the project directory:

```bash
cd cardvault
```

Verify that rbenv selected the project's Ruby version:

```bash
ruby --version
```

The output should report Ruby 3.4.10.

### 8. Install Bundler

CardVault's `Gemfile.lock` specifies **Bundler 2.6.9**.

Install the required version:

```bash
gem install bundler -v 2.6.9
```

Verify it:

```bash
bundle --version
```

The output should report:

```text
Bundler version 2.6.9
```

### 9. Install Ruby Dependencies

Install the dependencies defined by `Gemfile` and `Gemfile.lock`:

```bash
bundle install
```

This installs Rails and the other Ruby gems required by CardVault. Rails does not need to be installed separately.

### 10. Configure Environment Variables

Create your local `.env` file from the repository template:

```bash
cp .env.example .env
```

The template contains:

```text
POSTGRES_DB=cardvault_development
POSTGRES_USER=cardvault
POSTGRES_PASSWORD=your_local_password_here
POSTGRES_PORT=5432
```

Replace `your_local_password_here` with a password for your local PostgreSQL development environment.

Do not commit `.env` to Git.

### 11. Start PostgreSQL

Make sure Docker Desktop is running.

Start the PostgreSQL service:

```bash
docker compose up -d
```

CardVault currently uses the `postgres:18` image. Docker Compose creates and starts the PostgreSQL container, configures its networking and port mapping, and attaches the persistent `postgres_data` volume.

Verify the service is running:

```bash
docker compose ps
```

### 12. Prepare the Databases

Prepare the Rails databases:

```bash
bin/rails db:prepare
```

The local configuration uses separate development and test databases:

```text
Development: cardvault_development
Test:        cardvault_test
```

### 13. Start CardVault

Start Puma and the Rails application:

```bash
bin/rails server
```

By default, Puma listens on port `3000`.

The local architecture is:

```text
Client
   │
   │ HTTP :3000
   ▼
Puma
   │
   ▼
Rails on macOS
   │
   │ localhost:${POSTGRES_PORT}
   ▼
Docker host-port mapping
   │
   │ → container port 5432
   ▼
PostgreSQL 18
```

CardVault is currently configured as an API-only Rails application.

### 14. Verify the Setup

With Rails running, verify that the application boots without database connection errors.

Open a Rails console:

```bash
bin/rails console
```

Exit the console with:

```ruby
exit
```

Run the test suite:

```bash
bin/rails test
```

Because CardVault is an API-only application and may not yet have a root endpoint, successfully starting Rails does not necessarily mean visiting `localhost:3000` in a browser will display a web page.

### 15. Stop the Local Environment

Stop the Rails server with `Control-C`.

Stop PostgreSQL:

```bash
docker compose down
```

The `postgres_data` Docker volume persists PostgreSQL data after the container is stopped or removed.

---

## Everyday Development

After the initial installation, the full setup process does not need to be repeated each time you work on CardVault.

### Start PostgreSQL

```bash
docker compose up -d
```

### Start Rails

```bash
bin/rails server
```

### Run the Test Suite

```bash
bin/rails test
```

### Open the Rails Console

```bash
bin/rails console
```

### Check PostgreSQL Container Status

```bash
docker compose ps
```

### View PostgreSQL Logs

```bash
docker compose logs db
```

### Stop PostgreSQL

```bash
docker compose down
```

---

## Database Management

### Prepare the Database

```bash
bin/rails db:prepare
```

`db:prepare` ensures the required database exists and prepares its schema.

### Run New Migrations

```bash
bin/rails db:migrate
```

Use this after pulling or creating migrations that have not yet been applied locally.

### PostgreSQL Data Persistence

The Compose configuration uses the named Docker volume:

```text
postgres_data
```

This keeps PostgreSQL data outside the lifecycle of an individual container.

Running:

```bash
docker compose down
```

removes the Compose containers and network but preserves the named volume and its database data.

Be careful with commands that explicitly remove Docker volumes. Removing `postgres_data` destroys the PostgreSQL data stored in that local development volume.

---

## Troubleshooting

### Wrong Ruby Version

Check which Ruby executable your shell is using:

```bash
which ruby
```

Check the Ruby executable selected by rbenv:

```bash
rbenv which ruby
```

Verify the version:

```bash
ruby --version
```

If rbenv-installed executables are not being resolved correctly, rebuild the rbenv shims:

```bash
rbenv rehash
```

Also verify that rbenv is correctly initialized in your shell.

### Wrong Bundler Version

Check the active Bundler version:

```bash
bundle --version
```

CardVault expects:

```text
Bundler version 2.6.9
```

If necessary, install it explicitly:

```bash
gem install bundler -v 2.6.9
```

### Docker Is Not Running

If `docker compose` cannot connect to Docker, make sure Docker Desktop is running.

Verify with:

```bash
docker info
```

### PostgreSQL Container Is Not Running

Check the service:

```bash
docker compose ps
```

Inspect its logs:

```bash
docker compose logs db
```

### Port 5432 Is Already in Use

The default `.env.example` configuration maps host port `5432` to PostgreSQL's container port `5432`.

If another PostgreSQL instance or application already uses host port `5432`, change:

```text
POSTGRES_PORT=5432
```

in `.env` to an available host port.

The container still listens internally on port `5432`; only the host-side port changes.

### Missing Environment Variables

If Rails or Docker Compose reports missing `POSTGRES_*` variables, verify that `.env` exists:

```bash
ls -la .env
```

If necessary, recreate it from the template:

```bash
cp .env.example .env
```

Then configure the local PostgreSQL password.

### PostgreSQL Authentication Problems

Verify that the values in `.env` match the credentials used when the PostgreSQL Docker volume was originally initialized.

PostgreSQL initialization variables such as the database name, username, and password are used when PostgreSQL initializes a new data directory. Changing those values later does not automatically recreate an existing database stored in `postgres_data`.

Inspect PostgreSQL logs for additional information:

```bash
docker compose logs db
```

Do not delete the PostgreSQL volume as a troubleshooting step unless you intentionally want to destroy and recreate your local development database.

---

## Local Development Architecture

CardVault intentionally uses a hybrid local development environment:

```text
macOS
│
├── Ruby 3.4.10
├── Bundler 2.6.9
├── Rails 8.1.3.1
├── Puma
│
│   localhost:${POSTGRES_PORT}
│
└───────────────┐
                ▼
        Docker port mapping
                │
                ▼
        PostgreSQL 18 container
                │
                ▼
        postgres_data volume
```

Rails runs directly on macOS while PostgreSQL runs inside Docker. This keeps the Rails development workflow local while providing an isolated and reproducible PostgreSQL environment.
