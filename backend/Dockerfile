FROM laravelsail/php83-composer

# Instala a extensão do PostgreSQL para PHP
RUN apt-get update \
    && apt-get install -y libpq-dev \
    && docker-php-ext-install pdo_pgsql
