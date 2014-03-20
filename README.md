# OpenShift Galera MariaDB Cartridge

This is a WIP OpenShift cartridge which will give you the end result of a 3 gear MariaDB Galera cluster!

**Quick Steps:**

```
rhc -s app create mariadb php-5.3 # or any other web cartridge (it must be a scaled cartridge)
rhc cartridge add https://raw.githubusercontent.com/andrewklau/openshift-haproxy-galera-mariadb/master/metadata/manifest.yml -a mariadb
rhc cartridge add https://raw.github.com/andrewklau/openshift-galera-mariadb/master/metadata/manifest.yml -a mariadb
rhc app show mariadb --gears
```

Now you can ssh into your gears and you'll see your lovely lovely mariadb cluster connected and waiting to be thrashed with load.

**Status:** Working!

**What's Different:**
- MariaDB Galera cluster requires 1 extra ports (galera - 4567) this is handled by hooks to save them as env variables to be shared across the cluster. Galera can also use rsync for it's SST (state snapshot transfer) on port 4444 however mysqldump has proven to be a little more reliable in OpenShift
- Modified wsrep_sst_mysqldump

**To Do:**
- Verify logic and possible loops or data corruption (I'd really appreciate a second pair of eyes)
- Do the upgrade/snapshot/backup scripts
- Improve HAProxy config files

**Ideally:**
- Setup proper version control so for nodes with the correct packages installed can get automatically updates (need more research)
- Create another embeded haproxy cartridge for loadbalancing and split-brain prevention(done!!)
- Setup SSL for the mysqldump (need more research)
- Add a wsrep_notify_cmd script to notify users if gears go down etc.

**Feedback:**
I appreciate any feedback and any code comments you have. If you see something absolutely horrible, please tell me so I can improve :)
