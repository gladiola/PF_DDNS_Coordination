# PF_DDNS_Coordination
Scripts to coordinate the use of PF firewall with dynamic DNS clients on FreeBSD to permit entrance based on IP.

# ddclient and pf
On a host that runs pf and ddclient, we can allow logins from other hosts that can be identified from their use of ddclient.  Suppose we install ddclient on ALICE and BOB.  Every day, ALICE and BOB update their IPs with a coordinating DNS server.  One day, host BOB receives an ssh login request from a user on host ALICE.  BOB has a table in pf that specifies who might be allowed in quickly.  In order for host ALICE's IP to be on that table, BOB's use of ddclient will need to refresh the contents of the table.  

On host BOB, we might find a rule in pf like:

```
## Table to hold those who should pass in quick
table <pfIngress> persist file "/usr/local/etc/pf/location/pfIngress.txt"
```
But, host BOB has been running for a while.  So, to get host ALICE's fresh IP on there, and to displace her past IP addresses, we need to refresh the pf table that's loaded by that file.

We need a script with a line like:
```
pfctl -t pfIngress -T replace -f /usr/local/etc/pf/location/pfIngress.txt
```
This is covered in reload_pfIngress.txt.
