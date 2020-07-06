FROM php:5.3-apache

#
# Install MSMTP for sending email
#
RUN apt-get update \
    && apt-get -y --force-yes install msmtp
COPY linux/msmtprc /etc/

#
# Install SSH
#
ARG SSH_PSWD=root
RUN apt-get update \
    && apt-get -y --force-yes install openssh-server \
    && sed -i 's/UsePAM yes/UsePAM no/g' /etc/ssh/sshd_config \
    && sed -i 's/#ClientAliveInterval 0/ClientAliveInterval 30/g' /etc/ssh/sshd_config \
    && sed -i 's/#ClientAliveCountMax 3/ClientAliveCountMax 1200/g' /etc/ssh/sshd_config \
    && sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' /etc/ssh/sshd_config \
    && sed -i 's/www-data:\/var\/www:\/usr\/sbin\/nologin/www-data:\/var\/www:\/bin\/bash/g' /etc/passwd \
    && echo "root:${SSH_PSWD}" | chpasswd \
    && echo "www-data:${SSH_PSWD}" | chpasswd
COPY linux/.bashrc /root/
COPY linux/.bashrc linux/.profile /var/www/

#
# Prepare for start
#
COPY docker-bootstrap /usr/local/bin/
EXPOSE 22

CMD ["docker-bootstrap"]