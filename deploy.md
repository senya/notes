# Change root password in the image
```
virt-customize -a jammy-server-cloudimg-amd64.img --root-password password:123 --uninstall cloud-init
```

# Resize vm image
```
virt-filesystems --long -h --all -a olddisk
qemu-img create -f qcow2 -o preallocation=metadata newdisk.qcow2 15G
virt-resize --expand /dev/sda2 olddisk newdisk.qcow2
```

Если после этого попадаем в grub-rescue, то там восстанавливаемся примерно так:
```
ls   # видим что есть
set  # видим неправильные переменные
set VAR=VALUE  # выправляем переменные
insmod ext2
insmod normal
normal
```

Загрузившись в гостя зовем (по крайней мере для debian11):
```
grub-install /dev/sda
```


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
