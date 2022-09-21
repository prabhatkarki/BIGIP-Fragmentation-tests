# BIGIP-Fragmentation-tests
Test Environment. 
  Two Openshift Clusters
  Each cluster has client and server pods (netcat/linux) 
  Client from cluster1 send traffic to cluster 2. On cluster 1 traffic from client egresses via BIGIP/SPK and on cluster 2 ingresses via BIGP/SPK to server pod.
  
