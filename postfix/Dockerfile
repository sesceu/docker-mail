FROM ubuntu:latest

ENV DEBIAN_FRONTEND noninteractive
ENV AMAVIS 1
ENV OPEN_DKIM 1

# Prerequisites
RUN apt-get update && apt-get install -y --no-install-recommends \
      locales \
      netcat \
      rsyslog \
      ssl-cert \
      postfix \
      dovecot-imapd \
      dovecot-sieve \
      dovecot-managesieved \
      dovecot-ldap \
      jq \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN locale-gen en_US en_US.UTF-8 && dpkg-reconfigure locales

# Postfix configuration
ADD ./config/postfix /etc/postfix/
RUN cat /etc/postfix/master-additional.cf >> /etc/postfix/master.cf

# Dovecot configuration
COPY ./config/dovecot /etc/dovecot/conf.d/
COPY ./config/rsyslog.conf /etc/rsyslog.conf

# Copy boot scripts
COPY ./boot.sh /
COPY ./boot.d /boot.d
RUN chmod 755 /boot.sh /boot.d/*

# Nice place for your settings
VOLUME ["/mail_settings"]

# Volume to store email
VOLUME ["/vmail"]

# Volume to store sieve-rules
VOLUME ["/sieve"]

# Add user vmail that ownes mail and sieve
RUN groupadd -g 5000 vmail
RUN useradd -g vmail -u 5000 vmail -d /vmail -m

EXPOSE 25 143 587 993
CMD /boot.sh; service postfix start; service dovecot start; rsyslogd -n
HEALTHCHECK CMD nc -z localhost 25 && nc -z localhost 143 && nc -z localhost 587 && nc -z localhost 993 && nc -z localhost 4190 && echo "" | openssl s_client -starttls smtp -connect localhost:25 | openssl x509 -noout -checkend 86400 || exit 1
