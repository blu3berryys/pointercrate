# Pointercrate ![build](https://github.com/stadust/pointercrate/actions/workflows/test.yml/badge.svg) [![codecov](https://codecov.io/gh/stadust/pointercrate/branch/master/graph/badge.svg?token=C7B5LU2IF5)](https://codecov.io/gh/stadust/pointercrate)

As of March 2nd, 2019, this is the official repository for **Pointercrate**. It contains the core backend components of [pointercrate.com](https://pointercrate.com/), specifically the code for the demonlist and user area pages. It does **not** include the code for the home page, API documentation, or demonlist guidelines.

This repository serves as a framework for building custom **Pointercrate-like** websites. It is open source for developers who want to create their own demonlist platforms while avoiding the use of **Pointercrate's branding**. The home page and other branding elements are not open-sourced because of misuse by individuals who have hosted their own lists with Pointercrate branding, despite not being associated with the platform.

However, this repository includes an **example binary** that demonstrates how to use the various components to create a demonlist website. For more information, see the [Getting Started section](#getting-started) below.

### Exclusion of Pointercrate-specific Code
Removing Pointercrate-specific code is still a work in progress. For example, SEO-related metadata still references pointercrate.com. If you use this repository as a base, please ensure to update these references.

---

## Getting Started (Linux)

The goal of this section is to guide you in compiling and running the `pointercrate-example` binary target to set up a locally running **demonlist clone**.

We assume you have the **Rust toolchain** set up. If not, install the latest stable version via [`rustup`](https://rustup.rs/):

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
. "$HOME/.cargo/env"
```

### Database Setup

Pointercrate uses **PostgreSQL** as its database. This guide assumes you have a local PostgreSQL server running and a `pointercrate` user and database created for use.

To connect to the PostgreSQL database, Pointercrate uses [`sqlx`](https://github.com/launchbadge/sqlx). To compile the project, you need to have access to the database with Pointercrate's schema. The connection string is set via the `DATABASE_URL` environment variable, e.g.,

```bash
# If you created the pointercrate database and role (without a password but with login permissions),
# the connection string will be
export DATABASE_URL=postgresql://pointercrate@localhost/pointercrate
```

To set up the database with the required tables, apply the migrations using the following commands:

```bash
# Install sqlx-cli for migrations
cargo install sqlx-cli --no-default-features --features native-tls,postgres

# Apply migrations
cargo sqlx migrate run
```

### Pointercrate Configuration

Pointercrate is configured via environment variables, typically stored in a `.env` file in the working directory. It also requires a secret key for signing access tokens, which is stored in a `.secret` file.

1. Copy the sample `.env` file from `pointercrate-example`:
   ```bash
   cp pointercrate-example/.env.sample .env
   ```

2. Create a `.secret` file with the following content (for debug purposes only):
   ```bash
   echo "insecure-do-not-use-in-prod" > .secret
   ```

3. Open `.env` and fill out the fields that do not have default values (e.g., `DATABASE_URL`).

### Running `pointercrate-example`

At this point, you should be able to run the `pointercrate-example` binary via:

```bash
cargo run -p pointercrate-example
```

If everything is set up correctly, you should see Rocket's development server start up with the following output:

<details>
  <summary>Click to view output</summary>

```
    Finished dev [unoptimized + debuginfo] target(s) in 0.16s
     Running `target/debug/pointercrate-example`
🔧 Configured for debug.
   >> address: 127.0.0.1
   >> port: 1971
   >> workers: 12
   >> max blocking threads: 512
   >> ident: Rocket
   >> IP header: X-Real-IP
   >> limits: bytes = 8KiB, data-form = 2MiB, file = 1MiB, form = 32KiB, json = 1MiB, msgpack = 1MiB, string = 8KiB
   >> temp dir: /tmp
   >> http/2: true
   >> keep-alive: 5s
   >> tls: disabled
   >> shutdown: ctrlc = true, force = true, signals = [SIGTERM], grace = 2s, mercy = 3s
   >> log level: normal
   >> cli colors: true
📬 Routes:
   >> (home) GET /
   >> (login_page) GET /login
   >> (login) POST /login
   >> (account_page) GET /account
   >> (register) POST /register
   >> (overview) GET /demonlist/?<timemachine>&<submitter>
   >> (stats_viewer_redirect) GET /demonlist/?statsviewer=true
   >> (demon_page) GET /demonlist/<position>
   >> (stats_viewer) GET /demonlist/statsviewer
   >> (nation_stats_viewer) GET /demonlist/statsviewer/nations
   >> (demon_permalink) GET /demonlist/permalink/<demon_id>
   >> (heatmap_css) GET /demonlist/statsviewer/heatmap.css
   >> (FileServer: pointercrate-core-pages/static) GET /static/core/<path..> [10]
   >> (FileServer: pointercrate-user-pages/static) GET /static/user/<path..> [10]
   >> (FileServer: pointercrate-demonlist-pages/static) GET /static/demonlist/<path..> [10]
   >> (login) POST /api/v1/auth/
   >> (get_me) GET /api/v1/auth/me
   >> (patch_me) PATCH /api/v1/auth/me
   >> (delete_me) DELETE /api/v1/auth/me
   >> (register) POST /api/v1/auth/register
   >> (invalidate) POST /api/v1/auth/invalidate
   >> (verify_email) GET /api/v1/auth/verify_email?<token>
   >> (paginate) GET /api/v1/users/
   >> (get_user) GET /api/v1/users/<user_id>
   >> (patch_user) PATCH /api/v1/users/<user_id>
   >> (delete_user) DELETE /api/v1/users/<user_id>
   >> (paginate) GET /api/v2/demons/
   >> (post) POST /api/v2/demons/
   >> (paginate_listed) GET /api/v2/demons/listed
   >> (get) GET /api/v2/demons/<demon_id>
   >> (patch) PATCH /api/v2/demons/<demon_id>
   >> (audit) GET /api/v2/demons/<demon_id>/audit
   >> (post_creator) POST /api/v2/demons/<demon_id>/creators
   >> (movement_log) GET /api/v2/demons/<demon_id>/audit/movement
   >> (delete_creator) DELETE /api/v2/demons/<demon_id>/creators/<player_id>
   >> (paginate) GET /api/v1/records/
   >> (unauthed_pagination) GET /api/v1/records/
   >> (submit) POST /api/v1/records/
   >> (paginate) GET /api/v1/players/
   >> (paginate_claims) GET /api/v1/players/claims
   >> (ranking) GET /api/v1/players/ranking
   >> (delete) DELETE /api/v1/records/<record_id>
   >> (get) GET /api/v1/records/<record_id>
   >> (patch) PATCH /api/v1/records/<record_id>
   >> (get) GET /api/v1/players/<player_id>
   >> (patch) PATCH /api/v1/players/<player_id>
   >> (get_notes) GET /api/v1/records/<record_id>/notes
   >> (add_note) POST /api/v1/records/<record_id>/notes
   >> (audit) GET /api/v1/records/<record_id>/audit
   >> (put_claim) PUT /api/v1/players/<player_id>/claims
   >> (geolocate_nationality) POST /api/v1/players/<player_id>/geolocate
   >> (delete_note) DELETE /api/v1/records/<record_id>/notes/<note_id>
   >> (patch_note) PATCH /api/v1/records/<record_id>/notes/<note_id>
   >> (patch_claim) PATCH /api/v1/players/<player_id>/claims/<user_id>
   >> (delete_claim) DELETE /api/v1/players/<player_id>/claims/<user_id>
   >> (paginate) GET /api/v1/submitters/
   >> (get) GET /api/v1/submitters/<submitter_id>
   >> (patch) PATCH /api/v1/submitters/<submitter_id>
   >> (ranking) GET /api/v1/nationalities/ranking
   >> (nation) GET /api/v1/nationalities/<iso_code>
   >> (subdivisions) GET /api/v1/nationalities/<iso_code>/subdivisions
   >> (list_information) GET /api/v1/list_information/
🥅 Catchers:
   >> (catch_404) 404
📡 Fairings:
   >> Shield (liftoff, response, singleton)
   >> Maintenance (ignite, request)
🛡️ Shield:
   >> X-Content-Type-Options: nosniff
   >> Permissions-Policy: interest-cohort=()
   >> X-Frame-Options: SAMEORIGIN
🚀 Rocket has launched from http://127.0.0.1:1971
```

</details>

The last line indicates the URL for accessing your local Pointercrate instance (e.g., `127.0.0.1:1971`).

---

### Next Steps

If you're using **Pointercrate** as a framework for your own demonlist website, check out the sample code in [`pointercrate-example/src/main.rs`](pointercrate-example/src/main.rs). Start by replacing placeholder strings (such as `<your website>` with your domain), update the "Hello World" home page, and customize the **User Area**.

To access the demonlist administration interface, create an account and grant yourself administrator permissions via the PostgreSQL shell:

```bash
$ psql -U pointercrate pointercrate
psql (16.1)
Type "help" for help.

pointercrate=# -- assuming the user has member_id 1:
pointercrate=# UPDATE members SET permissions = '0100000000001000'::BIT(16) WHERE member_id = 1;  
```

After reloading the user area, you'll have access to all the administrative tabs for website and demonlist management.

---

## Running Integration Tests

Run the test suite with `cargo test` in the repository root. For running the example binary, a database with the Pointercrate schema must be available via the `DATABASE_URL` environment variable. It is recommended to use a separate database for tests, such as `pointercrate_test`, as this database is dropped and recreated for each test.

Integration tests are also run as part of the CI process on each pull request.

---

## Special Thanks

The following people have contributed to the development of **Pointercrate**:

- cos8o: Reverse engineered parts of Geometry Dash's source to display accurate object counts and level lengths.
- zmx, [mgostIH](https://github.com/mgostIH), and others in the Discord server.
- [Nimbus](https://github.com/NimbusGD): Developed various Discord bots integrating with the Pointercrate API.
- Aquatias, Deltablu, and Moosh: Trustworthy admins who help maintain the system.
- rSteel, zMarc, Stiluetto, and Zipi: Beloved staff members.
- And, of course, the developers of all the dependencies used by **Pointercrate**.
