# Cisco
crypto isakmp policy 1
	encryption aes 256
	hash sha256
	authentication pre-share
	group 15
crypto isakmp key 13371337 address 4.4.4.100
crypto ipsec transform-set tun0 esp-aes 256 esp-sha256-hmac
	mode transport
crypto ipsec profile protect-gre
	set security-association lifetime seconds 86400
	set transform-set tun0
crypto map OUT local-address Gig1
crypto map OUT 10 ipsec-isakmp
	set peer 4.4.4.100
	set transform-set tun0
	match address 155

interface Tunnel0
	ip address 10.10.10.2 255.255.255.252
	tunnel source 5.5.5.100
	tunnel destination 4.4.4.100
	tunnel protection ipsec profile protect-gre
interface Gig1
	crypto map OUT
ip access-list extended FW
	10 permit ahp any any
	20 permit esp any any
ip access-list extended 155
	10 permit gre any any
	20 permit gre host 5.5.5.100 host 4.4.4.100

# StrongSwan
conn eto-base
	fragmentation=yes
	dpdaction=restart
	ike=aes256-sha256-modp3072
	esp=aes256-sha256-modp3072
	keyexchange=ikev1
	type=transport
	keyingtries=%forever
conn tun0
	also=eto-base
	left=4.4.4.100
	leftauth=psk
	right=5.5.5.100
	rightauth=psk
	auto=start
