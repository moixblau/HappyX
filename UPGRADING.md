# Upgrading HappyX

Most updates are just pulling the new images (see [Self-host](README.md#self-host)). This file
lists the ones that need something more.

## Postgres 17 to 18

Stacks from before 23 October 2026 ran Postgres 17 on the `db_data` volume. A new major version
cannot read the old data files, so the stack now uses a new volume (`db_data_pg18`) and the data
is moved with a dump. The old `db_data` volume is left untouched, so going back is just
redeploying the previous stack file.

1. Before redeploying, dump the running database:
   `docker exec <db container> pg_dump -U happyx -d happyx | gzip > happyx-pg17.sql.gz`
   (the latest file in the backups folder works too).
2. Redeploy with the new `deploy/stack.yml`. Postgres 18 starts empty and the API creates the
   tables.
3. Stop the `api` and `bot` containers, then restore:

   ```sh
   docker exec -i <db container> psql -U happyx -d happyx -c 'DROP SCHEMA public CASCADE; CREATE SCHEMA public;'
   gunzip -c happyx-pg17.sql.gz | docker exec -i <db container> psql -U happyx -d happyx
   ```

4. Start `api` and `bot` again. Once everything looks right, delete the old `db_data` volume.

For local development, the same applies to `docker compose`: dump, `docker compose up -d`,
restore. Or start from an empty database.

## Stack file renamed

`deploy/portainer-stack.yml` is now `deploy/stack.yml`. A Portainer stack deployed from this
repository needs its compose path changed to `deploy/stack.yml` (Stacks > your stack > Editor >
Repository settings, or recreate it). The backup timezone is now the `TZ` variable (default
`UTC`); set it to keep the previous schedule.
