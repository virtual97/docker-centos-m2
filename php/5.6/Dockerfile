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
    php5.6 \
    php5.6-cgi \
    php5.6-fpm \
    php5.6-xml \
    php5.6-gd \
    php5.6-soap \
    php5.6-intl \
    php5.6-mbstring \
    php5.6-mcrypt \
    php5.6-mysql \
    php5.6-zip \
    php5.6-json \
    php5.6-opcache \
    php5.6-curl \
    php-oauth \
    php-xdebug

RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
# RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

RUN curl -sS https://getcomposer.org/installer | \
  php -- --install-dir=/usr/local/bin --filename=composer

RUN sed -i '/^listen /c \
listen = 0.0.0.0:9005' /etc/php/5.6/fpm/pool.d/www.conf

# Add cron jobs
ADD jobs /etc/cron.d/jobs
RUN chmod 0644 /etc/cron.d/jobs

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

ENTRYPOINT service php5.6-fpm start && service cron start && service ssh start && /bin/bash
# EXPOSE 9000

# Custom_start (add scripts_permissions)
ADD xd_swi /usr/local/bin/xd_swi
RUN chmod 0744 /usr/local/bin/xd_swi
RUN phpdismod xdebug && service php5.6-fpm restart
# Custom_end (add scripts_permissions)