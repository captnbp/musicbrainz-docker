Musicbrainz docker container
==================

This repo contains everything needed to run a musicbrainz slave server with replication in a docker container.

First, thanks to Jeff Sturgis <jeffsturgis@gmail.com> with this repository : https://github.com/jsturgis/musicbrainz-docker

### Installation
* `sudo fig build`

### Update volumes setup ###
*  vim fig.yml
 
###### Start
* `sudo fig up` 

### Create Database
If this is a new instance and you need to create the database:

* `sudo docker exec -ti musicbrainz bash`
* `cd /`
* `./createdb.sh`

### Handling Schema Updates
When there is a schema change you will need to follow the directions posted by the musicbrainz team to update the schema.
You can run bash in the running musicbrainz container like this:

`sudo docker exec -ti musicbrainz bash`

The usual process to update the schema is:

* Ensure you’ve replicated up to the most recent replication packet available with the old schema. (if you’re not sure, run ./admin/replication/LoadReplicationChanges and see what it tells you).
* In the running container Switch to the new code with git fetch origin followed by git checkout $NEW_SCHEMA_BRANCH and also update the Dockerfile to the new branch.
* In the running container run ./upgrade.sh
* Set DB_SCHEMA_SEQUENCE to $NEW_SCHEMA_NUM in this repo musicbrainz-dockerfile/DBDefs.pm
* On the host machine stop and remove the musicbrainz container and image
* On the host machine make sure the postgresql container is running
* In musicbrainz-dockerfile run `sudo ./build.sh`
* In musicbrainz-dockerfile run `sudo ./run.sh`

That’s it!
