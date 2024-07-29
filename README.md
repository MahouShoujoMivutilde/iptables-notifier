# iptables-notifier

Sends notifications to your phone when iptables `LOG` events happen. Meant to run on my DIY router.

A minimalist way to know if something unexpected is phoning home.

#### Example

After the setup, `ping archlinux.org` from a monitored device will result in this being sent:

<details><summary>Notification's content</summary>
<p>

```
Jul 29 19:25:22 router kernel: Look! A sus device phoning home
IN=lan0
OUT=wan0
MAC=xxxxxxxx device's mac xxxxxxxx
SRC=10.0.0.7 name: "wifi.test."
DST=95.217.163.246 name: "archlinux.org."
LEN=84
TOS=0x00
PREC=0x00
TTL=63
ID=0
DF
PROTO=ICMP
TYPE=8
CODE=0
ID=65386
SEQ=0
```

</p>
</details>

Of course, it works for way more things than just pings.

### Requires

* [dog](https://github.com/ogham/dog/) - for reverse dns lookup of SRC and DST addresses
* Something that can send notifications - e.g. [apprise](https://github.com/caronc/apprise) (has everything) or you could lookup how use `curl` to send notification for a given service
* systemd-journald - for iptables' events
* awk, bash

###  Setup

#### Iptables on the router

```
# a sus device with a static ip (per your DHCP server config), 10.0.0.7
-A FORWARD -i lan0 -s 10.0.0.7 -j LOG --log-level 6 --log-prefix "Look! A sus device phoning home "

# # could also block right after logging
# -A FORWARD -i lan0 -s 10.0.0.7 -j REJECT
```

#### The script

```
git clone 'https://github.com/MahouShoujoMivutilde/iptables-notifier'
cd iptables-notifier
```

Go to the end of the `handle()` function and change `noti-bot` for whatever you'll be using to deliver notifications.


```bash
sudo cp iptables-notifier /usr/local/bin
```

#### The service

```bash
sudo cp iptables-notifier.service /etc/systemd/system
```

Don't forget to

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now iptables-notifier.service
```
