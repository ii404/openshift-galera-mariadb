# How these hooks work

**DB-INIT** 

DB-INIT has three possible values:
- 0 - uninitialized aka brand new gear
- 1 - the passwords are sync'd throughout the cluster. Only the first 3 gears will enter this state
- 2 - new gear waiting for passwords OR gear that still is waiting to be initialized
- 3 - cluster is initialized

Dot Point Logic:
- **Publish:** Every new gear, start as db-init=0
- > Now, lets tell everyone what our current db-init is (either 0,1,2,3)
- **Subscribe:** For anyone listening, they'll only take note of the new value if they hear db-init>0
- > If criteria match, we'll only upgrade our db-init to 2
- > If we receive 2/3, but we are 0 we set ourself to 2, this means the rest of our cluster is already initialized but we still need to go through the steps.

**DB-LOGIN**

DB-LOGIN has two functions:
- Initialize the cluster's shared passwords
- Distribute the shared passwords to all new gears

Dot Point Logic:

- **Publish:** If we're uninitialized (ie. db-init=0) lets broadcast our random username and passwords we generated in the install script
- **Publish:** Otherwise, we're already initialized so let's share our password around the cluster
- **Publish:** Or, we're still waiting for passwords (ie. db-init=2) so we stay quiet

- **Subscribe:** If we're uninitialized, every time a gear broadcasts their login details we grab it and build the values into an array and save it.
- > Once we've hit got a minimum of 3 values, we do a dumb check to validate which has the lowest value. This check will end up with the same result accross all the gears. 
- > We save the user:pass with the lowest value and state DB-INIT=1

- **Subscribe:** However if we've got a state of db-init=2, this could mean we're still waiting for passwords
- > Any new user:pass we receive should be sanitized so let's accept them all

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

**Race Conditions Prevention**

- Control start script, will delay the start of the cluster unless we can see that other gears are online.
- In the case for all gears being resumed at the same time, we try to delay them and only start when they match their own address within the loop.

**Example Scenarios:**

- 3 gears started per manifest's min3. 
> All 3 start with db-init=0, once the third one as executed it's publish all three will see 3 passwords in their array list
- Extra 2 gears added to the cluster
> Both gears will receive db-init=2 as soon as they join, this will skip the init process and just sync the passwords.
