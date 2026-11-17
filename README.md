# Lar-News

A small news portal on Laravel 8 with news categories and news items managed in the Orchid admin panel.

## Context

A personal project by Oleksandr Karabudka from 2021 (news schema and admin work from May to July 2021, committed in July 2021). It was built to try out Orchid as an admin panel for content management and to practise Eloquent model observers and events in Laravel 8.

## Features

- Public news list at `/news` showing active news items with their preview image.
- News categories with title, slug and active flag.
- News items with category, title, slug, display date, active flag, body text, preview image and main image.
- Orchid admin screens for categories and news items: sortable list with a text filter on the title, and edit forms using the Quill editor, image upload (`Picture`), date picker and category relation.
- Orchid user and role management and Orchid example screens.
- A model observer that dispatches a `NewsEvent` on every create, update, delete, restore and force delete of a news item; the event is mapped to a queued listener (currently an empty stub), and the database queue table is migrated.
- Factories for news categories and news items.

## Tech stack

- PHP 7.3+ / 8.0, Laravel 8
- Orchid Platform 10
- MySQL 5.7, MailHog (Docker Compose services)
- Laravel Mix
- Docker Compose (nginx 1.20, PHP-FPM 8.0, Node.js)

## Architecture

- Admin UI in `app/Orchid`: screens in `Screens/News`, layouts in `Layouts/News`, a `NewsFilter` and a `NewsSelection`; routes in `routes/platform.php`.
- Models `News` and `NewsCategory`; images are stored as Orchid attachments referenced by `preview_image` and `image` columns.
- `NewsItemObserver` is registered in `EventServiceProvider` and turns model lifecycle hooks into a single `NewsEvent` carrying the model and the event name, so side effects can be added as listeners without touching the model.
- The public part is a single controller (`App\Http\Controllers\News`) with a Blade view.

## Getting started

With Docker:

```bash
sh docker/conf/ssl/generate.sh        # create the local self-signed TLS certificate
cp .env.example .env                  # then set the DB_* values (DB_HOST=db for the db container)
docker compose up -d web db           # the php service starts automatically with web
docker compose exec php composer install -n
docker compose exec php php artisan key:generate
docker compose exec php php artisan migrate
docker compose exec php php artisan orchid:link
docker compose exec php php artisan orchid:admin <name> <email> <password>
```

The `php` and `node` services use the official `php:8.0-fpm` and `node:15-alpine` images. The official PHP image has no `pdo_mysql` extension, so add it (`docker-php-ext-install pdo_mysql`) in your own image before running migrations.

Without Docker:

```bash
cp .env.example .env                  # then set the DB_* values
composer install -n
php artisan key:generate
php artisan migrate
php artisan orchid:link
```

Open `/news` for the public list and `/admin` for the admin panel.

## Testing

There is one feature-style test, `tests/Unit/Http/Controllers/NewsTest.php`, which checks that `/news` shows active items and hides inactive ones. It reads existing news rows and does not seed or refresh the database itself, so data must be present.

```bash
php artisan test
```

## Status

Personal project from 2021, not maintained.

## License

MIT, see [LICENSE](LICENSE).
