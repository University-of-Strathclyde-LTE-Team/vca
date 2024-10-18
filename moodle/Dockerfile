FROM moodlehq/moodle-php-apache:8.3

RUN apt update

RUN apt install cron -y
RUN apt install nano -y

# Copy PHP ini default configuration.
#COPY /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini
COPY ./moodle.ini /usr/local/etc/php/conf.d/10-docker-php-moodle.ini
# Send Docker environment variables to etc so they're available for things like cron.
RUN env >> /etc/environment

RUN git clone -b MOODLE_405_STABLE git://git.moodle.org/moodle.git /var/www/html/
COPY config.php /var/www/html/config.php

# Now copy AI plugins
RUN git clone https://github.com/mhughes2k/moodle-local_ai.git /var/www/html/local/ai
RUN git clone https://github.com/mhughes2k/moodle-search_solrrag.git /var/www/html/search/engine/solrrag
RUN git clone https://github.com/mhughes2k/moodle-mod_xaichat.git /var/www/html/mod/xaichat

# Copy crontab
COPY crontab /var/www/
# Install crontab
RUN crontab /var/www/crontab 