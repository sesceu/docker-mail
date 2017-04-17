FROM ubuntu:latest

ENV DEBIAN_FRONTEND noninteractive

# Prerequisites
RUN apt-get update && apt-get install -y --no-install-recommends \
       locales \
       netcat \
       opendkim \
       opendkim-tools \
       openssl \
       rsyslog \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN locale-gen en_US en_US.UTF-8 && dpkg-reconfigure locales

COPY ./config/rsyslog.conf /etc/rsyslog.conf
COPY ./config/opendkim /etc/opendkim
COPY ./config/opendkim.conf etc/opendkim.conf

# Nice place for your settings
VOLUME ["/mail_settings"]

# Configure boot script
COPY ./boot.sh /
# And the key generation script
COPY ./keygen /
RUN chmod 755 /boot.sh /keygen

ENV OPEN_DKIM=true

EXPOSE 8891
CMD /boot.sh; opendkim -p inet:8891@0.0.0.0; rsyslogd -n
HEALTHCHECK CMD nc -z localhost 8891 || exit 1
