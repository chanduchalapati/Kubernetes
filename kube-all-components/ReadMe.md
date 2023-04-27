NodeSelectors:
We can label the nodes and schedule pods selectively based on these labels.
kubectl label node node01 <label-key>=<label-value>

Node Affinity:
Node affinity comes handy where we cannot schedule the pods on to the nodes on following conditions.
1. When we want to place a pod on Large or medium Node.
2. When we want to place a pod on a node that is not small.

Get pods from a specific node:
kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=<node>

Login to the container running in the pod:
  kubectl exec -it <pod-name> -c <container-name> -- bash
  kubectl logs podname -c container name -- command 

List the container name
  kubectl get pods POD_NAME_HERE -o jsonpath='{.spec.containers[*].name}'

Namespace switching:
kubectl config set-context --current --namespace=<insert-namespace-name-here>

Networking:
  These changes are gone when you restart the host. To avoid that we need to make changes in the following file
  /etc/network/interfaces

  ip link command is used to list and modify the interfaces in a host
  ip addr is the command used to see the ip addresses assigned to that interfaces.
  ip addr add command is used to add the ip addresses on the interfaces.

  ip addr add 192.168.2.0/24 dev eth0

  route

  ip route --> is used to view the routing table.
  ip route add command is used to add entries to the routing table.
  ip route add <destination-network-interface-ip> via <routeTable-gateway-IP>
  ip route add 192.168.2.0/24 via 192.168.1.1

  arp
  netstat -plnt


  We can set the rule from 0 to 1 so that the packets are sent back to pinged host but these changes
  are lost when we restart the system.
  cat /proc/sys/net/ipv4/ip_forward

  To make permenant changes we need to modify the following file.

  /etc/sysctl.conf ---> net.ipv4.ip_forward = 1


  /etc/nsswitch.conf

  Network Namespaces:
  Create a namespace ----> ip netns add <nsname>
  ip netns ---> Lists all the netns
  ip link ---> To list the network interfaces
  ip netns exec <nsname> ip link or ip -n <ns-name> link ---> Lists the network interfaces inside the name space

  How to establish connectivity between the namespaces? We need to create the virtual cable by following command:
  ip link add veth-red type veth peer name <ns-name2>
  Eg: ip link add <ns-name1> type veth peer name veth-blue
  Now attach the interfaces to the name spaces using the following commands
  ip link set veth-red netns red
  ip link set veth-blue netns blue
  Now assign an IP to each name space with the following commands
  ip -n red addr add 10.10.10.1 dev veth-red
  ip -n blue addr add 10.10.10.2 dev veth-blue
  Now we need to bring up the interface using the following command
  ip -n red link set veth-red up
  ip -n blue link set veth-blue up
Now that the connection has been established we can ping each name spaces.

Now if we have more namspaces we need a switch in our case a virtual switch(network interface) to establish connectivity between the namespaces. So lets create a virtual switch(network interface) as follows:
ip link add v-net-0 type bridge
If the interface is down we need to bring it up using the following command:
ip link set dev v-net-0 up
Now lets connect  the name spaces to the interface on the host
