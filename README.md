# Final Case Study Using Network Automation
Final Case Study for Developing Automation ( CPE 028 ) <br/>
Written and Edited by _Glen John Gerandoy_
#### Requirements
- GNS3
- Ubuntu ISO
- DevAsc
- PC RAM Atleast ( 8 GB or UP )
- Virtual Box
## Network Topology
![image](https://user-images.githubusercontent.com/75372989/149444619-41fc379b-24fe-4967-aee3-583888d9d6b2.png) <br/>
## Devices 
Note: Basic Configuration for the routers are required to have its connectivity between PC to PC
- Two (2) Cisco Routers (C3725)
- Two (2) Cisco Switch
- Ubuntu
- DevAsc Lab VM <br/>
## SSH Configuration in Ubuntu and DevAsc
##### DevAsc SSH Config (`~/.ssh/config`) <br/>
```bash
Host *
    Port 22
    User cisco
    StrictHostKeyChecking=no
    UserKnownHostsFile=/dev/null
    KexAlgorithms +diffie-hellman-group1-sha1
    Ciphers +3des-cbc

    SendEnv LANG LC_*
    Ciphers +aes256-cbc
```
##### DevAsc Netplan (`/etc/netplan/01-netcfg.yaml`)
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.10.2/24
      gateway4: 192.168.10.1
```
```bash
$ sudo netplan apply
```

##### Ubuntu SSH Config (`~/.ssh/config`) <br/>
```bash
Host *
    Port 22
    User cisco
    StrictHostKeyChecking=no
    UserKnownHostsFile=/dev/null
    KexAlgorithms +diffie-hellman-group1-sha1
    Ciphers +3des-cbc

    SendEnv LANG LC_*
    Ciphers +aes256-cbc
```
##### Ubuntu Netplan (`/etc/netplan/01-netcfg.yaml`)
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.0.2/24
      gateway4: 192.168.0.1
```
```bash
$ sudo netplan apply
```
#### Router 1
```
Current configuration : 1809 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R1
!
boot-start-marker
boot-end-marker
!
enable secret 5 $1$K48n$MZWR0PzN5t617atM1HHGD1
!
no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
ip cef
!
!
!
!
no ip domain lookup
ip domain name www.abc.com
!
multilink bundle-name authenticated
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
username cisco password 0 cisco123
archive
 log config
  hidekeys
!
!
!
!
ip tcp synwait-time 5
ip ssh version 2
!
!
!
!
interface FastEthernet0/0
 ip address 192.168.10.1 255.255.255.0
 duplex auto
 speed auto
!
interface Serial0/0
 ip address 10.10.10.1 255.255.255.252
 clock rate 2000000
!
interface FastEthernet0/1
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface Serial0/1
 no ip address
 shutdown
 clock rate 2000000
!
interface Serial0/2
 no ip address
 shutdown
 clock rate 2000000
!
interface FastEthernet1/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface Serial2/0
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/1
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/2
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/3
 no ip address
 shutdown
 serial restart-delay 0
!
router ospf 1
 log-adjacency-changes
 network 0.0.0.0 255.255.255.255 area 0
!
ip forward-protocol nd
ip route 0.0.0.0 0.0.0.0 10.10.10.2
!
!
no ip http server
no ip http secure-server
!
no cdp log mismatch duplex
!
!
!
!
!
!
control-plane
!
!
!
!
!
!
!
!
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login local
 transport input ssh
line vty 5 15
 login local
 transport input ssh
!
!
end
```
#### Router 2
```
Current configuration : 1913 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R2
!
boot-start-marker
boot-end-marker
!
enable secret 5 $1$b8FM$KPg0cA7nBwJaic5JuUi6V1
!
no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
ip cef
!
!
!
!
no ip domain lookup
ip domain name www.abc.com
!
multilink bundle-name authenticated
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
username cisco password 0 cisco123
archive
 log config
  hidekeys
!
!
!
!
ip tcp synwait-time 5
ip ssh version 2
!
!
!
!
interface FastEthernet0/0
 ip address 192.168.0.1 255.255.255.0
 ip access-group 110 in
 duplex auto
 speed auto
!
interface Serial0/0
 ip address 10.10.10.2 255.255.255.252
 clock rate 2000000
!
interface FastEthernet0/1
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface Serial0/1
 no ip address
 shutdown
 clock rate 2000000
!
interface Serial0/2
 no ip address
 shutdown
 clock rate 2000000
!
interface FastEthernet1/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface Serial2/0
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/1
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/2
 no ip address
 shutdown
 serial restart-delay 0
!
interface Serial2/3
 no ip address
 shutdown
 serial restart-delay 0
!
router ospf 1
 log-adjacency-changes
 network 0.0.0.0 255.255.255.255 area 0
!
ip forward-protocol nd
ip route 0.0.0.0 0.0.0.0 10.10.10.1
!
!
no ip http server
no ip http secure-server
!
access-list 110 deny   icmp any any echo-reply
access-list 110 permit ip any any
no cdp log mismatch duplex
!
!
!
!
!
!
control-plane
!
!
!
!
!
!
!
!
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login local
 transport input ssh
line vty 5 15
 login local
 transport input ssh
!
!
end
```
Note: When applying a network automation on routers, SSH Configuration should be applied in routers. <br/>
When configuring a SSH in router, first is to apply `ip domain-name` then generate rsa by doing a command of `crypto key gen rsa` then for the modulus it should be set to `1024` <br/>

## What is Network Automation? <br/>
Network automation is the process of using software to automate network and security provisioning and management in order to continuously maximize network efficiency and functionality.
#### List of YAML Playbook
- acl.yaml
- ospf.yaml
- webserver.yaml

## Implementing PYATS and Genie <br/>
pyATS is an end-to-end DevOps automation ecosystem. ... Assess whether new products, releases or changesets work with the rest of your network using the pyATS ecosystem as your gatekeeper.
