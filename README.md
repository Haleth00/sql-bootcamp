# SQL Bootcamp

My notes, practice queries, and setup for **The Complete SQL Bootcamp: Go from Zero to Hero** by Jose Portilla (Udemy).

The goal is to learn SQL well enough to query, join, and analyze data with confidence, using PostgreSQL on my own machine.

## Environment

| Piece | Choice |
|---|---|
| OS | NixOS (flake-based config) |
| Database | PostgreSQL 16 |
| GUI | pgAdmin 4 (web, `http://localhost:5050`) |
| Sample data | `dvdrental` (fictional DVD rental store) |

## Setup on NixOS

Add this to `configuration.nix`:

```nix
services.postgresql = {
  enable = true;
  package = pkgs.postgresql_16;

  # Learning machine only: no passwords on localhost.
  authentication = lib.mkOverride 10 ''
    local all all trust
    host  all all 127.0.0.1/32 trust
    host  all all ::1/128      trust
  '';
};

services.pgadmin = {
  enable = true;
  initialEmail = "<your-email>";
  initialPasswordFile = pkgs.writeText "pgadmin-pass" "<throwaway-password>";
};
```

Rebuild and check that the server is up:

```bash
sudo nixos-rebuild switch --flake /etc/nixos#nixos
systemctl status postgresql
```

Set a password for the `postgres` user (matches the course's pgAdmin form):

```bash
sudo -u postgres psql -c "ALTER USER postgres PASSWORD '<password>';"
```

> The `trust` auth setting is only safe because PostgreSQL listens on localhost. Don't use it on a shared or exposed machine, and don't reuse a real password in the Nix config, since the Nix store is world-readable.

### Connect pgAdmin

Open `http://localhost:5050`, then Servers → Register → Server:

- Host: `localhost`
- Port: `5432`
- Maintenance database: `postgres`
- Username: `postgres`

### Restore the sample database

Download `dvdrental.zip` from the course resources, unzip it, then:

```bash
createdb -h localhost -U postgres dvdrental
pg_restore -h localhost -U postgres -d dvdrental dvdrental.tar
psql -h localhost -U postgres -d dvdrental -c "select count(*) from film;"   # expect 1000
```

## Repo layout

```
.
├── README.md
├── queries/     # practice queries, one .sql file per lecture or topic
└── notes/       # short notes on concepts worth remembering
```

The `dvdrental` files are not committed. Download them from the course resources.

## Progress

Sections are added here as I work through the course.

- [ ] Setup and installation
- [ ] ...

## Resources

- [PostgreSQL documentation](https://www.postgresql.org/docs/)
- [pgAdmin documentation](https://www.pgadmin.org/docs/)
