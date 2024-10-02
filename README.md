# dnscrypt-proxy-guide: Guide for dnscrypt-proxy on Proxmox LXC with Pi-hole

## Disclaimer

This is a guide on how to set up [dnscrypt-proxy](https://github.com/DNSCrypt/dnscrypt-proxy) on a [Proxmox](https://www.proxmox.com/en/) LXC with [Pi-hole](https://pi-hole.net/).

You should definitely familiarise yourself with what exactly happens here beforehand.

This is intended to help you set it up, but is not a general solution. (But a possible one)

## Important Links

[GitHub `dnscrypt-proxy`](https://github.com/DNSCrypt/dnscrypt-proxy)

[Codeberg `dnscrypt-proxy-config`](https://codeberg.org/DecaTec/dnscrypt-proxy-config)

[Documentation (German)](https://decatec.de/home-server/anonyme-dns-kommunikation-mit-pi-hole-und-dnscrypt-proxy/)

## Setup

### 0. DNS Check

[dnscheck.tools](https://www.dnscheck.tools/)

### 1. Proxmox Host

```shell
pct enter 100
```

`100` -> ID for Proxmox LXC with Pi-hole

### 2. Proxmox LXC with Pi-hole

> Look at `dnscrypt-proxy` to see which release is the right one for you.

```shell
cd /opt

ls

wget https://github.com/DNSCrypt/dnscrypt-proxy/releases/download/2.1.5/dnscrypt-proxy-linux_x86_64-2.1.5.tar.gz

tar xvf dnscrypt-proxy-linux_x86_64-2.1.5.tar.gz

rm *.gz

mv linux-x86_64/ dnscrypt-proxy

cd dnscrypt-proxy/

ls

wget https://codeberg.org/DecaTec/dnscrypt-proxy-config/raw/branch/master/dnscrypt-proxy.toml

./dnscrypt-proxy -service install

./dnscrypt-proxy -service start

systemctl status dnscrypt-proxy

nano *.toml

systemctl restart dnscrypt-proxy
```

In the `*.toml` change the following flags:

```toml
dnscrypt_servers = false
```

```toml
doh_servers = false
```

Uncomment the following lines:

```toml
log_file = 'dnscrypt-proxy.log'
```

```toml
log_file_latest = true
```

### 3. In Pi-hole (Admin Page):

-> Upstream DNS Servers

Custom 1 (IPv4): `127.0.0.1#65054`

Custom 3 (IPv6): `::1#65054`

### 4. DNS Check

[dnscheck.tools](https://www.dnscheck.tools/)

## Friendly reminder

The odoh-servers may not always be available. This can lead to malfunctions and trigger a warning. This should be monitored.

A warning may then appear in Pi-hole that too many retries have been made.

Just take a look at the logs and take action if necessary:

```shell
cat /opt/dnscrypt-proxy/dnscrypt-proxy.log 
```

If it bothers you, set the following to `true`:

```toml
doh_servers = true
```

If necessary, that too:

```toml
dnscrypt_servers = true
```
