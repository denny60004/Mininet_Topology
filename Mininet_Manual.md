# Mininet Manual
## Environment
Linux 16.04.3 LTS
Python 2.7.12
Mininet 2.3.0d1

## Generating Topology
We have another project stimulating the topology in real world and getting its routing path. We save these data in csv/txt file since Markov clustering is based on Python 3, but Mininet only support Python 2. The output format is written in another manual.
Methods of reading these data are written in our code with comment, but you can write your own.

## Mininet Coding
### Basic Command
First
``` python
from mininet.topo import Topo
from mininet.net import Mininet
from mininet.node import Node
from mininet.log import setLogLevel, info
from mininet.cli import CLI
```
Add node (Routers in Mininet)
``` python
router_n= self.addNode(node_name, ip = 'XXX.XXX.XXX.XXX/XX', defaultRoute = "via XXX.XXX.XXX.XXX")
```
Add host
``` python
host_n = self.addHost(host_name, ip = "XXX.XXX.XXX.XXX", defaultRoute = "via " + "XXX.XXX.XXX.XXX")
```
Add Link
```python
self.addLink(hosts_n, router_n, intfName1 = "Anything unique in str", intfName2 = "Anything unique in str", params1 = {'ip' : ("XXX.XXX.XXX.XXX/XX")}, params2 = {'ip' : ("XXX.XXX.XXX.XXX/XX")})
```
Print Routing Table
``` python
print net[node_name].cmd('route')
print net[host_name].cmd('route')
```
Adding element in routing table
``` python
net[router_name].cmd("ip route add XXX.XXX.XXX.XXX/XX via XXX.XXX.XXX.XXX"))
```


Start the Mininet program
``` python
#Put this in the end of the code
CLI( net )
net.stop()
```


## topology.ipynb - Router implementation
### Interface
- #### links.txt
links.txt save links between nodes in 
```
(a,b)
(c,d)
```
format, where a and b are node numbers
links_store is the string tuple (a,b).
- #### nodes.txt
Node numbers of an AS are store in nodes.txt as
```
a
b
c
```
nodes_store is the list of node numbers in string
ex:[0, 3, 9, 26, 64]
- #### ips.txt
IP of nodes are store in ips.txt as
```
'168.0.a+1.254'
'168.0.b+1.254'
```
a,b are the node_numbers mentioned above, and in our code .254 represent router ip.
ips_store is the list of node numbers in string
ex: ['168.0.1.254', '168.0.4.254', '168.0.10.254', '168.0.27.254', '168.0.65.254']
- #### IntraTable_next_hop.txt
The node number of  next hop from routing path i to j is written in IntraTable_next_hop.txt
```
3
4
7
```
where 3 is the next hop of the first node to the second node, 4 is next hop of the first node to the third node, etc.

### ipython notebook description

### Routing table
1. We use 'IntraTable_next_hop.txt' to specify the gateway of each destination subnetwork and write it into routeing table.
2. We link routers using '10.0.K.nodeID' ip.  
    * Assume the IDs of two routers are A and B.
    * K is calculated by the following method.
        * W = Max(A,B)
        * X = Min(A,B)
        * K = (((W mod 254)^X) mod 254) + 1
    * The gateway of A is '10.0.K.B'
    * The gateway of B is '10.0.K.A' 
4. We add '168.AS_Number.nodeID' by `net[].cmd()`

### Some experience
1. Routers have many ip interfaces. When adding links between routers, the ip that we use to generate the router should be add links first.
2. Remember to run `sudo mn -c` to clean up the network whenever you rerun the notebook.



## topology.py - Switch implementation

### script description:
1. The input of this script is 'connection_XXX.csv', a .csv file defined by Henry Lai。
2. Change input csv file to change the topology
3. The definition of the input .csv file :
    ```
    # Created at 2018-03-19 20:49:20
    # Connection parameters. Layer11:750 Layer12: 70 Layer22: 2500
    # Connection Num. Layer11: 20 Layer12: 53 Layer22: 349
    # Lowest level starting ID, total switch number, bandwidth
    21,106,100
    # NodeID, x_pos, y_pos, degree
    0,12129,3110,7
    1,7249,1856,8
    .
    .
    .
    # links
    0,1
    0,2
    .
    .
    .
    ```
### How to use this script
Run the following command
```
> sudo python2 topology.py
```

### Some experience
1. Switches can't form loops. 
2. Remember to run `sudo mn -c` to clean up the network whenever you rerun the script.
