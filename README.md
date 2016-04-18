Login Proxy Service
===================

It is sometimes the case that you would like to connect to a server on a
remote machine, but would like to put some authentication and encryption
in front of the server.

This package is designed to make that easy by using `ssh` for the
authentication and encryption. The setup is basically this:

1. Start the server listening only on localhost.

2. Create a user that has, as its shell, a simple proxy that connects to
the above server, redirecting stdin to the server the server's output to
stdout. The `setup-proxy-user` script helps with this. Note that this
configuration does not necessarily mean the user will be able to access
nothing but the above server; see the "Security Notes" section below for
details.

3. Use the `connect-proxy-user` script on the remote machine to listen
on a local port and proxy connections from that port to the server
above.


Dependencies
------------

The remote end running the server depend on having netcat (`nc`)
available. The timeout parameter supplied assumes the [nmap version of
netcat](https://nmap.org/ncat/), but by removing this or tweaking it
appropriately, other flavours of netcat should work as well.

The local end requires [socat](http://www.dest-unreach.org/socat/).


Testing
-------

For testing I provide a simple server called "hexdump" which simply
echos back a hexdump of the input you give it. You can copy the provided
files to `/etc/systemd/system` and run `systemctl hexdump.socket start`
in order to start the server.


Security Notes
--------------

Note that, while we don't let the user run arbitrary commands, this
does not completely restrict their access to only the port to which
the proxy connects. If `sshd`'s TCP port forwarding is enabled
(`AllowTcpForwarding yes`), users will be able to connect to arbitrary
ports on the local host, and other things such as X11 forwarding may
also provide a certain amount of access.

It may be worthwhile to run a fairly "open" sshd for administrative
users that does not allow logins from other users, and for other users
run an sshd on a separate port that is thoroughly locked down.
