# SSH

## Generating A Key Pair

SSH is used to securely communicate with remove servers.

## Intro to Keys

Bob, Alice, etc.

## Why Passwords Fail

Passwords are bad, mkay

## Selecting a strong passphrase

 * xkcd cartoon
 * entropy
 * long sentences

## SSH Commands

The main ssh command utilities, provided by the OpenSSH package, are:

   * `ssh` - main connection client
   * `ssh-agent` - stores your key information locally so that you don't have to re-enter passwords
   * `ssh-add` - adds keys to ssh-agent
   * `ssh-keygen` - generates new keys

## Configure SSH on Mac OS X

If you have never previously used ssh there will be no configuration information or keys available. To find out if there is an existing ssh configuration, or existing user authentication keys, look for a folder called `.ssh` in the home directory

    $ ls -l .ssh

The response will be either:

    ls: .s: No such file or directory

or a listing

    config  id_rsa  id_rsa.pub  known_hosts

If the directory does not exist, create it

    $ mkdir .ssh

make sure the directory has proper permission set to it, so that other users on the machine are not able to read your configuration details or key information

    $ chmod 700 .ssh

Next we will generate a new key pair, which we will use to connect securely and without passwords. The command used to generate new keys is `ssh-keygen`.

The man page for ssh-keygen will list a very long line of options. At the moment we are only interested in two - the `t` flag to set the type of key generated and the `C` flag to set the comment, which we will use to identify the key by email address.

The type of key we will generate is RSA.

    $ ssh-keygen -t rsa -C "dave@dave.com"
    Generating public/private rsa key pair.
    Enter file in which to save the key (/Users/dave/.ssh/id_rsa): 
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /Users/dave/.ssh/id_rsa.
    Your public key has been saved in /Users/dave/.ssh/id_rsa.pub.
    The key fingerprint is:
    92:b7:d6:08:7e:a9:db:a1:e2:14:3a:77:a8:bb:b4:31 dave@dave.com
    The key's randomart image is:
    +--[ RSA 2048]----+
    |                 |
    |                 |
    |                 |
    |       .         |
    |    . + S        |
    |   . + + =       |
    |  E + o B .      |
    | . O.. * .       |
    |  =+..+..        |
    +-----------------+

Congratulations, you have now generated a key pair. The files created from the process are saved in the `.ssh` folder. By default the private key is saved as `id_rsa` and the public key is saved as `id_rsa.pub`

Next we will change the permission on these files so that they can not be accessed by other users on your system.

All files should be set to only be readable by the owners, so `600`. You can optionally allow public keys to be readable by anybody, but we will leave it as readable by the owner only for now.

    $ cd .ssh
    $ chmod 600 id_rsa
    $ chmod 600 id_rsa.pub

If you run a full directory listing you can check the permission for each file

    $ ls -l
    total 720
    -rw-------  1 nik  staff     933 Dec  6 01:00 config
    -rw-------  1 nik  staff    1766 Dec  6 01:30 id_rsa
    -rw-------  1 nik  staff     395 Dec  6 01:30 id_rsa.pub
    -rw-------  1 nik  staff   11378 Dec  5 03:01 known_hosts

## Backing up keys

Now would be a good time to save a backup of the keys. If you ever lose them or delete them you will not be able to access resources that are locked down to only allow users authenticating with this key to access.

The best way to backup the keys would be to copy both the private key and the public key to a USB thumb drive, and to keep the drive in a secure place.

Do not make a copy of your key and store it online in an online document store such as Google Docs. It would leave your servers vulnerable to an attack on those systems and your password, defeating the purpose of using public keys to secure access to your servers.

## Setting Permissions

    $ find ~/.ssh -type f -print | xargs -n1 chmod 600

## Extended Attributes

 $ xattr -d com.apple.quarantine aws-syd.pem

## Getting the fingerprint of a key

    $ ssh-keygen -lf ~/.ssh/id_rsa_nikcub_gmail
    2048 43:fc:52:38:de:f9:d7:f4:03:59:08:0f:c2:8a:db:c6  nikcub@gmail.com (RSA)

If you want to aliast this command as `ssh-kp`

    $ alias 

## Debugging an ssh server connection

    $ ssh -vT ec2-user@server-string.amazonaws.com
    OpenSSH_5.9p1, OpenSSL 0.9.8r 8 Feb 2011
    debug1: Reading configuration data /Users/nik/.ssh/config
    debug1: /Users/nik/.ssh/config line 23: Applying options for github.com
    debug1: /Users/nik/.ssh/config line 44: Applying options for *
    debug1: Reading configuration data /etc/ssh_config
    [.. snip ..]

## Setting up ssh-agent

You are now using keys to access your servers, and you have also chosen a good strong passphrase, but you find having to always re-enter the passphrase each time you connect to a server a bit of a pain.

Thankfully the OpenSSH suite comes with an application that runs in the background and saves decrypted version of your private keys and sends them to the ssh client each time it makes a connection to a server.

The application is called ssh-agent 

It needs to be running 

## Listing keys stored in ssh-agent

    $ ssh-agent -l

## Configuring the SSH client

Your ssh client will read options each time it runs from a configuration file located in the `.ssh` directory.

Example configuration file

## Changing the password on a key

    $ ssh-keygen -p -f ~/.ssh/id_rsa_nikcub

`f` flag specifies which key file to act on. ~/.ssh/id_rsa is the default key and the default location, so if you are only working with that one single key then there is no need to enter or specify the filename

To change the comment on a key, similarly

    $ ssh-keygen -c -f ~/.ssh/id_rsa_dave

