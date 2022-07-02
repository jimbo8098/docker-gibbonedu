The Dockerfiles in php and webserver create minimal images with apache, configured for php-fpm, and php-fpm to enable gibbon to run.

If you migrate your database put your databasedump in ./db/initdb/ before the first time you run docker-compose up -d. Adjustments to the database, like migration to another URL have to be done by you either in advance in the dump or via SQL using the mysql client.

Some of the work here is derived [from the excellent work of Paul Lebmann](https://github.com/PaulLebmann/docker-gibbonedu). The view here is to somewhat cut the fat of his work to make it less opinionated. Through simplicity, I hope it is easier to work with and customize for your needs.

# Setup
Depending on your school's size, the setup might vary somewhat.

## General First Time Setup
Regardless of which way you proceed, the installer will do a few things and leave some of the rest to you. The general flow is as follows:

1. Bring the containers up using one of the methods in the two setup sections below.
2. Connect to the site through your browser.
3. The installer page should show. Follow the on screen instructions.
4. On the database step, make sure to use the hostname of the `db` container or your service endpoint depending on your deployment method. This should ensure that traffic is correctly routed.
5. Follow the post-install setps noted at https://docs.gibbonedu.org/administrators/getting-started/installing-gibbon/#post-install-server-config by execing into the container.

## Testing/Small Setup
Just run `docker-compose up`. A local version of Gibbon will be brought up based on the version specified within the `.env` file. You can consider using `docker swarm` for a kube-like install.

## Larger setup
For a larger environment, you can expand the Apache and PHP containers. These use a background layer to connect the different containers to each other. Only the Apache container has exposed ports. This could more easily be orchestrated using Kubernetes.

### Other considerations
- Backups: While no backups are taken in the original configuration, the data is written to a docker volume. Therefore you can take backups of that volume on your host. Your best bet is to connect to create a cron which connects to the `db` host and takes the backup manually.
- Scalability: Note the "Larger Setup" section above which goes through considerations for a large scale setup. Performance so far has been tested on a Lenovo T450 so is hardly production scale!

## Backups
The section below references the docker-compose configuration but serve to give a good idea of how the backups could be taken.

## SQL
Data is stored in two docker volumes:

- `gibbon-db-data`
- `gibbon-db-log`

It's advisable to take the backups through a different container. This eases the update process for the individual containers by easing deviation from the original containers.

## Site/User Data
For the site content, which includes the user uploads, this is located in the volume named `gibbon-root` in the docker-compose file. By backing this volume up, you can take a backup of the data present at the time. This also allows for easier data swapping should you wish to wipe and reinstall Gibbon.