# WordPress Plugin Template

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/DeschutesDesignGroupLLC/wordpress-plugin-template)

## Launching a Local WordPress Docker Development Environment

Get your project dependencies by executing the following command.

```bash
yarn install && composer install
```

Start and initialize the Docker containers.

```bash
docker-compose up --build
```

### WordPress Installation

Execute the provided bash script to automate WordPress installation and plugin activation.

For additional configuration and setup, modify the WP-CLI script found in `docker/cli/install-wordpress`.

```bash
docker-compose run --rm cli install-wordpress
```

**Admin Details**<br>
Username: wordpress<br>
Password: wordpress

### Building for Production

Compile your assets for production with the command:

```bash
npm run build
```

## Command-Line Interface (CLI)

Access the WP-CLI with the following commands:

#### WP-CLI

```bash
docker-compose run --rm cli wp [command]
```

#### Laravel Artisan

The Acorns package includes several Laravel Artisan commands. You can find a current list [here](https://roots.io/acorn/docs/wp-cli/).

```bash
docker-compose run --rm cli wp acorn [artisan:command]
```

## Database

#### Migrations

Migrations can be created just like a default Laravel application.

```bash
docker-compose run --rm cli wp acorn make:migration create_this_table
docker-compose run --rm cli wp acorn migrate
```

**NOTE**: Running `migrate:fresh` will drop all database tables, including your WordPress core tables. You may run the [install](#wordpress-installation) script to reinstall WordPress and recreate all the database tables while maintaining your app's migrations.

#### Seeding

Because the WordPress plugin operates in a different namespace than standard Laravel convention, you will need to pass the seeder class to the `db:seed` command so that the container will resolve the correct class.

```bash
docker-compose run --rm cli wp acorn db:seed --class=WordpressPluginTemplate\\Database\\Seeders\\DatabaseSeeder
```

## Environment Variables

You can load and set environment variables using a `.env` file located in the `src` directory. Rename `.env.example` to `.env` to get started. The file will be automatically loaded by the application.

Any environment variable files by default will be excluded from your package. Make sure to set default values or modify `scripts/package.sh` to include `*.env` files when packaging.

## Preparing the Plugin for Distribution

WordPress' plugins sometimes encounter dependency namespace issues. To tackle this, it's advisable to prefix dependency namespaces with your own.

**Note:** Remember to [compile](#building-for-production) your assets for production.

Use PHP-Scoper for this task. Install PHP-Scoper PHAR via Phive:

```bash
phive install humbug/php-scoper --force-accept-unsigned
```

Run PHP-Scoper to prefix dependency namespaces and export the plugin files to the `build` folder.

```bash
composer prefix
```

Next, dump the Composer autoloader so everything works as expected.

```bash
composer dump-autoload --working-dir build --classmap-authoritative
```

Afterward, package the plugin for distribution:

```bash
npm run package
```

## Additional Commands

Run the PHP linter. This will lint all files in the `src` directory. The linter uses Laravel Pint. The config can be edited from `pint.json`.

```bash
composer lint
```

Run static analysis. This will analyze `yourplugin.php` and everything in `src/app/...` using PHPStan. The config can be edited from `phpstan.neon`.

```bash
composer test:types
```

Run the JS linter and format the code. The linter uses ESLint and the config can be edited from `.eslintrc.cjs`. The code formatter uses Prettier and the config can be edited from `.prettierrc
`.
```bash
npm run lint
npm run format
```