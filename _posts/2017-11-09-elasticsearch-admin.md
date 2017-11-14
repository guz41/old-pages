## Operating System and Elastic Search Settings

Elasticsearch settings are configured in elasticsearch.yml

### Environment variables
Environment variables can be used to define any element using ${} notation
```javascript
cluster.name:	${CLUSTER_NAME} //name of cluster
```

### Cluster settings
```javascript
cluster.name:			//name of cluster
```

### Node settings
The name of the node will be auto generated if none is provided
```javascript
node.name:			//name of node
```

#### Sample node configuration
It is recommended for production environments that you have seperate nodes to balance the data. Sample configuration for nodes:

##### Default
```javascript
node.client:	true
node.data:	true
```

##### Client
```javascript
node.client:	true
node.data:	false
```

##### Master
```javascript
node.master:	true
node.data:	false
```

##### Data
```javascript
node.master:	false
node.data: 	true
```

### Path settings
Can be updated to support specific server configuration
```javascript
path.data:		//path to data
path.logs:		//path to logs
path.plugins:		//path to plugins
```

### Master nodes
There is a possibility of having a "split Brain" in elasticsearch, this is where the cluster ends up with 2 concurrent master nodes and creates multiple sets of data, this is prevented by maintaining a minimum number of master nodes alive and connected to choose which becomes the new master in the event of a failure 
```javascript
discovery.zen.minimum_master_nodes: (n/2)+1    //3 total nodes would requrie 2 minimum 
```

### cluster recovery
Upon a cluster reboot, if some shards start before others .The shards will start to rebalance automatically as they believe that there has been a failure in the others nodes
```javascript
gateway.recovery_after_nodes: n-1  	//how many nodes should be present before a recovery begins 
gateway.expected_nodes: n 			//how many nodes we are expecting total in a cluster 
gateway.recover_after_time: 3m 		//how long to wait to start a recovery if not all nodes are present 
```

### multicasting:
Elasticsearch will constantly poll the network looking for additional nodes to join the network, potentially useful, however should be disabled for production settings as it uses bandwith and could accidentally pick up a test node 
```javascript
discovery.zen.ping.unicast.hosts: "host1", "host2", etc  //hostname or ip
discovery.zen.ping.multicast.enabled: false //so as to not to look for additional nodes 
```

if there are data only nodes it is suggested to disable http in yml, suggested to keep http enabled on master for debugging and has to be enabled on client to serve reqeusts

http.enabled: false 
 
JVM
Not reccomended to touch the jvm tuneables 
JVM Heapsize = n/2 #half for shards(jvm) the rest for the lucene runtime. Suggeted for data nodes is 64 gb total as heapsizes over 32gb become inefficent

One way to configure the heap size is to export the environment variable ES_HEAP_SIZE= 32g 

Swap files should be disabled as it drastically reduces performance. If this cannot be done on the OS level it can be configured in elasticsearch.yml for the jvm to disallow
bootstrap.mlockall: true  

File descriptors should be increased, this configures how many files each process can access. suggested to start with 64,000

nmap setting should also be high

may be required to set network.host to the external ip, if working in vms


sql equivilents
index = db
type = table 
fields = column

Rolling indexes: logging 
template, can set a prefix or suffix of name to inherit shard properties
alias - can setup a prefix to query against 

clusterrouting.allocation.enable #can be toggled to stop the shards from reallocating on restarts
path.repo - can be used for backups -send a snapshot request to complete the backup - can use get request on ./status 

curator cli can be used to manage ES