# OpenShift Galera MariaDB Cartridge

This should actually be very easy, assuming the node has access to the correct packages and my assumptions are correct. In theory the process should work in the case that we first:

- Start the first instance with the command:
	mysql_context "/usr/bin/mysqld_safe --defaults-file=$OPENSHIFT_MARIADB_DIR/conf/my.cnf" --wsrep-cluster-address="gcomm://" > /dev/null 2>&1 & 
- That's it! Now an infinte number of other instances can join the cluster as long as the my.cnf file is pointing to the first instance.

Status: On hold until I find a way to properly start the cartridge service without ending up with a split-brain scenario. 

Needs:
- Method to loadbalance the connections (LVS, maybe haproxy?) or just have the app control the load balancing however that would have issues with fault tolerance..
- As above, a proper way to start an initial instance. The concern would be if one were to crash openshift doesn't allow a proper way to do a manual intervention. It would require some sort of checker to see if any instance is available before trying to join the cluster.
