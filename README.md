# OpenShift Galera MariaDB Cartridge

This should actually be very easy, assuming the node has access to the correct packages and my assumptions are correct. In theory the process should work in the case that we first:

- Start the first instance with the command:
	mysql_context "/usr/bin/mysqld_safe --defaults-file=$OPENSHIFT_MARIADB_DIR/conf/my.cnf" --wsrep-cluster-address="gcomm://" > /dev/null 2>&1 & 
- That's it! Now an infinte number of other instances can join the cluster as long as the my.cnf file is pointing to the first instance.

Status: Un-Tested but should work..

What's Different:
- MariaDB Galera cluster requires 2 extra ports (galera - 4567 and sst (state snapshot transfer) 4444) these are handled by hooks to save them as env variables to be shared across the cluster.
- There are three ENV variables I've used to record a list of active servers. It is essentially a bunch of dirty bash scripts to that will manipulate and store the ENV variables from the publish and subscribe services of openshift cartridge.

- Updated post_install to prepare the wsrep_cluster
- Modification to the start command, it first will do a quick check to find all the hosts which are online and dynamically update the wsrep_cluster_addresses on boot

Issues:
- The cluster needs some sort of LVS or proxy to allow external connections and also take advantage of the multiple node cluster.
- I'm not sure how to change the SST port within galera, so that's likely going to fail because of the openshift inter-tcp routing.. Need to do a little more research..
- MariaDB Galera needs atleast 3 hosts to prevent split brains, however I'm not sure how a scaling minimum of 3 will start up. If they all startup at the same time then everything will fall apart. Need to do a little more research..
