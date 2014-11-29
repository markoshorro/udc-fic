Practice 4: Secure protocols
============================

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

c) The authorized\_keys file on .ssh directory is used by sshd daemon to check if any of the hosts' given keys matches with any entry of this mentioned file.
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







