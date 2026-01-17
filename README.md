# vfox-php

A [vfox](https://github.com/version-fox/vfox) / [mise](https://mise.jdx.dev) plugin for managing [PHP](https://www.php.net/) versions.

## Features

- **Dynamic version fetching**: Automatically fetches available versions from PHP's GitHub repository
- **Always up-to-date**: No static version list to maintain
- **Compiles from source**: Uses official PHP source releases
- **Automatic Composer**: Installs Composer after PHP
- **Cross-platform**: Works on Linux and macOS

## Requirements

### macOS (Homebrew)

```bash
xcode-select --install
brew install autoconf bison re2c pkg-config \
    libxml2 openssl@3 icu4c zlib libzip oniguruma \
    freetype jpeg libpng webp gmp libsodium readline bzip2
```

### Debian/Ubuntu

```bash
sudo apt-get install build-essential autoconf bison re2c pkg-config \
    libxml2-dev libssl-dev libicu-dev libzip-dev libonig-dev \
    libcurl4-openssl-dev libpng-dev libjpeg-dev libfreetype6-dev \
    libwebp-dev libgmp-dev libsodium-dev libreadline-dev libbz2-dev
```

### RHEL/CentOS/Fedora

```bash
sudo dnf groupinstall "Development Tools"
sudo dnf install autoconf bison re2c pkgconfig \
    libxml2-devel openssl-devel libicu-devel libzip-devel oniguruma-devel \
    libcurl-devel libpng-devel libjpeg-devel freetype-devel \
    libwebp-devel gmp-devel libsodium-devel readline-devel bzip2-devel
```

## Installation

### With mise

```bash
mise install php@latest
mise install php@8.4.17
mise install php@8.3.15
```

### With vfox

```bash
vfox add php
vfox install php@latest
```

## Usage

```bash
# List all available versions
mise ls-remote php

# Install a specific version
mise install php@8.4.17

# Set global version
mise use -g php@8.4.17

# Set local version (creates .mise.toml)
mise use php@8.4.17
```

## Environment Variables

This plugin sets the following environment variables:

- `PATH` - Adds the PHP bin and sbin directories
- `LD_LIBRARY_PATH` - Adds PHP lib directory (Linux only)

## Configuration

### Custom configure options

Add extra configure options:

```bash
PHP_EXTRA_CONFIGURE_OPTIONS="--with-pdo-sqlite" mise install php@8.4.17
```

Or override all configure options (prefix is always added):

```bash
PHP_CONFIGURE_OPTIONS="--with-openssl --enable-mbstring" mise install php@8.4.17
```

## Included Extensions

The default build includes many common extensions:

- **Database**: mysqli, pdo_mysql, pdo_pgsql (if libpq available)
- **String**: mbstring, intl, gettext, iconv
- **Compression**: zlib, bz2, zip
- **Crypto**: openssl, sodium
- **Graphics**: gd (with freetype, jpeg, png, webp)
- **Other**: curl, bcmath, calendar, exif, fpm, pcntl, soap, sockets

## How It Works

This plugin:

1. Fetches available versions from [github.com/php/php-src](https://github.com/php/php-src) tags
2. Downloads the source tarball for the requested version
3. Runs `./buildconf --force` to generate configure script
4. Configures with platform-appropriate options
5. Compiles with `make && make install`
6. Installs Composer

## Troubleshooting

### Missing dependencies on macOS

If you see errors about missing libraries, install them via Homebrew:

```bash
brew install libxml2 openssl@3 icu4c
```

### Configure fails with "Package not found"

Make sure pkg-config can find your libraries. The plugin sets PKG_CONFIG_PATH automatically for Homebrew packages, but you may need to install `pkg-config`:

```bash
brew install pkg-config
```

### Build takes a long time

PHP compilation typically takes 5-15 minutes depending on your hardware. The plugin uses parallel compilation (`make -j`) to speed this up.

## License

MIT License - see [LICENSE](LICENSE) for details.
