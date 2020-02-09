# root@DeviceName load set terminal

If you are here, you should check your hardware and software versions of your SRX110H-VA and you are ready to configure your device for stable internet connectivity. 

1. Connect to your SRX110H-VA via terminal cable on the console port
2. Type **`cli`** for jump to the CLi mode
3. Now, type **`edit`** command for your configuration
4. **`set system root-authentication encrypted-password`** for set your root password of your SRX110H0-VA
5. Let's load the configuration with **`load set terminal`** command

```
set system host-name <string>
set system domain-name <string>

# If you don't know your time zone, you can find with "set system time-zone ?" command
set system time-zone Europe/Istanbul

# 1.1.1.1 and 1.0.0.1 nameservers are powered by CloudFlare, you can keep it.
set system name-server 1.1.1.1
set system name-server 1.0.0.1

# 8.8.8.8 an 8.8.4.4 nameservers are provided by Google, you can keep it.
set system name-server 8.8.8.8
set system name-server 8.8.4.4

# 208.67.222.222 and 208.67.220.220 nameservers are provied by OpenDNS a.k.a Cisco. You can keep it.
set system name-server 208.67.222.222
set system name-server 208.67.220.220

# If you don't know for the NTP server, you can check via [https://www.ntppool.org](https://www.ntppool.org)
set system ntp server 192.36.143.130

# In Turkey, ISPs are using vlan id 35 for connection. Maybe your vlan id is different than this value. Please ask your ISP first.
set interfaces pt-1/0/0 unit 0 vlan-id 35

set interfaces pp0 unit 0 ppp-options pap local-name <your-vdsl-username>
set interfaces pp0 unit 0 ppp-options pap local-password <your-vdsl-password>
set interfaces pp0 unit 0 pppoe-options service-name <ISPName>

set system services dhcp pool 192.168.1.0/24 address-range low 192.168.1.3
set system services dhcp pool 192.168.1.0/24 address-range high 192.168.1.254
set system services dhcp pool 192.168.1.0/24 router 192.168.1.1
set system services dhcp propagate-settings pt-1/0/0.0
set system syslog archive size 100k
set system syslog archive files 3
set system syslog user * any emergency
set system syslog file messages any critical
set system syslog file messages authorization info
set system syslog file interactive-commands interactive-commands error
set system max-configurations-on-flash 5
set system max-configuration-rollbacks 5
set system license autoupdate url https://ae1.juniper.net/junos/key_retrieval
set interfaces interface-range interfaces-trust member fe-0/0/1
set interfaces interface-range interfaces-trust unit 0 family ethernet-switching vlan members vlan-trust
set interfaces fe-0/0/0 unit 0 family inet dhcp
set interfaces fe-0/0/1 unit 0 family ethernet-switching vlan members vlan-trust
set interfaces fe-0/0/2 unit 0 family ethernet-switching vlan members vlan-trust
set interfaces fe-0/0/3 unit 0 family ethernet-switching vlan members vlan-trust
set interfaces fe-0/0/4 unit 0 family ethernet-switching vlan members vlan-trust
set interfaces fe-0/0/5 unit 0 family ethernet-switching vlan members vlan-trust
set interfaces fe-0/0/6 unit 0 family ethernet-switching vlan members vlan-trust
set interfaces fe-0/0/7 unit 0 family inet
set interfaces pt-1/0/0 vlan-tagging
set interfaces pt-1/0/0 vdsl-options vdsl-profile auto
set interfaces pt-1/0/0 unit 0 encapsulation ppp-over-ether
set interfaces pp0 traceoptions flag all
set interfaces pp0 no-per-unit-scheduler
set interfaces pp0 unit 0 ppp-options pap passive
set interfaces pp0 unit 0 pppoe-options underlying-interface pt-1/0/0.0
set interfaces pp0 unit 0 pppoe-options auto-reconnect 5
set interfaces pp0 unit 0 pppoe-options client
set interfaces pp0 unit 0 family inet negotiate-address
set interfaces vlan unit 0 family inet address 192.168.1.1/24
set routing-options static route 0.0.0.0/0 next-hop pp0.0
set routing-options static route 0.0.0.0/0 metric 100
set protocols stp
set class-of-service host-outbound-traffic ieee-802.1 default 000
set security flow tcp-mss all-tcp mss 1452
set security screen ids-option untrust-screen icmp ping-death
set security screen ids-option untrust-screen ip source-route-option
set security screen ids-option untrust-screen ip tear-drop
set security screen ids-option untrust-screen tcp syn-flood alarm-threshold 1024
set security screen ids-option untrust-screen tcp syn-flood attack-threshold 200
set security screen ids-option untrust-screen tcp syn-flood source-threshold 1024
set security screen ids-option untrust-screen tcp syn-flood destination-threshold 2048
set security screen ids-option untrust-screen tcp syn-flood timeout 20
set security screen ids-option untrust-screen tcp land
set security nat source rule-set trust-to-untrust from zone trust
set security nat source rule-set trust-to-untrust to zone untrust
set security nat source rule-set trust-to-untrust rule source-nat-rule match source-address 0.0.0.0/0
set security nat source rule-set trust-to-untrust rule source-nat-rule match destination-address 0.0.0.0/0
set security nat source rule-set trust-to-untrust rule source-nat-rule then source-nat interface
set security policies from-zone trust to-zone untrust policy trust-to-untrust match source-address any
set security policies from-zone trust to-zone untrust policy trust-to-untrust match destination-address any
set security policies from-zone trust to-zone untrust policy trust-to-untrust match application any
set security policies from-zone trust to-zone untrust policy trust-to-untrust then permit
set security zones security-zone trust host-inbound-traffic system-services all
set security zones security-zone trust host-inbound-traffic protocols all
set security zones security-zone trust interfaces vlan.0 host-inbound-traffic system-services all
set security zones security-zone trust interfaces vlan.0 host-inbound-traffic protocols all
set security zones security-zone untrust screen untrust-screen
set security zones security-zone untrust interfaces fe-0/0/0.0 host-inbound-traffic system-services dhcp
set security zones security-zone untrust interfaces fe-0/0/0.0 host-inbound-traffic system-services tftp
set security zones security-zone untrust interfaces pt-1/0/0.0 host-inbound-traffic system-services dhcp
set security zones security-zone untrust interfaces pt-1/0/0.0 host-inbound-traffic system-services tftp
set security zones security-zone untrust interfaces pp0.0
set vlans vlan-trust vlan-id 3
set vlans vlan-trust l3-interface vlan.0
```

If your configuration is ready to connect to the internet, now you can able to ping google.com.  In CLi mode, now you can run **`ping inet google.com`** if your ISP providing an IPv4 otherwise you are using IPv6 and just run **`ping inet6 google.com`**  for test the connectivity. Your ping command should come back with results like below:

```
root@DeviceName> ping inet google.com
PING google.com (172.217.169.142): 56 data bytes
64 bytes from 172.217.169.142: icmp_seq=0 ttl=53 time=62.073 ms
64 bytes from 172.217.169.142: icmp_seq=1 ttl=53 time=61.792 ms
64 bytes from 172.217.169.142: icmp_seq=2 ttl=53 time=61.618 ms
64 bytes from 172.217.169.142: icmp_seq=3 ttl=53 time=62.155 ms
^C
--- google.com ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max/stddev = 61.618/61.910/62.155/0.216 ms
```

You can type CTRL+C (if you are on Windows or Linux), type control + C /  ^C (if your are on MacOS). This key combination is sending a "stop" signal for ping. 

Now your Juniper SRX110H-VA is successfully configured. Your local network subnet is 192.168.1.1/24.
