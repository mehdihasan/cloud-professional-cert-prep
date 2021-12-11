# Interconnecting Networks


```
--------------------------------------------------------------------------------
                        |      Dedicated        |           Shared
--------------------------------------------------------------------------------
                        |                       |
     Layer 3            |   Direct Peering      |           Carrier Peering
                        |                   [Cloud VPN 1.5-3 Gbps per tunnel]  
                        | [10 Gbps Per Link]    |
--------------------------------------------------------------------------------
                        |                       |
     Layer 2            |  Dedicated            |      Partner 
                        |       Interconnect    |           Interconnect
                        | [10 Gbps per link]    |   [50 Mbps - 10 Gbps per connection]
                        | [100 Gbps BETA]       |
--------------------------------------------------------------------------------
```


```
-----------------------------------------------------
      Interconnect          |      Peering          |
-----------------------------------------------------
                            |                       |
  Direct access to RFC      |  Access to Google     |
   1918 IPs in your VPC     |   public IPs only     |
       with SLA             |     without SLA       |
-----------------------------------------------------
  1. Dedicated Interconnect | 1. Direct Peering     |
  2. Partner Interconnect   | 2. Carrier Peerin     |
  3. Cloud VPN              |                       |
                            |                       |
-----------------------------------------------------
```


