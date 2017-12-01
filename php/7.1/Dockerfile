FROM ubuntu:16.04

#custom_start
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections
#custom_end

RUN DEBIAN_FRONTEND=noninteractive \
  apt-get update && \
  apt-get install -y language-pack-en-base &&\
  export LC_ALL=en_US.UTF-8 && \
  export LANG=en_US.UTF-8

RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y software-properties-common
RUN DEBIAN_FRONTEND=noninteractive LC_ALL=en_US.UTF-8 add-apt-repository ppa:ondrej/php
  
RUN DEBIAN_FRONTEND=noninteractive LC_ALL=en_US.UTF-8 \
  apt-get update \
  && apt-get upgrade -y --allow-unauthenticated \
  && apt-get install -y --allow-unauthenticated \
    openssh-server \
    curl \
    cron \
    nano \
    zip \
    unzip \
    php7.1 \
    php7.1-cgi \
    php7.1-fpm \
    php7.1-xml \
    php7.1-gd \
    php7.1-soap \
    php7.1-intl \
    php7.1-mbstring \
    php7.1-mcrypt \
    php7.1-mysql \
    php7.1-zip \
    php7.1-json \
    php7.1-opcache \
    php7.1-curl \
    php-xdebug \
    ffmpeg \
	php7.1-bcmath

RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
# RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

RUN curl -sS https://getcomposer.org/installer | \
  php -- --install-dir=/usr/local/bin --filename=composer

RUN curl -LsS https://symfony.com/installer -o /usr/local/bin/symfony
RUN chmod a+x /usr/local/bin/symfony

RUN wget https://phar.phpunit.de/phpunit-6.0.phar
RUN chmod +x phpunit-6.0.phar
RUN mv phpunit-6.0.phar /usr/local/bin/phpunit

RUN sed -i '/^listen /c \
listen = 0.0.0.0:9001' /etc/php/7.1/fpm/pool.d/www.conf

# COPY jobs.txt /etc/cron.d/jobs
# ADD jobs.txt /etc/cron.d/jobs
# RUN chmod 0644 /etc/cron.d/jobs

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

ENTRYPOINT service php7.1-fpm start && service cron start && service ssh start && /bin/bash
# EXPOSE 9000
