FROM debian:8

ENV DEBIAN_FRONTEND noninteractive

# install required packages
RUN apt-get -y update
RUN apt-get install -y build-essential automake autoconf libtool wget libssl-dev libjansson-dev libxml2-dev libkrb5-dev git \
                       libtalloc-dev libcurl4-openssl-dev
# download Freeradius 3.0.3
RUN wget ftp://ftp.freeradius.org/pub/freeradius/old/freeradius-server-3.0.3.tar.gz \
    && tar -xvf freeradius-server-3.0.3.tar.gz
# copy the patch file and apply
COPY freeradius.patch .
RUN patch -i freeradius.patch -p1
# build and install
WORKDIR freeradius-server-3.0.3
RUN ./configure \
    && make -j10
# This is required since first run fails
RUN make install || make install
# configure clients
COPY client.conf .
RUN cat client.conf >> /usr/local/etc/raddb/clients.conf
# add dictionary entry for the DSRK-Req attribute
RUN sed -i "/SAML-AAA-Assertion/a ATTRIBUTE DSRK-Req 150 octets" /usr/local/share/freeradius/dictionary.ukerna
# add user
RUN echo 'test@home.org   Cleartext-Password := "testing"' >> /usr/local/etc/raddb/users
# configure erp_realm
RUN sed -i "/eap {/a erp_realm = home.org" /usr/local/etc/raddb/mods-enabled/eap
CMD ["/usr/local/sbin/radiusd", "-X"]
