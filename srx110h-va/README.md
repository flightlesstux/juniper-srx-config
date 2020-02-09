# SRX110H-VA VDSL/VDSL2 Config

I'm using this config in Turkey with [turk.net](https://turk.net) ISP. If you are using another ISP in Turkey with VDSL technology, you can also use it for your device. This configuration is tested and working well on my device and I will explain how you can configure your device and set the status to **up and running**.

# Introduction

 You have to know the basic things about TCP/IP protocol or maybe have some SSH experience before.

Before start, you should have a terminal cable. If you don't have any, you can buy from [amazon.com](https://amazon.com). I'm using UGREEN USB Console Cable USB 2.0 to RJ45 with RS232 FTDI. You can [click here](https://www.amazon.com/gp/product/B07MK22FTY/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1) to buy.

You can able to connect to your SRX110H-VA with physically via network cable (cat5 or cat6). It means, your desktop or laptop should have an ethernet port connectivity. If you don't have an ethernet port, you can also buy UGREEN USB Ethernet Adapter Aluminum USB 3.0 to Network Gigabit RJ45 LAN 10/100/1000 Mbps Adapter Converter from [here](https://www.amazon.com/gp/product/B07MK6DJ6M/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1). I'm also using it.

**Step 1:  Check your hardware information**

For check hardware in the CLi mode with **`root@DeviceName> show chassis hardware`** command. It should like below:
```
root@DeviceName> show chassis hardware
Hardware inventory:
Item             Version  Part number  Serial number     Description
Chassis                                XXXXXXXXXXXX      SRX110H-VA
Routing Engine   REV 01   650-035910   XXXXXXXXXXXX      RE-SRX110H-VA
FPC 0                                                    FPC
  PIC 0                                                  8x FE Base PIC
FPC 1                                                    FPC
  PIC 0                                                  Internal VDSL2/ADSL-POTS
Power Supply 0
```

---
**Step 2: Check your software version**

Check your software version. This configuration is compatible with JunOS 12.1X46-D86. Maybe older versions have some bugs or not stable. This is the latest version for SRX110H-VA. In the CLi mode with **`root@DeviceName> show version`**. The print is like below. If your version is not 12.1X46-D86, please upgrade first.
```
root@DeviceName> show version
Hostname: DeviceHostName
Model: srx110h-va
JUNOS Software Release [12.1X46-D86]
```
**Step 2.1: Upgrade your SRX110H-VA**

1. Download the **junos-srxsme-12.1X46-D86-domestic.tgz** [here](https://drive.google.com/open?id=10iDazV4GYqOMTLWQHSPY-9upUOu7Ejwv).
2.  Verify the downloaded file via checksum.
3. [Click here](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16652) to follow the instructions on official Juniper KB16652 document.

---
**Step 3: Delete entire configuration**

At this step,  we will delete all configuration and load a new one. In *`[edit]`* mode just type **`delete`**. Your prompt should be like below:
```
root@DeviceName# delete
This will delete the entire configuration
Delete everything under this level? [yes,no] (no)
```

**Step 4: Install a new config**

Now, we are ready to load configuration to SRX110H-VA. Copy the configuration from GitHub repository and in *`[edit]`* mode this time just type **`load set terminal`**. Your prompt should be like below:

```
root@DeviceName# load set terminal
[Type ^D at a new line to end input]
Paste the configuration which one is you copied
```
When you pasted the configuration, type CTRL+D (if you are on Windows or Linux), type control + D /  ^D (if you are on macOS). This key combination is sending a "save my config" signal to SRX110H-VA. Your screen should be below:

```
load complete

[edit]
root@DeviceName#
```

**Step 5: Save the new config**

Now, time to save the configuration of SRX110H-VA. Just write a **`commit`** and take a drink from your coffee. SRX110H-VA should say like this:

```
commit complete

[edit]
root@DeviceName#
```


**Step 6: Check the connection status**

**`show interfaces pp0.0`** command is the easiest way to check VDSL connection brief when you are in CLi mode. PPPoE state should be shown your session is up and session ID. Also, PAP State should be a success. Your output should be like that:

```
root@DeviceName> show interfaces pp0.0
  Logical interface pp0.0 (Index 81) (SNMP ifIndex 534)
    Flags: Point-To-Point SNMP-Traps 0x0 Encapsulation: PPPoE
    PPPoE:
      State: SessionUp, Session ID: 1,
      Session AC name: TT-35-IZMIR-ERC-SSR-01, Remote MAC address: ac:60:b6:5f:b7:bc,
      Configured AC name: None, Service name: <ProviderName>,
      Auto-reconnect timeout: 5 seconds, Idle timeout: Never,
      Underlying interface: pt-1/0/0.0 (Index 80)
    Input packets : 16696249
    Output packets: 23477163
  Keepalive settings: Interval 10 seconds, Up-count 1, Down-count 3
  Keepalive: Input: 22315 (00:00:22 ago), Output: 134650 (00:00:06 ago)
  LCP state: Opened
  NCP state: inet: Opened, inet6: Not-configured, iso: Not-configured, mpls: Not-configured
  CHAP state: Closed
  PAP state: Success
    Security: Zone: untrust
    Protocol inet, MTU: 1492
      Flags: Sendbcast-pkt-to-re, Negotiate-Address
      Addresses, Flags: Kernel Is-Preferred Is-Primary
        Destination: 172.17.1.220, Local: 100.67.3.70
```

If you want to check PPPoE connection details deeply, in CLi mode run **`root@DeviceName> show interfaces pp0.0 extensive | no-more`** Your output should be like below:

```
  Logical interface pp0.0 (Index 81) (SNMP ifIndex 534) (Generation 146)
    Flags: Point-To-Point SNMP-Traps 0x0 Encapsulation: PPPoE
    PPPoE:
      State: SessionUp, Session ID: 1,
      Session AC name: TT-35-IZMIR-ERC-SSR-01, Remote MAC address: ac:60:b6:5f:b7:bc,
      Configured AC name: None, Service name: <ProviderName>,
      Auto-reconnect timeout: 5 seconds, Idle timeout: Never,
      Underlying interface: pt-1/0/0.0 (Index 80)
    Traffic statistics:
     Input  bytes  :          22971671300
     Output bytes  :           3564766397
     Input  packets:             16731511
     Output packets:             23497722
    Local statistics:
     Input  bytes  :              5768729
     Output bytes  :              5771877
     Input  packets:               158251
     Output packets:               159365
    Transit statistics:
     Input  bytes  :          22965902571                13768 bps
     Output bytes  :           3558994520                14064 bps
     Input  packets:             16573260                   15 pps
     Output packets:             23338357                   19 pps
  Keepalive settings: Interval 10 seconds, Up-count 1, Down-count 3
  Keepalive statistics:
    Input : 22325 (last seen 00:00:24 ago)
    Output: 134710 (last sent 00:00:08 ago)
  LCP state: Opened
  NCP state: inet: Opened, inet6: Not-configured, iso: Not-configured, mpls: Not-configured
  CHAP state: Closed
  PAP state: Success
    Security: Zone: untrust
    Flow Statistics :
    Flow Input statistics :
      Self packets :                     1736
      ICMP packets :                     1525
      VPN packets :                      0
      Multicast packets :                0
      Bytes permitted by policy :        103260213589
      Connections established :          0
    Flow Output statistics:
      Multicast packets :                0
      Bytes permitted by policy :        3540408726
    Flow error statistics (Packets dropped due to):
      Address spoofing:                  0
      Authentication failed:             0
      Incoming NAT errors:               0
      Invalid zone received packet:      0
      Multiple user authentications:     0
      Multiple incoming NAT:             0
      No parent for a gate:              0
      No one interested in self packets: 0
      No minor session:                  0
      No more sessions:                  0
      No NAT gate:                       0
      No route present:                  0
      No SA for incoming SPI:            0
      No tunnel found:                   0
      No session for a gate:             0
      No zone or NULL zone binding       0
      Policy denied:                     0
      Security association not active:   0
      TCP sequence number out of window: 63
      Syn-attack protection:             0
      User authentication errors:        0
    Protocol inet, MTU: 1492, Generation: 159, Route table: 0
      Flags: Sendbcast-pkt-to-re, Negotiate-Address
      Addresses, Flags: Kernel Is-Preferred Is-Primary
        Destination: 172.17.1.220, Local: 100.67.3.70, Broadcast: Unspecified, Generation: 154
```

## Congratulations

Now you can use your Juniper SRX110H-VA device to connect to the internet. You can use the physical ports to connect to your desktop, laptop, tv, media center or wireless router.

---
If you want to be a contributor, you can always kindly very welcome. Just KISS (keep it simple stupid) to your configuration.

