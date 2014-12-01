Practice 4: Secure protocols
============================

[Práctica original:] http://www.tic.udc.es/~nino/blog/lsi/prac-semi/lsi-practica4-rev-2012.pdf

# SSH
=====

SSH is a network protocol (Application layer) that allows to execute commands, exchange data, login and other network services between two machines.
How it works? 

    mymachine@machine-name:~$ ssh -v lsi@<HOST>
    OpenSSH_6.6.1, OpenSSL 1.0.1f 6 Jan 2014
    debug1: Reading configuration data /etc/ssh/ssh_config
    debug1: /etc/ssh/ssh_config line 19: Applying options for *
    debug1: Connecting to <HOST> [HOST] port 22.
    debug1: Connection established.
    debug1: identity file /home/---/.ssh/id_rsa type -1
    debug1: identity file /home/---/.ssh/id_rsa-cert type -1
    debug1: identity file /home/---/.ssh/id_dsa type -1
    debug1: identity file /home/---/.ssh/id_dsa-cert type -1
    debug1: identity file /home/---/.ssh/id_ecdsa type -1
    debug1: identity file /home/---/.ssh/id_ecdsa-cert type -1
    debug1: identity file /home/---/.ssh/id_ed25519 type -1
    debug1: identity file /home/---/.ssh/id_ed25519-cert type -1
    debug1: Enabling compatibility mode for protocol 2.0
    debug1: Local version string SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2
    debug1: Remote protocol version 2.0, remote software version OpenSSH_5.5p1 Debian-6+squeeze5
    debug1: match: OpenSSH_5.5p1 Debian-6+squeeze5 pat OpenSSH_5* compat 0x0c000000
    debug1: SSH2_MSG_KEXINIT sent
    debug1: SSH2_MSG_KEXINIT received
    debug1: kex: server->client aes128-ctr hmac-md5 none
    debug1: kex: client->server aes128-ctr hmac-md5 none
    debug1: SSH2_MSG_KEX_DH_GEX_REQUEST(1024<3072<8192) sent
    debug1: expecting SSH2_MSG_KEX_DH_GEX_GROUP
    debug1: SSH2_MSG_KEX_DH_GEX_INIT sent
    debug1: expecting SSH2_MSG_KEX_DH_GEX_REPLY
    debug1: Server host key: RSA e8:26:30:91:a3:08:...:e0:05:3e:c0:de:bc:ac
    debug1: Host 'HOST' is known and matches the RSA host key.
    debug1: Found key in /home/---/.ssh/known_hosts:2
    debug1: ssh_rsa_verify: signature correct
    debug1: SSH2_MSG_NEWKEYS sent
    debug1: expecting SSH2_MSG_NEWKEYS
    debug1: SSH2_MSG_NEWKEYS received
    debug1: Roaming not allowed by server
    debug1: SSH2_MSG_SERVICE_REQUEST sent
    debug1: SSH2_MSG_SERVICE_ACCEPT received
    debug1: Authentications that can continue: publickey,password
    debug1: Next authentication method: publickey
    debug1: Offering RSA public key: mail@mail.com
    debug1: Authentications that can continue: publickey,password
    debug1: Offering RSA public key: mail@mail.com
    debug1: Authentications that can continue: publickey,password
    debug1: Offering RSA public key: mail@mail.com
    debug1: Authentications that can continue: publickey,password
    debug1: Trying private key: /home/---/.ssh/id_rsa 
    debug1: Trying private key: /home/---/.ssh/id_dsa
    debug1: Trying private key: /home/---/.ssh/id_ecdsa
    debug1: Trying private key: /home/---/.ssh/id_edXXX
    debug1: Next authentication method: password
    lsi@<HOST>'s password: 
    debug1: Authentication succeeded (password).
    Authenticated to --- ([---]:22).
    debug1: channel 0: new [client-session]
    debug1: Requesting no-more-sessions@openssh.com
    debug1: Entering interactive session.
    debug1: Sending environment.
    debug1: Sending env LC_PAPER = es_ES.UTF-8
    debug1: Sending env LC_ADDRESS = es_ES.UTF-8
    debug1: Sending env LC_MONETARY = es_ES.UTF-8
    debug1: Sending env LC_NUMERIC = es_ES.UTF-8
    debug1: Sending env LC_TELEPHONE = es_ES.UTF-8
    debug1: Sending env LC_IDENTIFICATION = es_ES.UTF-8
    debug1: Sending env LANG = en_US.UTF-8
    debug1: Sending env LC_MEASUREMENT = es_ES.UTF-8
    debug1: Sending env LC_TIME = es_ES.UTF-8
    debug1: Sending env LC_NAME = es_ES.UTF-8
    Linux debian 2.6.32-5-amd64 #1 SMP Tue May 13 16:34:35 UTC 2014 x86_64
    
    The programs included with the Debian GNU/Linux system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.
    
    Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
    permitted by applicable law.
    No mail.
    Last login: --- from ---
    lsi@debian:~$

After establish a TCP connection on port 22, there are some key exchanges between server and client. Then server offers the fingerprint of its public key to client. If client connected before to the server, i.e., there is an entry on its known\_hosts file, the next step  will be the authentication, but if not, client has to accept that public key.
b) To transfer a file from a host (can be localhost or not) to another host (can be localhost or not), we can use scp. To tell scp which cipher algorithm to use, we have _-c_ option, and to check which cipher algorithms our ssh version supports, _ssh -Q cipher_. So:

    scp -c <cipher-algorithm> [[user1@]host1:][file] [[user2@]host2:][file]

In example:

    $ hwclock
    vie 28 nov 2014 20:18:20 CET  -0.312973 seconds
    $ scp -c aes256-ctr test-file lsi@<ip>:/home/lsi/test-folder
    ...
    lsi@debian:~$ ls -l /home/lsi/test-folder/
    total 0
    -rw-r--r-- 1 lsi lsi 0 nov 28 20:18 test-file

c) The authorized\_keys file on .ssh directory is used by sshd daemon to check if any of the given hosts' keys matches with any entry of this mentioned file.
So in the server, if allow _PubkeyAuthentication_ on _sshd\_config_ file and we add an entry on _authorized\_keys_ with a public key generated on our machine and saved on .ssh/, we will not need password to authenticate.

    $ keygen -t rsa
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/user/.ssh/id_rsa):     
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /home/user/.ssh/id_rsa.
    Your public key has been saved in /home/user/.ssh/id_rsa.pub.
    The key fingerprint is:
    2b:d0:b6:3a:34...:ea:10:9a:91:7c:93 user@machine
    The key's randomart image is:
    +--[ RSA 2048]----+
    |                 |
    |                 |
    |       .         |
    |..  ..           |
    |+. E..d S        |
    |.+. *o.. .       |
    |+  . so .        |
    | .  +.oo         |
    | .o..ooo.        |
    +-----------------+
    $ cat .ssh/id_rsa.pub 
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDZ75Yx0NFXQ+gqXLsr+bEBeIcjIsIvMsiN/HR3QdIiiphI0fK96Vz6mV3vpJZnOi28Ap/P9RxAXp/i+EMXOHAgiHMoMugcB75zAYoJ7D80Q5tfqxAqXqrLg57N1JivVInCKR5+11jUKFWL2QrfEArmICHaAI2+lAXwcVxFmFPVtw7WVzblF28DOLS4hxokCG24szJHk6rssAjGG5J0VcFIGf5ZDZHn38N4WYfYogOLwM6IwPK58zwNRJXWKd7vGn8lZGHasSgJcaqT3KQYA3UKYKGFJeoGIcRJwtunrEpAUz7but1eB1e3OGta2htlx8aLf4XEbUU3lfW5WKoqqKfj markoshorro@markoshorro-M17xR4

Following this steps, on next login, the output with _-vvv_, on some part should be something like this:

    ...
    debug1: Offering RSA public key: user@machine
    debug3: send_pubkey_test
    debug2: we sent a publickey packet, wait for reply
    debug1: Server accepts key: pkalg ssh-rsa blen 279
    debug2: input_userauth_pk_ok: fp ca:79:ed:95:...:e7:50:44:45:00:d6:f2:9e:c1
    debug3: sign_and_send_pubkey: RSA ca:79:ed:95:...:e7:50:44:45:00:d6:f2:9e:c1
    debug1: Authentication succeeded (publickey).
    Authenticated to IP ([IP]:22).
    ...

d) If we want to securize a protocol that it is not, we can make a SSH Tunnel with it. So what we are doing is redirect all this traffic through an SSH connection, tunneling.
-L option specifies local port forwarding. For the duration of the SSH session, pointing your browser at http://localhost:8080/ would send you to http://www.ubuntuforums.org/

    $ ssh -L 8080:www.canonical.com:80 -f <host> -Nv

Where _host_ is your own machine, _-N_ executes none commands and _-v_ shows some information about the tunnel created.

e) Going further, on _sshd\_config_ you can add _AllowUsers_ to limit access to specific IP addresses for specific users, e.g.:

    AllowUsers root@10.10.102.172 lsi@10.*

In this case, _root_ could only log in over 10.10.102.172 IP and lsi from any 10.X.X.X IP

# Certification Authority (CA)
==============================

Firstable we are going to configure a Certification Authority (CA) in our system. We are going to use GnuTLS instead of OpenSSL, because using GnuTLS avoids the licensing issues that can arise from employing the more common OpenSSL package.
So, we start the process creating setting up a CA:

    $ certtool --generate-privkey --outfile ca-key.pem
    $ certtool --generate-self-signed --load-privkey ca-key.pem --outfile ca-cert.pem

The _ca-keyname_ will be used to sign the certificates we want.
Now, if we want to create our certificate, we first generate our private key to certificate and then we create a request to be signed:

    $ certtool --generate-privkey --outfile key.pem
    $ certtool --generate-request --load-privkey key.pem --outfile request.pem
    Generating a PKCS #10 certificate request...
    Country name (2 chars): es
    Organization name: markoshorro
    Organizational unit name: mk
    Locality name: house
    State or province name: house
    Common name: markoshorro
    UID: 11
    Enter a dnsName of the subject of the certificate: 
    Enter the IP address of the subject of the certificate: 
    Enter the e-mail of the subject of the certificate: 
    Enter a challenge password: 
    Does the certificate belong to an authority? (y/N): n
    Will the certificate be used for signing (DHE and RSA-EXPORT ciphersuites)? (y/N): n
    Will the certificate be used for encryption (RSA ciphersuites)? (y/N): y
    Is this a TLS web client certificate? (y/N): y
    Is this also a TLS web server certificate? (y/N): y

Then we can sign it with the CA certificate:

    $ certtool --generate-certificate --load-request request.pem \
               --outfile cert.pem --load-ca-certificate ca-cert.pem \
               --load-ca-privkey ca-key.pem

Of course, the CA should keep carefully its private key to mantain authenticity.
After all, _cert.pem_ is the file that certificates, by the CA (in this case, ourselves), that is me who emits that.

## Apache2 and SSL
==================

We are meant to configure Apache so if we try to access to a concrete directory of the web hierarchy, we must use SSL and we authenticated.
Firstable, and after setting up Apache (_apt-get install apache2..._), we create the mentioned directory and add a html file.
Now, on any part of _/etc/apache2/apache2.conf_ file we paste a directive like this:

    <Directory <path_to_secret_directory>>
         SSLRequireSSL
    </Directory>

In example, my html pages are on _/var/www/_ and my private directory on _/var/www/private_, so:

    <Directory /var/www/private>
         SSLRequireSSL
    </Directory>

Restarting Apache service, you should notice that if you try to access from browser to _http://localhost/<whatever>/index.html_ fails, but with _https_ it does not.

# openVPN
=========

Basically, VPN extends a private network through Internet.
A simple way to create a tunnel with openVPN is to configurate a server endpoint and a client endpoint.
Server (for the record, we saved this file on _/etc/openvpn/server.conf_):

    # Server configuration
    dev tun0
    ifconfig 10.8.0.1 10.8.0.2
    secret sekret.key

Client (for the record, we saved this file on _/etc/openvpn/client.conf_):

    # Client configuration
    remote <ip\_server>
    dev tun0
    ifconfig 10.8.0.2 10.8.0.1
    secret sekret.key

Where _sekret.key_ is generated before the connection by: _openvpn --genkey --secret sekret.key_.
Once machines are configurated, they only have to run their configurations:

    $ openvpn --config <path_to_config_file>
    Mon Dec  1 19:06:31 2014 OpenVPN 2.1.3 x86_64-pc-linux-gnu [SSL] [LZO2] [EPOLL] [PKCS11] [MH] [PF_INET6] [eurephia] built on Jun  4 2013
    Mon Dec  1 19:06:31 2014 IMPORTANT: OpenVPN's default port number is now 1194, based on an official port number assignment by IANA.  OpenVPN 2.0-beta16 and earlier used 5000 as the default port.
    Mon Dec  1 19:06:31 2014 NOTE: OpenVPN 2.1 requires '--script-security 2' or higher to call user-defined scripts or executables
    Mon Dec  1 19:06:31 2014 /usr/sbin/openvpn-vulnkey -q /etc/openvpn/private/braismarkos.key
    Mon Dec  1 19:06:31 2014 TUN/TAP device tun0 opened
    Mon Dec  1 19:06:31 2014 /sbin/ifconfig tun0 10.8.0.1 pointopoint 10.8.0.2 mtu 1500
    Mon Dec  1 19:06:31 2014 UDPv4 link local (bound): [undef]
    Mon Dec  1 19:06:31 2014 UDPv4 link remote: [undef]
    Mon Dec  1 19:08:35 2014 Peer Connection Initiated with [AF_INET]<THE_CLIENT_REAL_IP>:1194
    Mon Dec  1 19:08:36 2014 Initialization Sequence Completed

This should work. You can see the interfaces configurations (_ifconfig_):

          ...
          tun0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  
                    inet addr:10.8.0.1  P-t-P:10.8.0.2  Mask:255.255.255.255
                    UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500  Metric:1
                    RX packets:0 errors:0 dropped:0 overruns:0 frame:0
                    TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
                    collisions:0 txqueuelen:100 
                    RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
          ...

Testing ping:

    root@debian:/home/lsi# ping 10.8.0.2
    PING 10.8.0.2 (10.8.0.2) 56(84) bytes of data.
    64 bytes from 10.8.0.2: icmp_req=1 ttl=64 time=0.665 ms
    64 bytes from 10.8.0.2: icmp_req=2 ttl=64 time=0.584 ms
    64 bytes from 10.8.0.2: icmp_req=3 ttl=64 time=0.521 ms
    

References
==========
GnuTLS - http://www.gnutls.org/manual/html_node/certtool-Invocation.html, https://help.ubuntu.com/community/GnuTLS
SSH - http://unixhelp.ed.ac.uk/CGI/man-cgi?ssh+1
openVPN - http://openvpn.net/index.php/open-source/documentation/miscellaneous/78-static-key-mini-howto.html







