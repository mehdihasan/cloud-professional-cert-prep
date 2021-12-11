# Google Cloud And Hybrid Network Architecture

Design decisions should be made on the followinf criteria:
1. location
2. number of users
3. scalability
4. fault tolerance


## Offers
- VPC Interconnect: connecting subnets into a single virtual provate network.
- VPC Peering: to connect two different projects from two different organization running inside GCP.
- Cloud VPN: to connect on-premises or another cloud. Using IPSec VPN tunnel. Low volume data connection - 3 Gb/s.  Can configure *static routes* or *dynamoc routes* using BGP (Border Gateway Protocol). 
    - Cloud HA VPN: SLA 99.9% for HA VPN. Creates 2 IP addresses. 
- Cloud Interconnect
    - Dedicated Interconnect: dedicated high speed connection to a colocation facility. A dedicated connect bundle can have (8 x 10 Gb/s) or (2 x 100 Gb/s) connections.
    - Partner Interconnect: through a service provider. Starts from 50 Mb/s.

## Observations
- In all the cases they are using the BGP (Border Gateway Protocol). So, is it the base technology?
- require a good understanding on CIRD block.