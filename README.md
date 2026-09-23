# client-asbitech — werk-replica van asbitech.eu

**Doel:** een 1:1 werk-replica van de live site (Combell) op onze Coolify-infra, zodat we
technische/on-page/semantische/content-optimalisaties kunnen doorvoeren en teruggeven aan de
klant-dev. **De klant blijft live op Combell** — dit is géén cutover.

Classificatie: **platform-patroon** (site-takeover/replica). Volgt `docs/SOP-SITE-TAKEOVER.md`
in de hoofdrepo. Werk-URL: `https://asbitech-wp.preview.sherlockseo.com` (noindex).

## Stack (bron, gemeten 2026-09-23)
WordPress 7.1.2 · PHP 8.3 · MariaDB. Elementor + Elementor Pro + Ultimate/Unlimited Elements +
Envato · WPML (NL/FR) · ACF Pro · WP All Import Pro · Yoast · Redirection · Wordfence · ~30 plugins.

## Wat zit hier wél / niet in git
- **Wel:** `docker-compose.yaml` (WP + MariaDB), deze runbook.
- **Niet:** de site-content (wp-content/uploads, 847 MB), de DB-dump (34 MB), secrets/salts.
  Die worden geseed vanuit `hermes:~/work/clients/asbitech/` en leven in Coolify-volumes/env.

## Belangrijk
- **Elementor + WPML** → domein-switch alleen met `wp search-replace --all-tables`
  (serialized-safe). Nooit platte SQL/sed.
- **`blog_public=0`** (noindex) zolang dit een replica is.
- Front-end rendert zonder Pro-licenties; editen in de stack vereist ze (aparte licentielijst).

Seeding-stappen: `deploy/SEED-RUNBOOK.md`.
