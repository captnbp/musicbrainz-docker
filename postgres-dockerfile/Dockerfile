FROM postgres:9.3
MAINTAINER benoit.pourre@gmail.com

RUN DEBIAN_FRONTEND=noninteractive

# Update the Ubuntu and PostgreSQL repository indexes
RUN apt-get update

# Install ``python-software-properties``, ``software-properties-common`` and PostgreSQL 9.3
#  There are some warnings (in red) that show up during the build. You can hide
#  them by prefixing each apt-get statement with DEBIAN_FRONTEND=noninteractive
RUN apt-get -y -q install libxml2-dev libpq-dev libexpat1-dev libdb-dev libicu-dev postgresql-server-dev-9.3 postgresql-contrib-9.3 git-core build-essential postgresql-plperl-9.3

RUN git clone https://github.com/metabrainz/postgresql-musicbrainz-unaccent.git && git clone https://github.com/metabrainz/postgresql-musicbrainz-collate.git

RUN cd postgresql-musicbrainz-unaccent && make && make install && cd ../postgresql-musicbrainz-collate && make && make install && cd ../


# Expose the PostgreSQL port
EXPOSE 5432

VOLUME /var/lib/postgresql/data

ENTRYPOINT ["/docker-entrypoint.sh"]

CMD ["postgres"]
