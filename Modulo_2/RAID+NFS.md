# На HQ-SRV

```
sudo mdadm --create /dev/md0 -l 0 -n 2 /dev/sdb /dev/sdc
sudo mkfs.ext4 /dev/md0
sudo mkdir -p /raid
sudo mount /dev/md0 /raid
echo "/dev/md0 /raid ext4 defaults 0 0" >> /etc/fstab
sudo mkdir -p /raid/nfs
sudo chmod -R 777 /raid/nfs
echo "/raid/nfs       192.168.200.0/28(rw,no_root_squash)" >> /etc/exports
sudo systemctl enable --now nfs-server.service
sudo exportfs -arv
```

------------------------------------------------------------------------------------------
# На HQ-CLI

Обязательно отдельно:
```
sudo -i
```

```
sudo mkdir /mnt/nfs
sudo chmod –R 777 /mnt/nfs
echo "192.168.100.2:/raid/nfs        /mnt/nfs    nfs     auto    0 0" >> /etc/fstab
sudo mount -av
sudo df -h
touch /mnt/nfs/test.txt
```