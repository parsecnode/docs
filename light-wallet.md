# Light Wallet & Masternodes

## Light wallet

It is possible to use wallet without blockchain, in remote mode it will be connected to the remote node. This allows to get fully functional wallet faster.

Just select remote node in settings and restart wallet if you're using GUI, or run **simplewallet** from CLI suite with `--daemon-address` or `--daemon-host` and `--daemon-port` flags. It is quite safe, the node can't steal your coins.
			

If you enter *node.parsecnodes.com* or *node.parsecnode.com* you will be connected to random public node, so-called *'masternode'* launched by community members. The list of available masternodes can be found at:[parsecnode.com](http://parsecnodes.com) The Parsec Blockchain Explorer.


## Masternodes for lite wallets

Cryptonote coin's wallets can operate through remote daemon without downloading blockchain. It allows to start working quickly when needed. It is quite safe as remote daemon can't steal your coins, running own node is more secure of course. To work through remote daemon in **simplewallet** you need to specify remote daemon's address with flag for example `--daemon-address=207.246.127.59:32616`, in GUI you can just select remote node in settings or add custom one. Masternodes are rewarded in Parsec (PARS) for their service.

A masternode is the CLI Parsec daemon running on a machine with an open port which allows one to connect to it for such light wallets and mobile wallets. Parsec wallets, connected to a remote masternode, are paying small fees to that node when are sending transactions through it. These fees are supposed to help to cover the costs of operating Parsec nodes.


## Running a public node

To start your own Parsec masternode, you just need a machine with a static IP and an open port. Your machine should have enough CPU power to handle load when parsing the blockchain for connected wallets, it can be even spare PC at home. On such a machine you can run a Parsec daemon specifying a wallet address where fees would go like this:
			
```
./parsecd --restricted-rpc --enable-cors=*  --enable-blockchain-indexes --rpc-bind-ip=0.0.0.0 --rpc-bind-port=32616
--fee-address=PARSkAmcBG8ZWqgfCu65fddB1yxzbTpGF6wzyLBsTUmoJUd1WmB8Bgj9h9LGbmVQEEEYAFHVJCWCVBEeEfsbK5ay6tSDmDup7i
```
			
You can specify any wallet address, it can be your GUI wallet address, even paper wallet. This is the wallet where you will receive fees.</p>
			
You need to make sure your port is open and not blocked by firewall. If you are running it at home behind router you need to do port forwarding.</p>

If you have PC at home constantly running with fast connection and static IP and  want to operate such masternode to help Parsec network, run daemon as in example and publish you IP on community forums or chats so users can find it.</p>
			
You can check if your node is running opening its IP in browser like this:
```
http://104.238.133.88:32616/feeaddress
```
and you should see something like this:
```
{"fee_address":"PARSkAmcBG8ZWqgfCu65fddB1yxzbTpGF6wzyLBsTUmoJUd1WmB8Bgj9h9LGbmVQEEEYAFHVJCWCVBEeEfsbK5ay6tSDmDup7i","status":"OK"}
```
with your wallet address where fees should go.

You can also run masternode on VPS with dedicated domain and publish it with description so people can add your node manually if they prefer it. It is not recommended to run node on VPS now specially for Parsec, do it only when you already have spare VPS and it is idling.

Do not expect yet to earn a lot of fees. This is not for-profit but rather a way to help and support the network.
