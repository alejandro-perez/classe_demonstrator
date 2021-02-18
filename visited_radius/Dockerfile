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
RUN ./configure
RUN make -j10
# This is required since first run fails
RUN make install || make install
# configure clients
COPY client.conf .
RUN cat client.conf >> /usr/local/etc/raddb/clients.conf
# add dictionary entry for the DSRK-Req attribute
RUN sed -i "/SAML-AAA-Assertion/a ATTRIBUTE DSRK-Req 150 octets" /usr/local/share/freeradius/dictionary.ukerna
# configure proxies
COPY proxy.conf .
RUN cat proxy.conf >> /usr/local/etc/raddb/proxy.conf
# configure erp_realm
RUN sed -i "/eap {/a erp_realm = visited1.org" /usr/local/etc/raddb/mods-enabled/eap
# enable EAP pre-proxy functionality
RUN sed -i "/pre-proxy {/a eap" /usr/local/etc/raddb/sites-enabled/default
CMD ["/usr/local/sbin/radiusd", "-X"]
