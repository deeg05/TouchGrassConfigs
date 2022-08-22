VyOS Site-to-site IPSec vpn plus some initial config like dhcp and dns
```
nat {
     source { # exclude site-to-site
         rule 10 {
             destination {
                 address 10.0.2.0/29
             }
             exclude
             outbound-interface eth0
             source {
                 address 10.0.1.0/29
             }
         }
         rule 100 { # PAT
             outbound-interface eth0
             source {
                 address 10.0.1.0/29
             }
             translation {
                 address masquerade
             }
         }
     }
 }

service {
     dhcp-server {
         shared-network-name left.baltimor.wsr {
             authoritative
             name-server 8.8.8.8
             subnet 10.0.1.0/29 {
                 default-router 10.0.1.1
                 domain-name left.baltimor.wsr
                 range CLIENTS {
                     start 10.0.1.3
                     stop 10.0.1.6
                 }
             }
         }
     }
     dns {
         forwarding {
             allow-from 10.0.1.0/29
             listen-address 10.0.1.1
             name-server 1.1.1.1
         }
     }
     ssh {
         port 22
     }
 }
vpn {
     ipsec {
         esp-group FYM {
             compression disable
             lifetime 28800
             mode tunnel
             pfs dh-group14
             proposal 1 {
                 encryption aes256gcm128
                 hash sha256
             }
             proposal 10 {
                 encryption aes256gcm128
                 hash sha256
             }
         }
         ike-group FYM {
             lifetime 3600
             proposal 1 {
                 dh-group 14
                 encryption aes256gcm128
                 hash sha256
             }
         }
         ike-group ikev2 {
             dead-peer-detection {
                 action restart
                 interval 30
                 timeout 120
	          }
             ikev2-reauth no
             key-exchange ikev2
             lifetime 28800
             mobike disable
             proposal 10 {
                 dh-group 14
                 encryption aes256gcm128
                 hash sha256
             }
         }
         interface eth0
         site-to-site {
             peer 2.2.2.2 {
                 authentication {
                     id 1.1.1.2
                     mode pre-shared-secret
                     pre-shared-secret 13371337
                     remote-id 2.2.2.2
                 }
                 connection-type initiate
                 ike-group ikev2
                 ikev2-reauth inherit
                 local-address 1.1.1.2
                 tunnel 0 {
                     esp-group FYM
                     local {
                         prefix 10.0.1.0/29
                     }
		     remote {
                         prefix 10.0.2.0/29
                     }
                 }
             }
         }
     }
 }
```
