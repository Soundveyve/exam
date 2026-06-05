# На всех Linux'ах, кроме HQ-CLI

```
sudo useradd -u 2026 sshuser
sudo passwd sshuser
```

sudo EDITOR=nano visudo
```
sshuser        ALL=(ALL)        NOPASSWD: ALL
```

/etc/ssh/sshd_config
```
AllowUsers sshuser
Port 2026
MaxAuthTries 2
Banner /etc/ssh/ssh_banner
```

Потом в файлике `/etc/ssh/ssh_banner` написать приветственное сообщение и выполнить строго в такой последовательности:
```
sudo setenforce 0
sudo systemctl restart sshd.service
```

# Для Eltex'ов

```
configure
	username net_admin
		password P@ssw0rd
		privilage 15
		ssh auth method password
```

