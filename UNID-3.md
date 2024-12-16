# Session 11

Configurar IPs

Server

Dos cliente windows

Cliente1

Cliente2

### Actualizar la red local

```bash
vim /etc/resolv.conf

search fmcaceres.fmc
nameserver 8.8.8.8
```

### Instalar dependencias

```bash
yum install -y bind bind-utils
```

### Configurar dns

```bash
vim /etc/sysconfig/network

NETWORKING=YES
HOSTNAME=dns.fmcaceres.fmc
```

```bash
vim /etc/sysconfig/network

NETWORKING=YES
HOSTNAME=dns.fmcaceres.fmc
```

```bash
vim /etc/hosts

17.244.212.192  dns.fmcaceres.fmc dns
109.254.138.2 dns.fmcaceres.fmc dns
203.160.65.2  dns.fmcaceres.fmc dns
```

```ps1
vim /etc/NetworkManager/NetworkManager.conf

[]
dns=none
```

### Configurar hosts

```ps1
# Cambiar options
options {
  # Cambiar por ips propias de la WAN, LAN A y LAN B
  listen-on port 53 { 127.0.0.1; 17.244.212.192; 109.254.138.2; 203.160.65.2; };
  # Cambiar
  allow-query { any; };
}

# Inversas - Ejemplo
# 17.244.212.x => 212.244.17
# Colocar DNS y host donde correspanda

zone "fmcaceres.fmc" IN {
  type master;
  file "directa.fmcaceres";
  allow-update { none; };
}

# 17.244.212.x => 212.244.17
zone "212.244.17.in-addr.arpa" IN {
  type master;
  file "inservawan.fmcaceres";
  allow-update { none; };
}

# 109.254.138.x => 138.254.109
zone "138.254.109.in-addr.arpa" IN {
  type master;
  file "inversalana.fmcaceres";
  allow-update { none; };
}

# 203.160.65.x => 65.160.203
zone "65.160.203.in-addr.arpa" IN {
  type master;
  file "inversalanb.fmcaceres";
  allow-update { none; };
}
```

### Verificar los hosts son correctos

```bash
# Reinciar server
systemctl restart NetworkManger

# Verificar que esten correcto
named-checkconf /etc/named.conf
```

```bash
cd /var/named/

cp named.localhost directa.fmcaceres
cp named.loopback inversawan.fmcaceres
cp named.loopback inversalana.fmcaceres
cp named.loopback inversalanb.fmcaceres
```

### directa.fmcaceres

```ps1
# directa.fmcaceres

dns.fmcaceres.fmc. root.fmcaceres.fmc.
# 17.244.212.192; 109.254.138.2; 203.160.65.2;
# Como debería de estar
	IN	NS	dns.fmcaceres.fmc.
dns	IN	A	17.244.212.192;
dns	IN	A	109.254.138.2;
dns	IN	A	203.160.65.2;

www	IN	A	17.244.212.192;
www	IN	A	109.254.138.2;
www	IN	A	203.160.65.2;
ftp	IN	A	17.244.212.192;
ftp	IN	A	109.254.138.2;
ftp	IN	A	203.160.65.2;
smtp	IN	A	17.244.212.192;
smtp	IN	A	109.254.138.2;
smtp	IN	A	203.160.65.2;
pop	IN	A	17.244.212.192;
pop	IN	A	109.254.138.2;
pop	IN	A	203.160.65.2;
imap	IN	A	17.244.212.192;
imap	IN	A	109.254.138.2;
imap	IN	A	203.160.65.2;

	AAAA	::1
```

### inversawan.fmcaceres

```ps1
# inversalana.fmcaceres

# Antes de la segunda arroba primera linea
dns.fmcaceres.fmc. root.fmcaceres.fmc.

# Como debería de estar
@	IN	NS	dns.fmcaceres.fmc.
@	IN	PTR	fmcaceres.fmc.
dns	IN	A	 17.244.212.192;
www	IN	A	 17.244.212.192;
ftp	IN	A	 17.244.212.192;
smtp	IN	A	 17.244.212.192;
pop	IN	A	 17.244.212.192;
imap	IN	A	 17.244.212.192;

	AAAA	::1
192	IN	PTR	dns.fmcaceres.fmc.
192	IN	PTR	www.fmcaceres.fmc.
192	IN	PTR	ftp.fmcaceres.fmc.
192	IN	PTR	smtp.fmcaceres.fmc.
192	IN	PTR	pop.fmcaceres.fmc.
192	IN	PTR	imap.fmcaceres.fmc.
```

### inversalana.fmcaceres

```ps1
# inversalana.fmcaceres

# Antes de la segunda arroba primera linea
dns.fmcaceres.fmc. root.fmcaceres.fmc.

# Como debería de estar
@	IN	NS	dns.fmcaceres.fmc.
@	IN	PTR	fmcaceres.fmc.
dns	IN	A	109.254.138.2;
www	IN	A	109.254.138.2;
ftp	IN	A	109.254.138.2;
smtp	IN	A	109.254.138.2;
pop	IN	A	109.254.138.2;
imap	IN	A	109.254.138.2;

	AAAA	::1
2	IN	PTR	dns.fmcaceres.fmc.
2	IN	PTR	www.fmcaceres.fmc.
2	IN	PTR	ftp.fmcaceres.fmc.
2	IN	PTR	smtp.fmcaceres.fmc.
2	IN	PTR	pop.fmcaceres.fmc.
2	IN	PTR	imap.fmcaceres.fmc.
```

### inversalanb.fmcaceres

```ps1
# inversalanb.fmcaceres

# Antes de la segunda arroba primera linea
dns.fmcaceres.fmc. root.fmcaceres.fmc.

# Como debería de estar
@	IN	NS	dns.fmcaceres.fmc.
@	IN	PTR	fmcaceres.fmc.
dns	IN	A	203.160.65.2;
www	IN	A	203.160.65.2;
ftp	IN	A	203.160.65.2;
smtp	IN	A	203.160.65.2;
pop	IN	A	203.160.65.2;
imap	IN	A	203.160.65.2;

	AAAA	::1
2	IN	PTR	dns.fmcaceres.fmc.
2	IN	PTR	www.fmcaceres.fmc.
2	IN	PTR	ftp.fmcaceres.fmc.
2	IN	PTR	smtp.fmcaceres.fmc.
2	IN	PTR	pop.fmcaceres.fmc.
2	IN	PTR	imap.fmcaceres.fmc.
```

```bash
# Check zones
named-checkzone fmcaceres.fmc /var/named/directa.fmcaceres
named-checkzone fmcaceres.fmc /var/named/inversawan.fmcaceres
named-checkzone fmcaceres.fmc /var/named/inversalana.fmcaceres
named-checkzone fmcaceres.fmc /var/named/inversalanb.fmcaceres

chown named directa.fmcaceres
chown named inversawan.fmcaceres
chown named inversalana.fmcaceres
chown named inversalanb.fmcaceres

chgrp named directa.fmcaceres
chgrp named inversawan.fmcaceres
chgrp named inversalana.fmcaceres
chgrp named inversalanb.fmcaceres

# inicar servicio
systemctl enable named.service
systemctl start named.service
systemctl status named.service
```

### Notes

- Si named falla al iniciar el servicio, hacer correciones y volver a instarl bind bind-utils
