cjdns: cjd's Network Suite
==========================

Dear Reader,

I suppose you are here because you are interested in alternative networks,
perhaps for censorship resistance, perhaps network security and I have no doubt
you are wondering what the hell this thing is supposed to do.

We can all find common ground in the statement that The Internet is painfully
insecure. Free speech and privacy advocates find it insecure against government
listening and blocking, governments find it insecure against hackers taking 
systems over and leaking secrets, and internet service providers find it
insecure against DDoS kiddies who use large swarms of zombie machines to send
enough traffic to overload a network link. These are, however, all different 
views of the same problem.

We have a number of somewhat competing offerings to solve this problem from ISPs
and government. We have IPSEC, DNSSEC, numerous proposals from the mundane to 
the wild and whacky such as "internet drivers licenses".

The people who have developed these proposals are unfortunately limited in 
their thinking. ISPs are unable to see past the now almost 30 year old routing
protocols which glue together the internet of today. Government actors are
conditioned to think of something as secure when they have control over it.
A quick look at x509 (the authentication system behind SSL) shows us that
central points of failure inevitably live up to their name. In order to have a
central authority, the people must not only be able to trust his motives but
they must be able to trust his system's integrity as well. Recently people's 
e-mail was compromised when DigiNotar certificate authority was hacked and used
to forge gmail certificates.

It is worthy of note that the vulnerability in DNS which ICE exploited to take
down websites they deemed "dedicated to copyright infringement" was also used
by Anonymous to replace a movie industry website with a manifesto.

*A System Is Only Secure When Nobody Has Total Control*


What is cjdns?
--------------

It is a routing engine designed for security, scalability, speed and ease of
use. The dream: You type `./cjdns` and give it an interface which connects
another node and it gives you an ipv6 address generated from a public
encryption key and a virtual network card (TUN device) which you can use to
send packets to anyone in the cjdns network to which you are connected.


How does it work?
-----------------

In order to understand how cjdns works, it is important to understand how the
existing internet works when you send a packet, at each "intersection in the
road" the router reads the address on the packet and decides which turn it
should take. In the cjdns net, a packet goes to a router and the router labels
the packet with directions to a router which will be able to best handle it.
That is, a router which is near by in physical space and has an address which
is numerically close to the destination address of the packet. The directions
which are added to the packet allow it to go through a number of routers
without much handling, they just read the label and bounce the packet wherever
the next bits in the label tell them to. Routers have a responsibility to
"keep in touch" with other routers that are numerically close to their address
and also routers which are physically close to them.

The router engine is a modified implementation of the Kademlia DHT design.


How close is it to complete?
----------------------------

A live testing network exists with at least 15 active nodes.


What about DNS?
---------------

DNS is a complex system to implement and highly complex to implement without
central authority, if you would like to offer help with this part, I invite you
to come join.


Further Reading & Discussion
----------------------------

Please read the Whitepaper, or at least skim it:

  * https://github.com/cjdelisle/cjdns/raw/master/rfcs/Whitepaper.md

If you are still interested in this project and want to follow it, 
get in the channel on IRC:

  * irc://irc.EFNet.org/#cjdns
  * http://chat.efnet.org:9090/?channels=%23cjdns&Login=Login

Some raw pastes for the curious:

  * http://cjdns.pastebay.org/


Thank you for your time and interest,
Caleb James DeLisle  ==  cjdelisle  ==  cjd


--------------------------------------------------------------------------------


    Possibly outdated below.
    Please check IRC for the latest info.


Build
=====

How to compile cjdns on Debian 6 (Squeeze):

  * Hint 1: You did a backup recently. ;)
  * Hint 2: Might work same under Ubuntu and Linux Mint.

1: Remove older versions of dependencies: `libevent` and `libevent-dev`.
------------------------------------------------------------------------

Be sure libevent is gone and remove if found. 
It will cause problems during the build.

Check to see which libevent is installed:

    # dpkg -l | grep ^ii| grep libevent
    ii  libevent-dev            1.3e-3     Development libraries, header files and docs
    ii  libevent1               1.3e-3     An asynchronous event notification library
    # apt-get remove libevent-dev

**Note: You may need to (re)compile TOR if you use it.**

2: Obtain latest versions of dependencies from repository.
----------------------------------------------------------

    # apt-get install build-essential cmake git

3: Obtain latest `libevent2` dependency manually.
-------------------------------------------------

CHECK https://github.com/libevent/libevent for LATEST version.
(This document assumes 2.0.16.)

Grab the stable tarball from libevent and untar:

    # wget https://github.com/downloads/libevent/libevent/libevent-2.0.16-stable.tar.gz
    # tar -xzf libevent-2.0.16-stable.tar.gz

Enter directory and compile libevent:

    # cd libevent-2.0.16-stable
    # ./configure

Resolve missing dependencies if needed and run again until all errors gone:

    # make
    # make install

4: Retrieve cjdns from GitHub.
------------------------------

Grab it from GitHub and change to the source directory:

    # git clone https://github.com/cjdelisle/cjdns.git cjdns
    # cd cjdns

5: Setup environment.
---------------------

Setup `build` directory and change to it:

    # mkdir build
    # cd build

You Likely want DEBUG logs (this is VERY ALPHA after all), so set the
`Log_LEVEL` environment variable:

    # export Log_LEVEL=DEBUG

6: Build.
---------

Pre-build step with `cmake`:

    # cmake ..

Build cjbdns:

    # make

Look for:

    [100%] Built target LabelSplicer_test

ALL DONE! Wanna test? Sure.

    # make test

--------------------------------------------------------------------------------


Installation
============

Use `screen` or such to get a few ttys, Xterms, pipe to log and bg, whatever.

Change to the `cjdns/build` directory if you need to.

Run cjdroute without options for HELP:

    # ./cjdroute

0: Make sure you've got the stuff.
----------------------------------

    # sudo modprobe tun

If it says nothing, good.

If it says: `FATAL: Module tun not found.` Bad.

In this case, look up how to get the tun module installed for your platform.
How to get TUN working is beyond the scope of this document, look up how to install
openvpn on your particular platform.

NOTE: TonidoPlug2 ships with a kernel which does not include TUN and does not
offer the source code to build it yourself.

    # cat /dev/net/tun

If it says: `cat: /dev/net/tun: File descriptor in bad state` Good!

If it says: `cat: /dev/net/tun: No such file or directory`

Create it using:

    # mkdir /dev/net ; mknod /dev/net/tun c 10 200 ; chmod 0666 /dev/net/tun

Then `cat /dev/net/tun` again.

1: Generate a new configuration file.
-------------------------------------

    # ./cjdroute --genconf >> cjdroute.conf

2: Find a friend.
-----------------

In order to get into the network you need to meet someone who is also in the network and connect
to them. This is required for a number of reasons:

1. It is a preventitive against abuse because bad people will be less likely to abuse a
   system after they were, in an act of human kindness, given access to that system.
2. This is not intended to overlay The Old Internet, it is intended to replace it. Each connection
   will in due time be replaced by a wire, a fiber optic cable, or a wireless network connection.
3. In any case of a disagreement, there will be a "chain of friends" linking the people involved
   so there will already be a basis for coming to a resolution.

tl;dr Get out and make some human contact once in a while!

You can meet people to peer with in the IRC channel:

  * irc://irc.EFNet.org/#cjdns
  * http://chat.efnet.org:9090/?channels=%23cjdns&Login=Login

NOTE: If you're just interested in setting up a local VPN between your computers, this step is
  not necessary.

3: Fill in your friend's info.
------------------------------

In your cjdroute.conf file, you will see:

            // Nodes to connect to.
            "connectTo":
            {
                // Add connection credentials here to join the network
                // Ask somebody who is already connected.
            }

After adding their connection credentials, it will look like:

            // Nodes to connect to.
            "connectTo":
            {
                "0.1.2.3:45678":
                {
                    "password": "thisIsNotARealConnection",
                    "authType": 1,
                    "publicKey": "thisIsJustForAnExampleDoNotUseThisInYourConfFile.k",
                    "trust": 10000
                }
            }

You can add as many connections as you want to your "connectTo" section.

Your own connection credentials will be shown in a JSON comment above in your
"authorizedPasswords" section. Do not modify this but if you want to allow someone
to connect to you, give it to them.

It looks like this:

        /* These are your connection credentials
           for people connecting to you with your default password.
           adding more passwords for different users is advisable
           so that leaks can be isolated.

            "your.external.ip.goes.here:12345":
            {
                "password": "thisIsNotARealConnectionEither",
                "authType": 1,
                "publicKey": "thisIsAlsoJustForAnExampleDoNotUseThisInYourConfFile.k",
                "trust": 10000
            }
        */

`your.external.ip.goes.here` is to be replaced with the IPv4 address which people will use to
connect to you from over The Old Internet.

4a: Startup the easy way!
------------------------

If you're using an OpenVZ based VPS then you will need to use this as OpenVZ does not permit
persistent tunnels.

    sudo su -c "./cjdroute < cjdroute.conf >> cjdroute.log & ./cjdroute --getcmds < cjdroute.conf | bash"


4b: Run cjdroute as non-root.
-----------------------------

cjdroute will by default change it's username to "nobody" (an unprivileged user) after startup
but if you don't trust me or you want to be extra sure, you can run it without root access even
in the beginning.

Create a cjdns user:

    # useradd cjdns

Create a new TUN device and give the cjdns user authority to access it:

    # /sbin/ip tuntap add mode tun user cjdns
    # /sbin/ip tuntap list | grep `id -u cjdns`

The output of the last command will tell you the name of the new device.
If that name is not `"tun0"` then you will need to edit the cjdroute.conf file
and change the line which says: `"tunDevice": "tun0"` to whatever it is.

4b-1: Get commands.
----------------

Get the commands to run in order to prepare your TUN device by running:

    # ./cjdroute --getcmds < cjdroute.conf

These commands should be executed as root now every time the system restarts.

4b-2: Fire it up!
--------------

    # sudo -u cjdns ./cjdroute < cjdroute.conf

Notes
-----

Protect your conf file! A lost conf file means you lost your password and connections
and anyone who connected to you will nolonger be able to connect. A *compromized* conf
file means that other people can impersonate you on the network.

    chmod 400 cjdroute.conf
    mkdir /etc/cjdns ; cp ./cjdroute.conf /etc/cjdns/

To delete a tunnel, use this command:

    # /sbin/ip tuntap del mode tun <name of tunnel>


--------------------------------------------------------------------------------


Known Issues
============

Old versions of the IP utility do not work for creating tunnel devices.
-----------------------------------------------------------------------

    # ip -V
    ip utility, iproute2-ss080725
    # /sbin/ip tuntap add mode tun user cjdns
    Object "tuntap" is unknown, try "ip help".

    # /sbin/ip tuntap list
    Object "tuntap" is unknown, try "ip help".

    # ip -V
    ip utility, iproute2-ss100519
    # /sbin/ip tuntap list
    tun0: tun user 1001

The fix: for now grab a copy of a newer `ip` binary and copy it to your home 
directory. Replacing the system binaries is not likely a good idea.

Currently we are still debugging some routing issues.
-----------------------------------------------------

If you want to help out, load up a few VMs or physical boxen,
link them, see what happens, tell us!  :)

Lots of bugs to fix yet, but hey, it's talking now!


--------------------------------------------------------------------------------


Self-Check Your Network
=======================

Once your node is running, you're now a newly minted IPv6 host. Your operating
system may automatically reconfigure network services to use this new address.
If this is not what you intend, you should check to see that you are not 
offering more services then you intended to.  ;)

1: Obtain IP address.
---------------------

Use `ifconfig -a` to find your TUN device's IPv6 address. (Same as above.)

2: Scan for open services.
--------------------------

Run `nmap` to discover which services are accessible from this address.
For example, to scan the address fcf7:75f0:82e3:327c:7112:b9ab:d1f9:bbbe:

    # nmap -6 -n -r -v -p1-65535 -sT fcf7:75f0:82e3:327c:7112:b9ab:d1f9:bbbe

    Starting Nmap 5.61TEST2 ( http://nmap.org ) at 2011-12-29 20:40 EST
    Initiating Connect Scan at 20:40
    Scanning fcf7:75f0:82e3:327c:7112:b9ab:d1f9:bbbe [65535 ports]
    Completed Connect Scan at 20:40, 4.38s elapsed (65535 total ports)
    Nmap scan report for fcf7:75f0:82e3:327c:7112:b9ab:d1f9:bbbe
    Host is up (0.00073s latency).
    All 65535 scanned ports on fcf7:75f0:82e3:327c:7112:b9ab:d1f9:bbbe are closed

    Read data files from: /usr/local/bin/../share/nmap
    Nmap done: 1 IP address (1 host up) scanned in 4.60 seconds
        Raw packets sent: 0 (0B) | Rcvd: 0 (0B)

3: If you see anything open, fix it.
------------------------------------

Examples for SSH and Samba are below.

### SSH

Edit `/etc/ssh/sshd_config`:

    ListenAddress 192.168.1.1

^ Replace `192.168.1.1` in the example above 
  with your STATIC IP (or map DHCP via MAC).

### Samba

Edit `/etc/samba/smb.conf`:

    [global]      
    interfaces = eth0 
    bind interfaces only = Yes

^ This will cause Samba to not bind to `tun0` 
  (or whichever TUN device you are using).

Thats it for now! Got More? Tell us on IRC.

--------------------------------------------------------------------------------

Created on 2011-02-16.  
Last modified on 2011-12-30.
