FROM debian:9

ENV DEBIAN_FRONTEND noninteractive

# install packages
RUN apt-get update \
    && apt-get install -y build-essential automake autoconf libtool wget libssl1.0-dev libjansson-dev libxml2-dev libkrb5-dev git krb5-gss-samples
# install moonshot UI
RUN wget https://repository.project-moonshot.org/debian-moonshot/moonshot-repository.debian9.deb \
    && dpkg -i moonshot-repository.debian9.deb \
    && apt-get -y update && apt-get -y install moonshot-ui-dev libradsec-dev moonshot-ui
# clone the mech_eap for what the patch was meant to
RUN git clone https://github.com/janetuk/mech_eap.git --branch v0.9.5
# download the libeap patch and apply it
WORKDIR mech_eap
RUN wget https://www.um.es/classe/files/libeap.patch \
    && patch -p2 -i libeap.patch
# apply the mech_eap patch. The one from the webpage does not work with this version of mech_eap, so this is a slightly
# modified version
COPY mech_eap.patch .
RUN patch -p1 -i mech_eap.patch
# build mech_eap and install it
RUN autoreconf -fi \
    && ./configure --without-shibresolver --without-opensaml \
    && make -j10 install
# put the mech_eap library in its proper location
RUN ln -s /usr/local/lib/gss /usr/lib/x86_64-linux-gnu/
# configure the gss library to find the mech_eap mechanism
RUN cp mech_eap/mech /etc/gss/mech.d/moonshot.conf
# configure the RADIUS proxy
COPY radsec.conf /usr/local/etc
# For simplicity, hardcode the EAP identity
COPY credential.xml .
RUN moonshot-webp credential.xml
CMD while true; do gss-server -verbose a@service; done
