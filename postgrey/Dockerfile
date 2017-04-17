FROM ubuntu:latest

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       locales \
       netcat \
       rsyslog \
       postgrey \
       jq \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN locale-gen en_US en_US.UTF-8 && dpkg-reconfigure locales

# Copy boot scripts
COPY ./boot.sh /
COPY ./boot.d /boot.d
RUN chmod 755 /boot.sh /boot.d/*

# Nice place for your settings
VOLUME ["/mail_settings"]

# Volume for postgrey's whitelists
VOLUME ["/etc/postgrey"]

# Volume for postgrey's database
VOLUME ["/var/lib/postgrey"]

EXPOSE 25 143 587 993
CMD /boot.sh; postgrey --inet=127.0.0.1:10023; rsyslogd -n
HEALTHCHECK CMD nc -z localhost 10023 || exit 1
