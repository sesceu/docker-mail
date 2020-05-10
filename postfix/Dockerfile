FROM ubuntu:20.10

ENV DEBIAN_FRONTEND noninteractive

# Prerequisites
RUN apt-get update && apt-get install -y \
    locales \
    netcat \
    rsyslog \
    ssl-cert \
    postfix \
    dovecot-imapd \
    dovecot-sieve \
    dovecot-managesieved \
    dovecot-ldap \	
    postgrey \
    jq && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    echo "#!/bin/sh\nexit 0" > /usr/sbin/policy-rc.d

RUN locale-gen en_US en_US.UTF-8 && dpkg-reconfigure locales

# Postfix configuration
ADD ./config/postfix /etc/postfix/
RUN cat /etc/postfix/master-additional.cf >> /etc/postfix/master.cf

# Dovecot configuration
COPY ./config/dovecot /etc/dovecot/conf.d/
COPY ./config/rsyslog.conf /etc/rsyslog.conf

# Copy boot scripts
COPY boot /
COPY boot.d /boot.d
RUN chmod 755 /boot /boot.d/*

# Nice place for your settings
VOLUME ["/mail_settings"]

# Volume to store email
VOLUME ["/vmail"]

# Volume to store sieve-rules
VOLUME ["/sieve"]

# Volume for postgrey's whitelists
VOLUME ["/etc/postgrey"]

# Volume for postgrey's database
VOLUME ["/var/lib/postgrey"]

# Add user vmail that ownes mail and sieve
RUN groupadd -g 5000 vmail
RUN useradd -g vmail -u 5000 vmail -d /vmail -m

# Enable AMAVIS and OPEN_DKIM
ENV AMAVIS 1
ENV OPEN_DKIM 1

EXPOSE 25 143 587 993
CMD /boot; service postgrey start; service postfix start; service dovecot start; rsyslogd -n
HEALTHCHECK CMD nc -z localhost 25 && nc -z localhost 143 && nc -z localhost 587 && nc -z localhost 993 && nc -z localhost 4190 && echo "" | openssl s_client -starttls smtp -connect localhost:25 | openssl x509 -noout -checkend 86400 || exit 1
