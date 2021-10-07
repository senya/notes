# Forward gpg keys by ssh

https://superuser.com/a/1329299/409543 :

In new versions of GnuPG or Linux distributions the paths of the sockets can change. These can be found out via

```
$ gpgconf --list-dirs agent-extra-socket
```

and

```
$ gpgconf --list-dirs agent-socket
```

Then add these paths to your SSH configuration:

```
Host remote
  RemoteForward <remote socket> <local socket>
```

Quick solution for copying the public keys:

```
scp .gnupg/pubring.kbx remote:~/.gnupg/
```

On the remote machine, activate GPG agent:

```
echo use-agent >> ~/.gnupg/gpg.conf
```

On the remote machine, also modify the SSH server configuration and add this parameter (/etc/ssh/sshd_config):

```
StreamLocalBindUnlink yes
```

Restart SSH server, reconnect to the remote machine - then it should work.
