# OpenShift Galera MariaDB Cartridge

This is a WIP MariaDB Galera cartridge for OpenShift

**Status:** ITS WORKING!!!!

**What's Different:**
- MariaDB Galera cluster requires 1 extra ports (galera - 4567) this is handled by hooks to save them as env variables to be shared across the cluster.
>  Galera can also use rsync for it's SST (state snapshot transfer) on port 4444 however mysqldump has proven to be a little more reliable in OpenShift
- There are a bunch of ENV variables I've used to record a list of active servers. It is essentially a bunch of bash scripts to that will manipulate and store the ENV variables from the publish and subscribe services of openshift cartridge. Please see hooks/README.md

- Modification to the start command, it first will do a quick check to find all the hosts which are online and dynamically update the wsrep_cluster_addresses on boot
- Modified wsrep_sst_mysqldump

**To Do:**
- Verify logic to prevent racing on cluster resume (when a gear idles or gets moved, or worst case all the gears get powered off)
> if this does not get a proper fix, there is a rare case that the cluster with each gear being standalone. (ie. split brain)
> **Untested Solution** using a wait timer on the cluster ID and a loop (see control start script)
- Move binaries into versions to follow other cartridge hiearchy (done)
- Make sure the ENV variables are properly sharing the variables (90% sure this is verified, but haven't tested all case scenarios)
- Verify logic and possible loops or data corruption (I'd really appreciate a second pair of eyes)
- Do the upgrade/snapshot/backup scripts

**Ideally:**
- Setup proper version control so for nodes with the correct packages installed can get automatically updates (need more research)
- Create another embeded haproxy cartridge for loadbalancing (need more research)
- Setup SSL for the mysqldump (need more research)
- Add a wsrep_notify_cmd script

**Feedback:**

I appreciate any feedback and any code comments you have. If you see something absolutely horrible, please tell me so I can improve :)
