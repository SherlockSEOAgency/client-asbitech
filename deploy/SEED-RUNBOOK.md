# SEED-RUNBOOK — asbitech werk-replica op Coolify

Bron-artefacten (op Hermes): `~/work/clients/asbitech/db/asbitech.sql.gz` (34 MB, GZ_OK) +
`~/work/clients/asbitech/files/` (847 MB, volledige WP-root incl. core).

1. **Coolify-app** (Docker Compose) uit repo `SherlockSEOAgency/client-asbitech`, domein
   `asbitech-wp.preview.sherlockseo.com`. Env in Coolify (niet in git):
   `WORDPRESS_DB_NAME`, `WORDPRESS_DB_USER`, `WORDPRESS_DB_PASSWORD`, `MYSQL_ROOT_PASSWORD`
   (random gegenereerd; ook opgeslagen in `hermes:~/work/clients/asbitech/coolify-creds.txt`, chmod 600).
2. **Deploy** → wordpress- + db-container up, lege volumes.
3. **Files seeden** in het `wp-html`-volume (host-mountpoint onder
   `/var/lib/docker/volumes/…_wp-html/_data`): rsync vanaf Hermes → Coolify-host → volume.
4. **DB seeden**: `zcat asbitech.sql.gz | docker exec -i <db> mariadb -u root -p… <dbname>`.
5. **wp-config patchen**: DB_HOST=`db:3306`, DB_NAME/USER/PASSWORD = de Coolify-env-creds,
   `table_prefix` = `yrue49hf_` behouden; salts behouden.
6. **Domein-switch (serialized-safe)**:
   `wp search-replace 'https://asbitech.eu' 'https://asbitech-wp.preview.sherlockseo.com' --all-tables --skip-columns=guid`
   (+ zonder scheme-variant), dan `wp option update blog_public 0`, `wp elementor flush-css`,
   `wp cache flush`.
7. **Verify (extern)**: `curl -sI https://asbitech-wp.preview.sherlockseo.com` → 200 + geldig TLS;
   render-vergelijk homepage/key-pages vs live; DB-rijtellingen == bron; geen resterende
   `asbitech.eu`-hits. Dan verse `sf_crawl` van de replica vs live-baseline `b7e2049c`.

Regels: nooit de Combell-live-site muteren; nooit de bestaande `asbitech-preview`-app raken;
secrets nooit in git/transcript.
