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
vim /etc/NetworkManager/NetworkManager.conf

NETWORKING=YES
HOSTNAME=dns.fmcaceres.fmc
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
zone "212.244.17".in-addr.arpa IN {
  type master;
  file "inservawan.fmcaceres";
  allow-update { none; };
}

# 109.254.138.x => 138.254.109
zone "138.254.109".in-addr.arpa IN {
  type master;
  file "inversalana.fmcaceres";
  allow-update { none; };
}

# 203.160.65.x => 65.160.203
zone "65.160.203".in-addr.arpa IN {
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

# directa.fmcaceres

directa.fmcaceres

```ps1
dns.sanrodale.sra. root.sanrodale.sra. {
63.111.156.128; 135.48.173.0; 83.111.199.0;
# Como debería de estar
	IN	NS	dns.sanrodale.sra.
dns	IN	A	63.111.156.128
dns	IN	A	135.48.173.0
dns	IN	A	83.111.199.0

www	IN	A	63.111.156.128
www	IN	A	135.48.173.0
www	IN	A	83.111.199.0
ftp	IN	A	63.111.156.128
ftp	IN	A	135.48.173.0
ftp	IN	A	83.111.199.0
smtp	IN	A	63.111.156.128
smtp	IN	A	135.48.173.0
smtp	IN	A	83.111.199.0
pop	IN	A	63.111.156.128
pop	IN	A	135.48.173.0
pop	IN	A	83.111.199.0
imap	IN	A	63.111.156.128
imap	IN	A	135.48.173.0
imap	IN	A	83.111.199.0

	AAAA	::1
```
