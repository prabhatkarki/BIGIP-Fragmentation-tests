# BIGIP-Fragmentation-tests
Test Environment. 
  1. Two Openshift Clusters
  2. Each cluster has client and server pods (netcat/linux) 
  3. Client from cluster1 send traffic to cluster 2. On cluster 1 traffic from client egresses via BIGIP/SPK and on cluster 2 ingresses via BIGP/SPK to server pod.
  
  BIGIP/SPK configuration.
  1. The VLAN MTU on BGIP/SPK are changed between 8000 to 4330 between test scenarios.
  2. Both egress (forwarding/wildcard)  and ingress VS (perf L4) are using fastl4 profile. The only change is "Reassemble IP Fragments" is enabled from the default settings. Unless this is enabled, tmm drops all incoming fragmented packets when using fastl4.
  
  
  TEST 1 (normal ipv6 and ipv4)
  1. The VLAN MTU is at default 8000 on both ingress egress.
  2. Client pod sends UDP packets 7960 and egress BIGIP/SPK forwards as is and on the cluster2 also packets are forwarded as is to server pod.
  
  TEST 2 Lower MTU ipv4
  1. VLAN MTU is set at 4330 on both egress and egress F5 nodes.
  2. Client sends UDP packet size 7960 with DF bit set at 1.
  3. Egress F5 nodes responds with ICMP Fragmentation needed response. 
  4. Client resends Original packet as fragmented into two 4k and 3k bytes. (no DF bit set as the packets are fragmented)
  5. F5 reassembles the fragmented packets and sends it out again fragmented 
  (Note: with fastl4 profile assigned to F5 VS, by default it drops any fragmented packets. So the "reassemble fragmented packet" feature is enabled in the fastl4 profile)
  6. Fragmented packet arrives in the F5 node in cluster2.
  7. Again the packets are reassmbled and fragmented and passed to the server pod.
  
  
  Test 3. Lower MTU ipv6 (NAT46)
  Because the tests are being performed in OCP 4.7, it is single stack so the client and server pods can only understand ipv4. So DNS/NAT46 feature is being used on F5 for this.
  1. Client needs to go to ipv6 dest. But since its ipv4 only
  1. Client sends UDP packet size 7960 to ipv4 dest via egress F5 node. The dest ipv4 is mapped to a ipv6 address in F5 table. Because as NAT46 functions, hands off the ipv4 to client and creates a mapping of that ipv4 to real ipv6 destination in its table
  2. F5 nodes responds with ICMP Fragmentation needed response. 
  4. Client resends Original packet as fragmented into two 4k and 3k bytes. (no DF bit set as the packets are fragmented)
  5. F5 reassembles the fragmented packets. However, the dest is ipv6. So F5 converts the dest IP to ipv6 and sends fragmented ipv6 instead.


Test with lower MTU setting on swtich/router in between the clsuters
  
  
