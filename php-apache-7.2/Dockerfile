FROM ubuntu:latest
MAINTAINER virtualone

RUN apt-get update && apt-get -y upgrade && DEBIAN_FRONTEND=noninteractive apt-get -y install \
apache2 apache2-utils php7.2 php-mysql libapache2-mod-php curl nano/*

RUN apt-get install mariadb-common mariadb-server mariadb-client -y

RUN a2enmod php7.2
RUN a2enmod rewrite

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

# Update the PHP.ini file, enable <? ?> tags and quieten logging.
RUN sed -i "s/short_open_tag = Off/short_open_tag = On/" /etc/php/7.2/apache2/php.ini
RUN sed -i "s/max_execution_time = 30/max_execution_time = 300/" /etc/php/7.2/apache2/php.ini
RUN sed -i "s/max_input_time = 60/max_input_time = 300/" /etc/php/7.2/apache2/php.ini
RUN sed -i "s/memory_limit = 128M/memory_limit = 1024M/" /etc/php/7.2/apache2/php.ini
RUN sed -i "s/upload_max_filesize = 2M/upload_max_filesize = 100M/" /etc/php/7.2/apache2/php.ini
RUN sed -i "s/post_max_size = 8M/post_max_size = 108M/" /etc/php/7.2/apache2/php.ini
RUN sed -i "s/error_reporting = .*$/error_reporting = E_ERROR | E_WARNING | E_PARSE/" /etc/php/7.2/apache2/php.ini

# Manually set up the apache environment variables
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid
ENV APACHE_RUN_DIR /var/run/apache2

RUN service apache2 start

RUN curl --insecure https://getcomposer.org/composer.phar -o /usr/bin/composer && chmod +x /usr/bin/composer

# Set timezone
RUN rm /etc/localtime
RUN ln -s /usr/share/zoneinfo/Asia/Karachi /etc/localtime
RUN "date"

# Update the default apache site with the config we created.
ADD ./site.com.conf /etc/apache2/sites-enabled/000-default.conf

#COPY ./site.com.conf /etc/apache2/sites-available/urbansky7.com.conf
#COPY ./site.com.conf /etc/apache2/sites-enabled/urbansky7.com.conf
#RUN a2ensite site.com.conf

RUN service apache2 restart

RUN mkdir bootstrap

ADD start.sh /bootstrap/
RUN chmod +x /bootstrap/start.sh

EXPOSE 80
ENTRYPOINT ["/bootstrap/start.sh"]
