FROM ubuntu:latest
MAINTAINER Jeff Sturgis <jeffsturgis@gmail.com>
RUN (echo "deb http://archive.ubuntu.com/ubuntu  $(lsb_release -sc) main restricted universe multiverse" > /etc/apt/sources.list && echo "deb http://archive.ubuntu.com/ubuntu/  $(lsb_release -sc)-updates main restricted universe multiverse" >> /etc/apt/sources.list && echo "deb http://archive.ubuntu.com/ubuntu/  $(lsb_release -sc)-backports main restricted universe multiverse" >> /etc/apt/sources.list && echo "deb http://archive.ubuntu.com/ubuntu/  $(lsb_release -sc)-security main restricted universe multiverse" >> /etc/apt/sources.list)
RUN apt-get update

RUN DEBIAN_FRONTEND=noninteractive 
RUN apt-get install -y git-core postgresql-9.3 postgresql-plperl-9.3
RUN apt-get install -y memcached redis-server build-essential libxml2-dev libpq-dev libexpat1-dev libdb-dev libicu-dev liblocal-lib-perl cpanminus

RUN git clone --recursive https://github.com/metabrainz/musicbrainz-server.git musicbrainz-server && cd musicbrainz-server && git checkout schema-change-20-to-21
RUN echo "eval $( perl -Mlocal::lib )"
RUN echo "eval $( perl -Mlocal::lib )" >> ~/.bashrc && . ~/.bashrc
RUN cpanm https://cpan.metacpan.org/authors/id/E/ET/ETHER/namespace-autoclean-0.22.tar.gz
RUN cpanm TURNSTEP/DBD-Pg-3.4.2.tar.gz
RUN cpanm http://search.cpan.org/CPAN/authors/id/E/ET/ETHER/Test-Warnings-0.020.tar.gz
RUN apt-get install -y libdatetime-perl libtest-warnings-perl libnamespace-clean-perl libcatalyst-perl libnet-dns-perl libmoosex-types-common-perl libhtml-formhandler-perl
#RUN cpanm http://search.cpan.org/CPAN/authors/id/D/DR/DROLSKY/DateTime-Format-Strptime-1.56.tar.gz
RUN cpanm http://search.cpan.org/CPAN/authors/id/D/DR/DROLSKY/DateTime-Format-Builder-0.81.tar.gz
RUN cpanm http://search.cpan.org/CPAN/authors/id/D/DM/DMAKI/DateTime-Format-Pg-0.16010.tar.gz
#RUN apt-get install -y libnet-dns-perl libmoosex-types-common-perl libhtml-formhandler-perl
RUN cat /musicbrainz-server/Makefile.PL | grep ^requires >> cpanfile
RUN cpanm --installdeps --notest --force .
RUN cpanm --notest SARTAK/MooseX-Role-Parameterized-0.27.tar.gz Plack::Middleware::Debug::Base Catalyst::Plugin::Cache::HTTP Catalyst::Plugin::StackTrace Cache::Memcached::Fast JSON::Any Cache::Memory Digest::MD5::File Term::Size::Any 
RUN apt-get install -y cron

ADD DBDefs.pm /musicbrainz-server/lib/
ADD scripts/start.sh /start.sh
ADD scripts/crons.conf /crons.conf
ADD scripts/replication.sh /replication.sh
ADD scripts/createdb.sh /createdb.sh

RUN crontab /crons.conf

EXPOSE 5000
VOLUME  ["/media/dbdump"]
WORKDIR /musicbrainz-server
CMD ["/start.sh"]
