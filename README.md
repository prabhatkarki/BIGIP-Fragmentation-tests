# BIGIP-Fragmentation-tests
Test Environment. 
  1. Two Openshift Clusters
  2. Each cluster has client and server pods (netcat/linux) 
  3. Client from cluster1 send traffic to cluster 2. On cluster 1 traffic from client egresses via BIGIP/SPK and on cluster 2 ingresses via BIGP/SPK to server pod.
  
  BIGIP/SPK configuration.
  1. The VLAN MTU on BGIP/SPK are changed between 8000 to 4330 between test scenarios.
  2. Both egress (forwarding/wildcard)  and ingress VS (perf L4) are using fastl4 profile. The only change is "Reassemble IP Fragments" is enabled from the default settings. Unless this is enabled, tmm drops all incoming fragmented packets when using fastl4.
  
  
  TEST 1
  The VLAN MTU is at default 8000 on both ingress egress 
