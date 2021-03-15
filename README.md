N E M E S I S  -  Crafting & Injection
======================================
[![License Badge][]][License]  [![Travis Status][]][Travis] [![Coverity Status][]][Coverity Scan] <img align="right" src="docs/logo.png">

The Nemesis Project is designed to be a command line based, portable
human IP stack for UNIX-like and Windows systems.  The suite is broken
down by protocol, and should allow for useful scripting of injected
packets from simple shell scripts.

The latest release is always available from GitHub at  
> https://github.com/libnet/nemesis/releases


Key Features
------------

* ARP/RARP, DNS, ETHERNET, ICMP, IGMP, IP, OSPF, RIP, TCP and UDP
  protocol support
* Layer 2 or Layer 3 injection on UNIX-like systems
* Layer 2 injection (only) on Windows systems
* Packet payload from file
* IP and TCP options from file
* Tested on OpenBSD, Linux, Solaris, Mac OS X and Windows 2000

Each supported protocol uses its own protocol "injector" which is
accompanied by a man page explaining its functionality.

Consult the ChangeLog for release details, and the documentation for
each protocol injector for in-depth descriptions of the available
functionality.


Examples
--------

* Inject malformed ICMP redirect

        sudo nemesis icmp -S 10.10.10.3 -D 10.10.10.1 -G 10.10.10.3 -i 5

* DHCP Discover (must use sudo and `-d` to send with source IP 0.0.0.0):

        sudo nemesis dhcp -d eth0

* IGMP v2 join for group 239.186.39.5

        sudo nemesis igmp -v -p 22 -S 192.168.1.20 -g 239.186.39.5 -D 239.186.39.5

* IGMP v2 query, max resp. time 10 sec, with Router Alert IP option

        echo -ne '\x94\x04\x00\x00' >RA
        sudo nemesis igmp -v -p 0x11 -c 100 -D 224.0.0.1 -O RA

  or

        echo -ne '\x94\x04\x00\x00' | sudo nemesis igmp -v -p 0x11 -c 100 -D 224.0.0.1 -O -

* IGMP v3 query, with Router Alert IP option

        echo -ne '\x03\x64\x00\x00' > v3
        sudo nemesis igmp -p 0x11 -c 100 -i 0.0.0.0 -P v3 -D 224.0.0.1 -O RA

* Random TCP packet

        sudo nemesis tcp

* DoS and DDoS testing

        sudo nemesis tcp -v -S 192.168.1.1 -D 192.168.2.2 -fSA -y 22 -P foo
        sudo nemesis udp -v -S 10.11.12.13 -D 10.1.1.2 -x 11111 -y 53 -P bindpkt
        sudo nemesis icmp redirect -S 10.10.10.3 -D 10.10.10.1 -G 10.10.10.3 -qR
        sudo nemesis arp -v -d eth0 -H 0:1:2:3:4:5 -S 10.11.30.5 -D 10.10.15.1


Build & Install
---------------

Nemesis is built around [libnet][].  Windows platform builds require
[libpcap][] as well.  Nemesis <= 1.4 was built around libnet 1.0 and
Nemesis >= 1.5 require libnet 1.1, or later.

### Debian/Ubuntu

    curl -sS https://deb.troglobit.com/pubkey.gpg | sudo apt-key add -
    echo "deb [arch=amd64] https://deb.troglobit.com/debian stable main" | sudo tee /etc/apt/sources.list.d/troglobit.list
    sudo apt-get update && sudo apt-get install nemesis

### Building from Source

On Debian and Ubuntu derived GNU/Linux systems:

    sudo apt install libnet1-dev

This installs the libnet headers and library in a standard location
which the `configure` script easily can find.  Should your libnet1
installation be in a non-standard location you can provide the paths
like this:

    configure LDFLAGS=-L/path/to/lib CPPFLAGS=-I/path/to/header

The [GNU Configure & Build][buildsystem] system use `/usr/local` as the
default install prefix.  Usually this is sufficient, the below example
installs to `/usr` instead:

    tar xf nemesis-1.7.tar.xz
    cd nemesis-1.7/
    ./configure --prefix=/usr
    make -j5
    sudo make install-strip

### Installing on Windows

nemesis.exe can be installed anywhere on a Windows system.  The caveat is 
that LibnetNT.dll must exist either in the same directory as nemesis.exe or
in any of the directories listed in the `%PATH%` variable.  On Windows 2000
this would be `%SystemRoot%\System32`

**Note:** the windows build has not been tried or tested in over a decade. YYMV


Building from GIT
-----------------

If you want to contribute, or simply want to try out the latest but
still unreleased features, then you need to know a few things about
the [GNU Configure & Build][buildsystem] system:

- `configure.ac` and a per-directory `Makefile.am` are key files
- `configure` and `Makefile.in` are generated from `autogen.sh`,
  they are not stored in GIT but automatically generated for the
  release tarballs
- `Makefile` is generated by `configure` script

To build from GIT you first need to clone the repository and run the
`autogen.sh` script.  This requires `automake` and `autoconf` to be
installed on your system.

    git clone https://github.com/libnet/nemesis.git
    cd nemesis/
    ./autogen.sh
    ./configure && make

GIT sources are a moving target and are not recommended for production
systems, unless you know what you are doing!


Origin & References
--------------------

* **1999**: Nemesis was created by Mark Grimes
* **2001**: Jeff Nathan took over maintainership
* **2018**: Project resurrected by Joachim Nilsson

The project is currently maintained at [GitHub][] with the intention to
serve as a focal point for new development.  If you have patches and/or
ideas, please submit them using the issue tracker or as pull requests.

[License]:         https://en.wikipedia.org/wiki/BSD_licenses
[License Badge]:   https://img.shields.io/badge/License-BSD%203--Clause-blue.svg
[GitHub]:          https://github.com/libnet/nemesis
[libnet]:          https://sourceforge.net/projects/libnet-dev/
[libpcap]:         http://www.tcpdump.org/
[buildsystem]:     https://airs.com/ian/configure/
[Travis]:          https://travis-ci.org/libnet/nemesis
[Travis Status]:   https://travis-ci.org/libnet/nemesis.png?branch=master
[Coverity Scan]:   https://scan.coverity.com/projects/15484
[Coverity Status]: https://scan.coverity.com/projects/15484/badge.svg
