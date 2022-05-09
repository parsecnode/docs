# How to run Parsec node as Linux service

On this page you will find description how to run parsecd with JSON PRC as Linux service that will restart itself if daemon crashes for some reason. This example is for Ubuntu server 20.04 x64, but it can be applied to any of the linux versions with small changes.

## Create Linux service to start parsecd

1. To start service we will use user _parsec_, so lets create it, manage permissions and login:

```
useradd -m -s /bin/bash -G adm,systemd-journal,sudo parsec && passwd parsec
groupadd parsec
usermod -a -G parsec parsec
su parsec
```

2. Create directory _/PARSEC/_ in home directory of _parsec_ user:

```
cd ~
mkdir PARSEC
cd PARSEC
```

3. Download latest Linux version of Parsec and unpack it:

This one is for Linux 20.04, if you have other version, find a corresponding release at https://github.com/parsecnode/parsec/releases/

```
wget https://github.com/parsecnode/parsec/releases/download/v.1.7.6/Parsec-cli-ubuntu20.04-v.1.7.6.tar.gz
tar -xvzf Parsec-cli-ubuntu20.04-v.1.7.6.tar.gz
rm Parsec-cli-ubuntu20.04-v.1.7.6.tar.gz
```

4. Create log file and add permision to write it:

```
sudo touch /var/log/parsecd
sudo chgrp -R parsec /var/log/parsecd
sudo chmod -R 770 /var/log/parsecd
```

5. Optionally you can pre-download blockchain bootstrap to speed-up process:

```
cd PARSEC
mkdir .parsec
cd .parsec
wget https://bootstrap.parsecnode.com/blockchain-$(date "+%Y-%m-%d").tar.gz
tar -xvzf blockchain-$(date "+%Y-%m-%d").tar.gz
rm -f blockchain-$(date "+%Y-%m-%d").tar.gz
```

Exit to `root`
```
exit
```

6. Add Parsec to firewall:

```
apt-get install ufw -y
ufw default allow outgoing
ufw default deny incoming
ufw allow ssh/tcp
ufw limit ssh/tcp
ufw allow http/tcp
ufw allow https/tcp
ufw allow 32616/tcp comment "PARSEC"
ufw logging on
ufw -f enable
systemctl enable ufw
ufw status
```

Retun to `parsec`
```
su parsec
cd ~
```

7. Lets check if everything is ok. Try to run daemon with _parsec_ user permission and wait for SYNCHRONIZED OK.

Do not forget to change address to your wallet!
```
sudo -u parsec PARSEC/parsecd --data-dir=PARSEC/.parsec --log-file=/var/log/parsecd --restricted-rpc --enable-cors=* --enable-blockchain-indexes --rpc-bind-ip=0.0.0.0 --rpc-bind-port=32616 --fee-address=PARSkAmcBG8ZWqgfCu65fddB1yxzbTpGF6wzyLBsTUmoJUd1WmB8Bgj9h9LGbmVQEEEYAFHVJCWCVBEeEfsbK5ay6tSDmDup7i --fee-amount=0.1
```
After that, stop it via entering `exit` inside daemon session.

If you facing errors, you could run Parsec node with debug:
```
sudo -u parsec PARSEC/parsecd --data-dir=PARSEC/.parsec --log-file=/var/log/parsecd --restricted-rpc --enable-cors=*  --enable-blockchain-indexes --rpc-bind-ip=0.0.0.0 --rpc-bind-port=32616 --log-level=4 --fee-address=PARSkAmcBG8ZWqgfCu65fddB1yxzbTpGF6wzyLBsTUmoJUd1WmB8Bgj9h9LGbmVQEEEYAFHVJCWCVBEeEfsbK5ay6tSDmDup7i --fee-amount=0.1
```

Exit to `root`
```
exit
```

8. To autostart _parsecd_ daemon, we need to create service file in _/etc/systemd/system_

```
sudo nano /etc/systemd/system/parsecd.service
```
with such content:
```
[Unit]
Description=parsecd
Documentation=http://parsecnode.com
After=syslog.target

[Service]
User=parsec
Restart=always
RestartSec=10
ExecStart=/home/parsec/PARSEC/parsecd --data-dir=/home/parsec/PARSEC/.parsec --log-file=/var/log/parsecd --restricted-rpc --enable-cors=*  --enable-blockchain-indexes --rpc-bind-ip=0.0.0.0 --rpc-bind-port=32616 --fee-address=PARSkAmcBG8ZWqgfCu65fddB1yxzbTpGF6wzyLBsTUmoJUd1WmB8Bgj9h9LGbmVQEEEYAFHVJCWCVBEeEfsbK5ay6tSDmDup7i --fee-amount=0.1
StandardInput=tty-force
TTYVHangup=yes
TTYPath=/dev/tty20
#TTYReset=yes
#RemainAfterExit=false
SuccessExitStatus=143

[Install]
WantedBy=multi-user.target
```

**Do not forget to change address to your wallet!**

9. Run service:

```
systemctl daemon-reload
systemctl enable parsecd.service
systemctl start parsecd.service
```

10. Check service status:

```
sudo systemctl status parsecd.service
```
You should see seomething like this:
```
● parsecd.service - parsecd
   Loaded: loaded (/etc/systemd/system/parsecd.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2020-10-17 19:24:47 CEST; 5s ago
     Docs: http://parsecnode.com
 Main PID: 5368 (parsecd)
    Tasks: 1 (limit: 4915)
   CGroup: /system.slice/parsecd.service
           └─5368 /home/parsec/PARSEC/parsecd --data-dir=/home/parsec/PARSEC/.parsec --log-file=/var/log/parsecd --restricted-rpc --
Oct 17 19:24:47 parsecnode.top systemd[1]: Started parsecd.
lines 1-10/10 (END)
```

And check your Parsec masternode from browser (don't forget to change server url to yours):
- http://parsecnodes.top:32616/feeaddress
- http://parsecnodes.top:32616/getinfo

You should see something like
```
{
  "fee_address": "PARSkAmcBG8ZWqgfCu65fddB1yxzbTpGF6wzyLBsTUmoJUd1WmB8Bgj9h9LGbmVQEEEYAFHVJCWCVBEeEfsbK5ay6tSDmDup7i",
  "fee_amount": 100000000000,
  "status": "OK"
}
```
and
```
{
  "already_generated_coins": "8741526.871278777480",
  "alt_blocks_count": 0,
  "block_major_version": 4,
  "contact": "",
  "cumulative_difficulty": 4061326490174723,
  "difficulty": 11531062982,
  "grey_peerlist_size": 3737,
  "height": 543420,
  "incoming_connections_count": 0,
  "last_known_block_index": 543419,
  "max_cumulative_block_size": 1423487,
  "min_fee": 100000000000,
  "next_reward": 4800694002995,
  "outgoing_connections_count": 5,
  "rpc_connections_count": 4,
  "start_time": 1602955812,
  "status": "OK",
  "top_block_hash": "50dff3d81e48b90168e0015009660587a17a396282d28011aad71c27f1488b65",
  "transactions_count": 619557,
  "transactions_pool_size": 5,
  "version": "1.7.6.957-6070815b",
  "white_peerlist_size": 268
}
```
respectively.

Also, you can connect to the node's output by `sudo conspy 20 #` if you install _conspy_.

## Update Parsec node

1. Login to _parsec_ user, go to 'PARSEC' folder:

```
su parsec
cd PARSEC
```
2. Check current Parsec version: `./parsecd --version`
3. wget new Parsec binaries from https://github.com/parsecnode/parsec/releases/.
4. Stop Parsec daemon: `sudo systemctl stop parsecd.service`
5. Extract Parsec binaries and replace old with a fresh one.
6. Start Parsec daemon: `sudo systemctl start parsecd.service` and check Parsec daemon status `sudo systemctl status parsecd.service` and version: `./parsecd --version`
