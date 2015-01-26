# Clustering

Clustering in tigase quite straight forward. When configured correctly the nodes in the cluster will automatically find eachother and join the cluster. When a node is failed or is taken down for maintainance, all the other nodes will be notified.

Clustering and loadbalancing can be achieved in various ways. The most common way is to have a loadbalancer (either software or hardware) in front that will balance between the nodes in the cluster. Another way is to use DNS SERV to make the balancing.

## Prerequisites

* A FQDN resolvable names for each node in the cluster
* Port 5777 should be open between the nodes. This is used for notifying and send messages beweeen the nodes.
* Each node must share the same database. In that case the port for communicating with the database should be open as well.


## Configuration

Changing a single tigase instance to be used in a clustered environment is simple. In the init.property file add this single line:

`--cluster-mode=true`

Each node must also have the same database connection strings, e.g.:

`--user-db=mysql
--user-db-uri=jdbc:mysql://db.myserver.example:3306/tigase?user=root&password=xxx`


### Strategy

The Tigase server 

* Default Strategy
* OnlineUserCaching Strategy
* Your own strategy


cost licens.. default is open and free. only relevant for 3 nodes and above. default send every request to every node (generates al lot of traffic)

`--sm-cluster-strategy-class=tigase.cluster.strategy.OnlineUsersCachingStrategy`


## Database

## Clustering Impact

### Native TCP client conenctions

### BOSH connections

Since BOSH connections are base on normal HTTP (request / response model), the client is not ensured that it will get the correct... 

So **sticky sessions** should be used on the loadbalancer to ensure that the client will get the correct answer back from the correct node in the cluster.

### Websocket connections


### Plugins

`--comp-class-2 = tigase.pubsub.cluster.PubSubComponentClustered`

### Components

### Ad-hoc scripts

## Debug and Testing


`--debug=cluster,server`


Have a look in the cluster_nodes table in the database. See if both nodes are present with the correct hostname. localhost etc. can NOT be used.

Log in to your tigase database server and query it for the hosts:

`use tigase;`  or what ever name you called your database schema

`select * from cluster_nodes`