# OpenShift Galera MariaDB Cartridge

This should actually be very easy, assuming the node has access to the correct packages and my assumptions are correct. In theory the process should work in the case that we first:

- Start the first instance with the command:
	mysql_context "/usr/bin/mysqld_safe --defaults-file=$OPENSHIFT_MARIADB_DIR/conf/my.cnf" --wsrep-cluster-address="gcomm://" > /dev/null 2>&1 & 
- That's it! Now an infinte number of other instances can join the cluster as long as the my.cnf file is pointing to the first instance.

**Status:** Single gear is working - trying to work out how to prevent the race though.

**What's Different:**
- MariaDB Galera cluster requires 2 extra ports (galera - 4567 and sst (state snapshot transfer) 4444) these are handled by hooks to save them as env variables to be shared across the cluster.
- There are three ENV variables I've used to record a list of active servers. It is essentially a bunch of dirty bash scripts to that will manipulate and store the ENV variables from the publish and subscribe services of openshift cartridge.

- Modification to the start command, it first will do a quick check to find all the hosts which are online and dynamically update the wsrep_cluster_addresses on boot

**To Do:**
- Move binaries into versions to follow other cartridge hiearchy
- Use the ssh private to do inter gear routing tests. Lets avoid relying on telnet!
- Make sure the ENV variables are properly sharing the variables
- Verify logic and possible loops or data corruption
- Test!
