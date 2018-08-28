FROM ubuntu:16.04

# install curl and git 
RUN apt-get update -y
RUN apt-get install -y curl git

# install PHP
RUN apt-get install -y php7.0 php7.0-dev php7.0-xml

# install apt-transport-https (required for curling the msodbcsql packages. doesn't come w/ container)
RUN apt-get update
RUN apt-get install -y apt-transport-https

# add msodbcsql packages
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
RUN curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list

# install msodbcsql and associated tools
RUN apt-get update
RUN ACCEPT_EULA=Y apt-get install -y msodbcsql17
RUN ACCEPT_EULA=Y apt-get install -y mssql-tools

# add msssql-tools to path 
RUN echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
RUN echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc

RUN apt-get install -y unixodbc-dev

# add extension info to ini files
RUN echo extension=pdo_sqlsrv.so >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/30-pdo_sqlsrv.ini
RUN echo extension=sqlsrv.so >> `php --ini | grep "Scan for additional .ini files" | sed -e "s|.*:\s*||"`/20-sqlsrv.ini

# install sqlsrv
RUN pecl install sqlsrv
RUN pecl install pdo_sqlsrv

# install apache
RUN apt-get install -y libapache2-mod-php7.0 apache2

RUN a2dismod mpm_event
RUN a2enmod mpm_prefork
RUN a2enmod php7.0

# add sqlsrv extension info to apache2/php.ini
RUN echo "extension=sqlsrv.so" >> /etc/php/7.0/apache2/php.ini
RUN echo "extension=pdo_sqlsrv.so" >> /etc/php/7.0/apache2/php.ini

# copy 30-pdo_sqlsrv.ini to some locations for loading
RUN cp /etc/php/7.0/cli/conf.d/30-pdo_sqlsrv.ini /etc/php/7.0/fpm/conf.d
RUN cp /etc/php/7.0/cli/conf.d/30-pdo_sqlsrv.ini /etc/php/7.0/apache2/conf.d

# install locales (sqlcmd will have a fit if you don't have this)
RUN apt-get install -y locales && echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen

EXPOSE 80 7000

WORKDIR /var/www/html/

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
