# How these hooks work

**DB-INIT** 
DB-INIT has three possible values:
0 - uninitialized aka brand new gear
1 - the passwords are sync'd throughout the cluster. It should really only sit in this state until the 3rd gear comes up
2 - cluster is initialized

Dot Point Logic:
- **Publish:** Every new gear, start as db-init=0
- > Now, lets tell everyone we're a noob (aka db-init=0)
- **Subscribe:** For anyone listening, they'll only take note of the new value if they hear db-init>0
- > If criteria match, we'll only upgrade our db-init to 1. 
- > If we receive 2, but we are 0 we set ourself to 1, this means the rest of our cluster is already initialized but we still need to go through the steps.

**DB-LOGIN**
DB-LOGIN has two functions:
- Initialize the cluster's shared passwords
- Distribute the shared passwords to all new gears

Dot Point Logic:
- **Publish:** If we're uninitialized (ie. db-init=0) lets broadcast our random username and passwords we generated in the install script
- **Publish:** Otherwise, we're already initialized so let's share our password around the cluster

- **Subscribe:** If we're uninitialized, every time a gear broadcasts their login details we grab it and build the values into an array and save it.
- > Once we've hit got a minimum of 3 values, we do a dumb check to validate which has the lowest value. This check will end up with the same result accross all the gears. 
- > We save the user:pass with the lowest value and state DB-INIT=1

- **Subscribe:** However if we've got a state of initialized, this could mean we're either genuinely initialized OR just faking it
- > Any new user:pass we receive should be sanitized so let's accept them.

**MARIADB-NODE**
MARIADB-NODE has two functions:
- Keep a record of every new gear which joins the cluster
- Initialize the cluster if criteria are met

Dot Point Logic:
- **Publish:** When we're new, lets publish all our access points. (ie. PUBLIC-IP:PUBLIC-MYSQL-PORT:PUBLIC-GALERA-PORT;PUBLIC-SST-PORT)

- **Subscribe:** For every broadcast, we'll accept that into our array of gears.

- **Subscribe:** if db-init is 1, meaning our passwords are all sync'd and our cluster has 3 gears. Lets start the initilization
- > Same as the DB-LOGIN hook, we'll determine which is the lowest value based on (gear-dns)
- > All gears will then run through and setup initial database and logind etails from DB-LOGIN
- > Whichever gear has the lowest value will get a head start to start the cluster
- > The other two gears will wait for the first one to complete, before joining the cluster
