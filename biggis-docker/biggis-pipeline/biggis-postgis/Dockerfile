FROM biggis/base:alpine-3.4

MAINTAINER Karsten Schnitter <karsten.schnitter@disy.net>

ARG ALPINE_VERSION=3.4

ARG BUILD_DATE
ARG VCS_REF

LABEL eu.biggis-project.build-date=$BUILD_DATE \
      eu.biggis-project.license="MIT" \
      eu.biggis-project.name="BigGIS" \
      eu.biggis-project.url="http://biggis-project.eu/" \
      eu.biggis-project.vcs-ref=$VCS_REF \
      eu.biggis-project.vcs-type="Git" \
      eu.biggis-project.vcs-url="https://github.com/biggis-project/biggis-infrastructure" \
      eu.biggis-project.environment="dev" \
      eu.biggis-project.version=$ALPINE_VERSION

ENV POSTGIS_VERSION 2.2.2 

RUN apk update
RUN apk upgrade

RUN apk add --update --no-cache postgresql postgresql-contrib bash 
RUN export PATH=/usr/local/pgsql/bin/:$PATH
RUN apk add --update --no-cache postgresql-dev \
                                perl file geos@testing geos-dev@testing  \
                                libxml2-dev gdal@testing gdal-dev@testing \
                                proj4@testing proj4-dev@testing \
                                gcc make libgcc g++ && \
    cd /tmp && \
    wget http://download.osgeo.org/postgis/source/postgis-${POSTGIS_VERSION}.tar.gz -O - | tar -xz && \
    chown root:root -R postgis-${POSTGIS_VERSION} && \
    cd /tmp/postgis-${POSTGIS_VERSION} && \
    ./configure && \
    echo "PERL = /usr/bin/perl" >> extensions/postgis/Makefile && \
    echo "PERL = /usr/bin/perl" >> extensions/postgis_topology/Makefile && \
    make -s && \ 
	make -s install && \ 
	cd / && \ 
	rm -rf /tmp/postgis-${POSTGIS_VERSION} && \ 
	apk del postgresql-dev perl file geos-dev \ 
	        libxml2-dev gdal-dev proj4-dev \ 
			gcc make libgcc g++ 

RUN mkdir /docker-entrypoint-initdb.d

ENV LANG en_US.utf8
ENV PGDATA /var/lib/postgres/data

ADD docker-entrypoint.sh /

ENTRYPOINT ["/docker-entrypoint.sh"]			
			
EXPOSE 5432 

CMD ["postgres"]
