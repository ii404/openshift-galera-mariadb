# OpenShift Galera MariaDB Cartridge

This is a WIP MariaDB Galera cartridge for OpenShift

**Status:** ITS WORKING!!!!

**What's Different:**
- MariaDB Galera cluster requires 2 extra ports (galera - 4567 and sst (state snapshot transfer) 4444) these are handled by hooks to save them as env variables to be shared across the cluster.
- There are three ENV variables I've used to record a list of active servers. It is essentially a bunch of bash scripts to that will manipulate and store the ENV variables from the publish and subscribe services of openshift cartridge.

- Modification to the start command, it first will do a quick check to find all the hosts which are online and dynamically update the wsrep_cluster_addresses on boot
- Modified wsrep_sst_mysqldump

**To Do:**
- Verify logic to prevent racing on cluster resume
- Move binaries into versions to follow other cartridge hiearchy
- Make sure the ENV variables are properly sharing the variables
- Verify logic and possible loops or data corruption
- Do the upgrade/snapshot/backup scripts
- Test!

- Setup proper version control so for nodes with the correct packages installed can get automatically updated
- Create another embeded haproxy cartridge for loadbalancing
