# Clustering

Clustering in tigase is quite straight forward. When configured correctly the nodes in the cluster will automatically find each other and join the cluster. If a node have failed or is taken down for maintainance, all the other nodes will be notified and i will continue its operations normaally. All of the clients that were connected to the node currently down, have to do re-connect again, and connect to a load balancer that will distribute it to a node that is up. All presence and messages will be as in normal single-node (non-cluster) operation and work out of the box. The proper way of doing re-connect for clients can be found in the core RFC (62... , chapter xx)


Clustering and load balancing can be achieved in various ways. The most common way is to have a load balancer (either software or hardware) in front that will balance between the nodes in the cluster. Another way is to use DNS SERV to make the balancing between the nodes. 

## Prerequisites

* A load balancer (either software or hardware, or DNS) to balance between the nodes
* The nodes should have a FQDN resolvable names for each node in the cluster
* Port 5777 should be open between the nodes in the cluster. This is used for notifying and send stanzas beweeen the nodes.
* Each node must share the same database. In that case the port for communicating with the database should be open as well.


## Configuration

Changing a single tigase instance to be used in a clustered environment is simple. In the property file (in {TIGASE_HOME}/etc/init.properties) add this single line:

`--cluster-mode=true`

Each node must also have the same database connection strings, e.g. be using MySQL:

`--user-db=mysql`

And share the same URI for connecting to the specified database:

`--user-db-uri=jdbc:mysql://db.myserver.example:3306/tigase?user=xxx&password=xxx`

if the above prerequisites is fullfilled your nodes should now be in a cluster. An easy check would be to have two clients, each connecting to a different node in the cluster. If the two nodes can see each other (by presence) and can send stanzas to each other, you now have a clustered environment setup.
### Strategies and Scaling

The Tigase server have two options for clustering strategy:

* Default Strategy
* OnlineUserCaching Strategy

The **default strategy** is simplified and sends every stanza to every one of the connected nodes in the cluster. It does not have a knowledge of where the client situates in the cluster. This of course, will generate a lot of network trafic and this strategy is only suitable for around 3-4 connected nodes. Also a highspeed interconnectivity is recommended for scaling to that number of nodes. This strategy is default and does not have to be specified in the property file. Its also included in the source code for the server and is there by open source and within the GPL3 license.

OnlineUserCaching strategy is 
cost licens.. default is open and free. only relevant for 3 nodes and above. default send every request to every node (generates al lot of traffic)

`--sm-cluster-strategy-class=tigase.cluster.strategy.OnlineUsersCachingStrategy`

Besides h

* Your own strategy
by implementing the *ClusteringStrategyIfc* interface.


## Database

## Clustering Impact

### Native TCP client conenctions

### BOSH connections

Since BOSH connections are base on normal HTTP (request / response model), the client is not ensured that it will get the correct... 

So **sticky sessions** should be used on the load balancer to ensure that the client will get the correct answer back from the correct node in the cluster.

### Websocket connections




### Plugins Components

if you have some of your own components or plugins please verify that this will work in a clustered environment. It is not by default easy to say that it will, so it takes a lot of considerations for each specified plugin and component.

E.g. settnig up pubsub for a clustered component you have to put this in your property file:

`--comp-class-2 = pubsub`

`--comp-class-2 = tigase.pubsub.cluster.PubSubComponentClustered`

The same with MUC and other types of *local* code (jar files on the nodes) where clients indirectly connects. These are normally identified by having a component name that is reachable by a subdomain on your setup domain for your XMPP setup.

So for the given publish subscribe component, this subdomain will be pubsub.yourdomain.tld. 

### Ad-hoc scripts

Not all ad-hoc scripts will work in a clustered environment. This is because each script is situated on each node, and if the given script is not forwarding it to the next node in the cluster -it will simply not be sent to a given client that is situated on that other node.

## Debug and Testing

To enable debug for clustering on the the server, each node should add this to its property file:

`--debug=cluster,server`

If your server is not connecting to the cluster, if might also be advisable to have a look in the *cluster_nodes* table in the database. See if both nodes are present with the correct hostname. Also check the *last_updated* coloumn to see if its still in a connected state. 

Hostname like localhost, 127.0.0.1 etc. can of course NOT be used. So check this table to see if it looks correctly setup.
To check your database server, log in to it and query it for the hosts:

`use tigase;` (or what ever name you called your database schema)

`select * from cluster_nodes;`


## Monitoring and Metering

Like debug and testing, monitoring and metering is a **VERY** important subject when it comes to large scale operations. It is here where you see if your setup scales and if everything is working correctly.

## Exposing your logs

normally logging is done locally. but if you have a large scale setup with multiple nodes in your cluster, this is not the way to handle logging.

* Syslog server
* ELK and JSON logging

For JSON logging you have to add a new log apander to each tigase server. This will convert all of your normal log into JSON files that can be used for further processing in the ELK stack (see chapter below).


### The ELK (Elastic search / Logstash / Kibana)stack

logstash will gather the JSON logs


## Links

* [Tigase Docs](http://docs.tigase.net)
* [Elastic Search Docs](http://docs.tigase.net)