FROM phusion/baseimage
MAINTAINER 4 All Digital  "joe@4alldigital.com"

# Required to run some apt-get commands (fix for Unable to locate package php5 issue)
ENV DEBIAN_FRONTEND noninteractive

# set UTF-8 locale
RUN locale-gen en_GB.UTF-8 && \
    echo 'LANG="en_GB.UTF-8"' > /etc/default/locale

RUN apt-get -qq update \
    && add-apt-repository ppa:openjdk-r/ppa \
    && apt-get -y update \
    && apt-get -q install -y --no-install-recommends \
    openjdk-7-jre \
    openjdk-7-jdk \
    unzip \
    && apt-get -y update \
    # cleanup
    && rm -fr /tmp/* /var/lib/apt/lists/* /var/tmp/* \
    && apt-get autoremove -y \
    && apt-get autoclean \
    && apt-get clean

# Install Java
RUN DEBIAN_FRONTEND=noninteractive \
    apt-get update \
    && apt-get -q install -y --no-install-recommends \
    && apt-get install -y --fix-missing \
    && apt-get install -y default-jre-headless wget \
    && apt-get autoremove -y \
    && apt-get autoclean \
    && apt-get clean

# Install Solr
ENV SOLR_HOME /opt/solr/example/solr
ENV SOLR_VERSION 4.10.1
ENV SOLR solr-$SOLR_VERSION
ENV SOLR_USER solr

RUN mkdir -p /opt/solr \
## tmp location during apache archive maintenance
    && wget --progress=bar:force https://distfiles.macports.org/apache-solr/$SOLR.tgz \
    && tar zxf $SOLR.tgz -C /opt/solr --strip-components 1

RUN groupadd -r $SOLR_USER \
    && useradd -r -g $SOLR_USER $SOLR_USER \
    && usermod -u 1000 $SOLR_USER \
    && usermod -a -G users $SOLR_USER \
    && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* $SOLR.tgz

EXPOSE 8983

# Configure runit
ADD ./svc /etc/service/
ADD ./config/indexes /opt/solr/example/solr
ADD ./config/jetty.xml /opt/solr/example/etc/jetty.xml
ADD ./config/webdefault.xml /opt/solr/example/etc/webdefault.xml
ADD ./config/realm.properties /opt/solr/example/etc/realm.properties

CMD ["/sbin/my_init"]
