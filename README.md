# haha_cache_go_brrr

a very silly, very shitty little DNS cache warmup tool born of sleep deprivation, far too much coffee, and a poor moral compass


## Description

haha_cache_go_brrr is a cache preloading tool designed for populating the initial and prefetch caches of a recursive resolver such as [Unbound DNS](https://www.nlnetlabs.nl/projects/unbound/about/) which backs a DNS filter or proxy such as [dnsproxy](https://github.com/AdguardTeam/dnsproxy) or [Pi-hole](https://pi-hole.net/)

Under no circumstances should haha_cache_go_brrr  be run against Pi-hole (Pi-hole deliberately maintains a very short min-ttl and performs no cache prefetching), nor should it be run against any resolver endpoint that does not cache, any ISP/third party upstream DNS providers, or in fact any DNS endpoint that you do not control.
This tool has been designed to support the Unbound DNS resolver by supplying cache pressure and prefetch rules when unbound is functioning as a caching recursive resolver with [large buffers](https://github.com/saint-lascivious/unbound-config/blob/master/use-large-buffers.conf), [cache slabs](https://github.com/saint-lascivious/unbound-config/blob/master/use-optimized-caches.conf), a high [cache-min-ttl](https://github.com/saint-lascivious/unbound-config/blob/master/use-cache-min-ttl.conf), [multiple threads](https://github.com/saint-lascivious/unbound-config/blob/master/use-optimized-threads.conf), [multi-threaded udp](https://github.com/saint-lascivious/unbound-config/blob/master/use-multithreaded-udp.conf) and [libevent](https://github.com/saint-lascivious/unbound-config/blob/master/use-libevent.conf) if the server is expected to process very large amounts of queries.

Note: If you intend to receive a recursive reply from an Unbound DNS server located anywhere other than localhost you may need to adjust your [access-control](https://github.com/saint-lascivious/unbound-config/blob/master/use-access-control.conf) definitions.


## Features
* Set the total domains queried

Users have the ability to set the total number of domains parsed out from the top domains CSV.

```
default: 500
```

* Set the resolver address and port

Set a custom resolver address (if not running on this machine) and custom port.
Uses localhost and Unbound DNS default port by default.

PLEASE ENSURE YOU CONTROL THESE ENDPOINTS

```
default: resolver_address="127.0.0.1"
default: resolver_port="5335"
```

* Set the flags used to form the dig queries

More information on the dig command structure can be found [here](https://linux.die.net/man/1/dig).
```
default: dig_flags="+short +time=10 +tries=1"
```

* Set your own top domains CSV URL and choose a domain column within it

Provide your own top domains CSV with the ability to set which column is used as each top domain list isn't guaranteed to have the domain in the same CSV column.
Uses the Majestic Million top domain list.

```
default: domain_list_url="https://downloads.majestic.com/majestic_million.csv"
default: csv_column="3"
```

* Query an additional custom domain list

Users can provide an addition list of domains, one per line, in the user created /etc/haha_cache_go_brrr/custom_domains file.
If present this list is parsed and queried after the top N domains.


* Parallel queries

Ability to optionally split the master dig command list into four and run using gnu parallel.
Set use_parallel="yes" to enable.

```
default: use_parallel=""
```

* Runs as a service with service timer

The systemd service timer approach ensures that haha_cache_go_brrr runs ten minutes after boot to give the system plenty of time to come up before applying cache pressure.


## Usage
* Install dependencies
[dns-utils](https://packages.debian.org/buster/dns-utils)
[parallel](https://packages.debian.org/buster/parallel)
```
sudo apt-get install dns-utils parallel
```

* Download haha_cache_go_brrr
```
cd /usr/local/bin/
sudo wget https://raw.githubusercontent.com/saint-lascivious/haha_cache_go_brrr/main/haha_cache_go_brrr
chmod +x /usr/local/bin/haha_cache_go_brrr
```
* Download haha_cache_go_brrr service files:
```
cd /etc/systemd/system/
sudo wget https://raw.githubusercontent.com/saint-lascivious/haha_cache_go_brrr/main/haha_cache_go_brrr.service
sudo wget https://raw.githubusercontent.com/saint-lascivious/haha_cache_go_brrr/main/haha_cache_go_brrr.timer
```
* Start the haha_cache_go_brrr service
```
sudo systemctl enable haha_cache_go_brrr.timer
sudo systemctl start haha_cache_go_brrr.timer
```

## To-Do
* Nothing right now.

Suggestions?


## Uninstall
```
sudo systemctl stop haha_cache_go_brr.timer
sudo systemctl disable haha_cache_go_brr.timer
sudo rm /etc/systemd/system/haha_cache_go_brrr.service
sudo rm /etc/systemd/system/haha_cache_go_brrr.timer
sudo rm -rf /etc/haha_cache_go_brr
sudo rm /usr/local/bin/haha_cache_go_brrr
```

## Contact
* Discord
[SaintLascivious](https://discord.gg/9Cq4gRg)

* Email
saint.lascivious@gmail.com

* IRC
[##saint-lascivious](https://webchat.freenode.net/##saint-lascivious)

* Reddit
[saint-lascivious](https://www.reddit.com/user/saint-lascivious)

![alt text][logo]

[logo]:https://vignette.wikia.nocookie.net/pokemon/images/7/76/265Wurmple.png "Using the spikes on its rear end, Wurmple peels the bark off trees and feeds on the sap that oozes out. This Pokémon's feet are tipped with suction pads that allow it to cling to glass without slipping."
