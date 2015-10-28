# MITM-VM (Name Pending)

## Description
This is an easy-to-deploy virtual machine that can provide flexible man-in-the-middle capabilities. This project will require little configuration, require little additional hardware, and provide many utilites and tools to accomplish common (and not so common) man-in-the-middle scenarios.

## Includes the following tools
* [mitmproxy](https://mitmproxy.org/index.html)

    * An interactive console program that allows HTTP traffic flows to be intercepted, inspected, modified and replayed.

* [netsed](http://manpages.ubuntu.com/manpages/lucid/man1/netsed.1.html)

    * A utility that is designed to alter the contents of packets forwarded through your network in real time.

* [sslstrip](http://www.thoughtcrime.org/software/sslstrip/)

    * A tool to transparently hijack HTTP traffic on a network, watch for HTTPS links and redirects, then map those links into either look-alike HTTP links or homograph-similar HTTPS links.

* [sslsniff](http://www.thoughtcrime.org/software/sslsniff/)

    * Constructs new certificate chains for SSL/TLS connections on the fly.

* [socat](http://www.dest-unreach.org/socat/)

    * A relay for bidirectional data transfer between two independent data channels. Each of these data channels may be a file, pipe, device (serial line etc. or a pseudo terminal), a socket (UNIX, IP4, IP6 - raw, UDP, TCP), an SSL socket, proxy CONNECT connection, a file descriptor (stdin etc.), the GNU line editor (readline), a program, or a combination of two of these.

* [btproxy](https://github.com/conorpp/btproxy)

    * Man-in-the-Middle analysis for bluetooth.

* [killerbee](https://github.com/riverloopsec/killerbee)

    * IEEE 802.15.4/ZigBee Security Research Toolkit

## Setup

### Virtual Machine Setup
`git clone https://git.praetorianlabs.com/kludwig/mitm-vm.git`

`cd mitm-vm`

`vagrant up`

When prompted, select the interface that will be the gateway interface


### Host Setup
This is dependent on the use case. The following use-cases should cover most situations.
#### Use Case 1: The device you want to man-in-the-middle connects to the Internet over Wi-Fi.
0. Update the INTERNET_ROUTER_IP environment variable in bootstrap.sh to match the IP address of your host gateway.

1. Spin-up the mitm-vm using `vagrant up`. During initializiation you will be prompted to select an interface for bridging in the vm. Select the interface that will go from the virtual machine to the Internet. In this use-case, it will be your Ethernet interface.


2. Route all traffic on your Macbook through the VM. 
    * This can be done via your Network System Preferences in OS X. System Preferences → Network → Ethernet → Configure IPv4 → Manually → Set Static IP to a valid static IP, set the subnet to 0.0.0.0, and set Router to the ip address of the mitm-vm.

3. Confirm that you have Internet access on the host, and that traffic is routing through the VM.

4. [Turn your Macbook into a Wireless Acces Point](http://support.apple.com/kb/PH13855?locale=en_US)

5. Confirm that the device you want to mitm can access the internet. I typically test this by pinging 8.8.8.8 on my host. If I actually get a response, I will check that it is filtering through the vm. To confirm this, run tcpdump on the vm (make sure you specify the correct interface!).

6. Run `vagrant ssh` to get on the mitm-vm and do all your sniffing/modifying/etc.

### Use Case 3: You want to sniff / proxy bluetooth

1. Install the [VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads). You need the most recent version of VirtualBox to do this (don't trust the "Check For Updates" mechanism in VirtualBox).

2. Add a USB filter for the OS X bluetooth device.  VirtualBox -> Mitmvm -> Settings -> Ports -> USB -> Little USB with a "+".

3. Before booting the vm, you will need to convince OS X to relinquish control of the bluetooth hardware. I have provided scripts to automate disabled and re-enabled host control of the bluetooth hardware (bt-down.sh and bt-up.sh). Run bt-down as sudo. This will create a file of the modules that were disabled. Re-enabling the modules needs this file so please don't delete it.

4. Boot the vagrant vm. Test that you have access to the bluetooth module by running `hcitool dev`. You should see a device! This same utility can be used to sniff and attach to devices. For bluetooth mitm please refer to [btproxy's documentation](https://github.com/conorpp/btproxy).

### Use Case 4: You want to sniff / modify Zigbee traffic

1. Follow the steps for the bluetooth use case.



## Planned / TODO
* Mallory sucks. I am going to deveop my own mallory-like system.

* The VM is sending off UDP packets but never returning them to the host.

* Scapy support would be nice.

* Setup a proper GOPATH.

* Man-in-the-Middle framework? https://github.com/byt3bl33d3r/MITMf
