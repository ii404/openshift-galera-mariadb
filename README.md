# OpenShift Galera MariaDB Cartridge

This is an OpenShift cartridge which will give you the end result of a 3 gear MariaDB Galera cluster, it can technically scale up to as many gears as possible (within reason). It requires this HAProxy cartridge [1]

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

I'm not a developer, but I'm learning so I appreciate any feedback and any code comments you have. If you see something absolutely horrible, please tell me so I can improve :)

**Disclaimer:**
- Normal disclaimer, I'm doing this for my own personal learning and gain, with the hopes that this will also help another. 
- Possibly become something which will be used in production environments. 
- However with the nature of database replication software, I'm not responssible for data loss or anything while using this cartridge. 
- Although I've tried very hard to prevent all possible common pitfalls.


[1] https://github.com/andrewklau/openshift-haproxy-galera-mariadb
