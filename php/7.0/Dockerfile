FROM ubuntu:16.04

#custom_start
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections
#custom_end

RUN apt-get update \
  && apt-get upgrade -y \
  && apt-get install -y \
    openssh-server \
    curl \
    cron \
    nano \
    zip \
    unzip \
    php7.0 \
    php7.0-cgi \
    php7.0-fpm \
    php7.0-xml \
    php7.0-gd \
    php7.0-soap \
    php7.0-intl \
    php7.0-mbstring \
    php7.0-mcrypt \
    php7.0-mysql \
    php7.0-zip \
    php7.0-json \
    php7.0-opcache \
    php-xdebug \
    php7.0-curl \
	php7.0-bcmath \
	git

# Setting openssh
RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
# RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

RUN curl -sS https://getcomposer.org/installer | \
  php -- --install-dir=/usr/local/bin --filename=composer

RUN curl -s https://packagecloud.io/install/repositories/phalcon/stable/script.deb.sh | bash
RUN apt-get update && apt-get install php7.0-phalcon

RUN sed -i '/^listen /c \
listen = 0.0.0.0:9000' /etc/php/7.0/fpm/pool.d/www.conf

# COPY jobs.txt /etc/cron.d/jobs
ADD jobs /etc/cron.d/jobs
RUN chmod 0644 /etc/cron.d/jobs

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

ENTRYPOINT service php7.0-fpm start && service cron start && service ssh start && /bin/bash
# EXPOSE 9000

# ===== Custom_start =====
#[add scripts_permissions_start]
# Configure xDebug
ADD xd_swi /usr/local/bin/xd_swi
RUN chmod 0744 /usr/local/bin/xd_swi
RUN phpdismod xdebug && service php7.0-fpm restart

# Adding git user
RUN adduser --system git
RUN mkdir -p /root/.ssh
#[add scripts_permissions_end]


#[install sendmail_start]
# set up sendmail config, see http://linux.die.net/man/5/ssmtp.conf for options
RUN mkdir /etc/ssmtp
RUN echo "hostname=localhost.localdomain" > /etc/ssmtp/ssmtp.conf
RUN echo "root=virtual977@gmail.com" >> /etc/ssmtp/ssmtp.conf
RUN echo "mailhub=maildev" >> /etc/ssmtp/ssmtp.conf
# The above 'maildev' is the name you used for the link command
# in your docker-compose file or docker link command.
# Docker automatically adds that name in the hosts file
# of the container you're linking MailDev to.

# Set up php sendmail config
RUN echo "sendmail_path=sendmail -i -t" >> /usr/local/etc/php/conf.d/php-sendmail.ini

# Fully qualified domain name configuration for sendmail on localhost.
# Without this sendmail will not work.
# This must match the value for 'hostname' field that you set in ssmtp.conf.
RUN echo "localhost localhost.localdomain" >> /etc/hosts
#[install sendmail_end]
# ===== Custom_end =====
