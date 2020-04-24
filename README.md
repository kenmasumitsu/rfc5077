Various tools for testing RFC 5077
==================================

[RFC 5077](http://tools.ietf.org/html/rfc5077) is a session resumption
mechanism for TLS without server-side state. You'll find here various
tools related to testing availability of RFC 5077.

This mechanism is an extension for TLS. If a client or a server does
not support TLS, it does not support RFC 5077.

Clients
-------

The following clients are implemented:

 - `openssl-client`
 - `gnutls-client`
 - `nss-client`

They all take an host and a port as argument. You need to use `-r`
flag to really test reconnection. You can also add `-T` to disable
ticket supports (RFC 5077) and `-S` to disable session ID
support. However, disabling session ID may be difficult, therefore, it
may not really have the expected effect.

Only OpenSSL client is complete enough. GNU TLS does not allow easy
display of session contents and NSS does not allow to check if a
session was resumed.

Additionally, `rfc5077-client` proposes some more advanced tests
against a server or a pool of servers. It will try to reuse sessions
with and without tickets and will query several time each IP of a pool
of servers. Use this if you want to check support of SSL session
resume of a server or a pool of servers.

It is possible that those clients may fail if you don't have a working
IPv6 connectivity. Get an IPv6 connectivity. ;-)

Servers
-------

`rfc5077-server` allows you to test support of RFC 5077 in the client
of your choice. It will returns an HTML page containing some
Javascript code to test browsers. You need to specify 4 ports. They
will respectively behave as follow:

 1. No session cache, no ticket support
 2. Session cache, no ticket support
 3. Session cache, ticket support
 4. No session cache, ticket support

While this server has some shortcoming, it should be relatively
performant and you can try to bench it. It should also be secure
enough to be put on the Internet.

Misc
----

`rfc5077-pcap` will analyze SSL handshakes contained in PCAP files. It
will try to detect "Client Hello". It will extract IP addesses,
protocol version, Session ID, cipher suites, compression methods and
detect the use of SNI extension and ticket extension. It should be
used to determine how many clients support one cipher suite or how
many clients support ticket extension.

The CSV file generated by this program can then be used with
`rfc5077-stats.py` that will produce some graphics (and also build a
SQLite database that you can use to make queries).

Getting Started
---------------

If you've just cloned this from git, run the following to ensure that
the submodules `http-parser` and `httpagentparser` are installed:

- `git submodule init`
- `git submodule update`

Then run `make` to build the executables. This currently needs **OpenSSL 1.1**.
If you have an older version, go back to branch `openssl-1.0`:

- `git checkout openssl-1.0`

Dependencies
------------

To compile these you will need a few dependencies that are the nss,
openssl, gnutls, libpcap, libev and nspr headers and libraries:

On Fedora the dependencies are:
 * openssl-devel
 * gnutls-devel
 * nss-devel
 * libpcap-devel
 * libev-devel
 * nspr-devel
 * pkgconfig

On Debian, the dependencies can be installed with the following command:

```bash
apt-get install libssl-dev gnutls-dev libnss3-dev libpcap-dev libev-dev libnspr4-dev pkgconf
```

On Osx the denpendencies are: (which can be installed by homebrew)
 * openssl@1.1
 * gnutls
 * nss
 * libpcap
 * libev
 * pkg-config

```bash
# install denpendencies
brew install openssl@1.1 gnutls nss libpcap libev pkg-config

# openssl@1.1, nss, libpcap are keg-only we should export some env before make
export PATH=$(brew --prefix)/opt/nss/bin:$PATH
export PATH=$(brew --prefix)/opt/libpcap/bin:$PATH
export PKG_CONFIG_PATH=$(brew --prefix)/opt/openssl@1.1/lib/pkgconfig:$PKG_CONFIG_PATH
export PKG_CONFIG_PATH=$(brew --prefix)/opt/nss/lib/pkgconfig:$PKG_CONFIG_PATH
export PKG_CONFIG_PATH=$(brew --prefix)/opt/libpcap/lib/pkgconfig:$PKG_CONFIG_PATH

# compile
make
```
