FROM php:8.4-apache

RUN \
    # Met à jour la liste des paquets disponibles
    apt-get update \        
    
    # Installe les paquets listés sans poser de questions (-y)
    && apt-get install -y \
    # Librairie de compression, utile pour plusieurs extensions PHP
    zlib1g-dev \
    # Compilateur C++ (souvent nécessaire pour compiler des extensions PECL)
    g++ \
    # Outil de versioning, utile dans de nombreux contextes de dev (pour Composer)
    git \
    # Requis pour l’extension PHP `intl` (Internationalization)
    libicu-dev \
    # Utilitaire zip (ligne de commande)
    zip \
    # Librairie nécessaire pour compiler l’extension PHP `zip`
    libzip-dev \
    
    # Compile et installe les extensions PHP natives
    && docker-php-ext-install \
    # Extension pour la gestion des locales et des formats internationaux
    intl \
    # Améliore les performances PHP via un cache d’opcode
    opcache \
    # Driver PDO pour MySQL
    pdo_mysql \
    
    # Installe l’extension APCu via PECL (cache partagé)
    && pecl install apcu \
    
    # Active l’extension APCu (création d’un fichier .ini dans /usr/local/etc/php/conf.d)
    && docker-php-ext-enable apcu \
    
    # Configure l’extension `zip` (si nécessaire, ici redondant avec `libzip-dev` déjà installé)
    && docker-php-ext-configure zip \
    
    # Installe l’extension PHP `zip`
    && docker-php-ext-install zip \

    # Supprime le cache des fichiers .deb téléchargés par apt
    && apt-get clean \

    # Supprime les fichiers de métadonnées que apt-get update a téléchargés (listes de paquets disponibles)
    && rm -rf /var/lib/apt/lists/*

# Copy SSL certificate and key
COPY ./cert/localhost.crt /etc/ssl/certs/localhost.crt
COPY ./cert/localhost.key /etc/ssl/private/localhost.key

# Active le module SSL d’Apache (nécessaire pour le HTTPS)
RUN a2enmod ssl \
    # Active le module de réécriture d’URL
    && a2enmod rewrite

WORKDIR /var/www

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \

    && curl -sS https://get.symfony.com/cli/installer | bash \
    && mv /root/.symfony5/bin/symfony /usr/local/bin/symfony