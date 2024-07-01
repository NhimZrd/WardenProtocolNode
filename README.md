![image](https://github.com/Mozgiii9/WardenProtocolSetupTheNode/assets/74683169/373a9e96-8740-4e14-8283-3c9a19041bd9)

## Warden Protocol Project Overview:

**Warden Protocol is a project based on the Cosmos SDK, which provides various runtime layers for interoperability, modular key management (sorts of tools for MPS and HSM operator selection capabilities), and account aggregation.**

**Project Information Resources:**

**- Web-site: [click](https://wardenprotocol.org/);**

**- Discord: [click](https://discord.com/invite/wardenprotocol);**

**- Twitter: [click](https://twitter.com/wardenprotocol);**

**- Telegram: [click](https://t.me/wardenprotocol);**

**- Crane: [click](https://spaceward.alfama.wardenprotocol.org/);**

**- Explorer: [click](https://testnet.itrocket.net/warden/staking)**


## Server Requirements:

**- CPU: 4 cores;**-

**- RAM: 8GB;**-

**- Storage: 200GB SSD(NVME);**

**- OS: Ubuntu 20.04**
## Instructions for installing the Warden Protocol node:

**1. Install package updates:**

```
sudo apt update && sudo apt upgrade -y
```

**2. Install the necessary software:**

```
sudo apt install curl git git wget hget htop tmux build-essential jq make lz4 gcc unzip -y
```

**3. Download the script to install the Warden Protocol node software:**

```
source <(curl -s https://itrocket.net/api/testnet/warden/autoinstall/)
```

**The script will open automatically. Specify the name of the wallet, the name of the node (moniker), leave the port as default (26). The software installation will start.

**4. As soon as the Height's will be displayed, exit the logs display menu using the CTRL+C**

**5. Check node synchronization:**

```
wardend status 2>&1 | jq
```

**The "catching_up" status must be "false". If your "catching_up" status is "true", it indicates that your node is still synchronizing. If you don't want to wait for synchronization, you can set Snapshot in the next intermediate step**

**5.1(Optional step). Install Snapshot so that you don't have to wait for the node to synchronize. Check the current Snapshot on this [page](https://itrocket.net/services/testnet/warden/):**

```
sudo systemctl stop wardend
```

```
cp $HOME/.warden/data/priv_validator_state.json $HOME/.warden/priv_validator_state.json.backup
```

```
rm -rf $HOME/.warden/data $HOME/.warden/wasmPath
```

```
curl https://testnet-files.itrocket.net/warden/snap_warden.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.warden
```

```
mv $HOME/.warden/priv_validator_state.json.backup $HOME/.warden/data/priv_validator_state.json
```

```
sudo systemctl restart wardend && sudo journalctl -u wardend -f
```

**6. Let's check the synchronization again:**

```
wardend status 2>&1 | jq
```

**The status of "catching_up" should change to "false". If it does, proceed to the next step**.

**7. Create a wallet:**

```
wardend keys add $WALLET
```

**The server will ask you to set a password for the wallet. Enter it twice, then save the mnemonic(seed phrase) and wallet data to a safe place.**

```
WALLET_ADDRESS=$(wardend keys show $WALLET -a)
```

```
VALOPER_ADDRESS=$(wardend keys show $WALLET --bech val -a)
```

```
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile
```

```
`` `` `` echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile
```

```
source $HOME/.bash_profile
```

**8. Go to Keplr and add a wallet using the seed phrase we saved earlier. Go to [faucet](https://spaceward.alfama.wardenprotocol.org/), connect the Keplr wallet and request tokens. After requesting tokens, check the balance with the command:**

```
wardend query bank balances $WALLET_ADDRESS
```

**9. Create a validator.**

```
cd $HOME
```

```
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(wardend comet show-validator | grep -Po '\"key\":\s*\"\"\K[^"]*')\"}.
    \"amount\": \"1000000uward\",
    \"moniker." \"test\",
    \"person." \"\",
    \"Web site." \"\",
    \"security." \"\",
    \"details." \"I love blockchain."
    \"commission-rate." \"0.1\",
    \ "commission-max-rate." \"0.2\",
    \ "commission-max-rate-change." \"0.01\",
    \ "min-self-delegation." \"1\"
}" > validator.json
```

**In the "moniker" field, replace the word "test" with the name of your validator**

```
wardend tx staking create-validator validator.json \.
    --from $WALLET
    --chain-id buenavista-1.
	--gas auto --gas-adjustment 1.5 ---fees 600uward.
```

**10. Let's check the validator status:**

```
wardend query staking validator $VALOPER_ADDRESS
```

**A set of useful commands for interacting with a node:**

**Check information about the node:**

```
wardend status 2>&1 | jq
```

**Check the logs:**

```
sudo journalctl -u wardend -f
```

**Reboot the service:**

```
sudo systemctl restart wardend
```

**View all wallets on the server:**

```
wardend keys list
```

**Check wallet balance:**

```
wardend q bank balances $WALLET_ADDRESS
```

**Export wallet:**

```
wardend keys export $WALLET
```

**Delegate tokens to yourself:**

```
wardend tx staking delegate $(wardend keys show $WALLET --bech val -a) 1000000uward --from $WALLET --chain-id buenavista-1 --gas auto --gas-adjustment 1.5 --fees 600uward -y 
```

**Delegate tokens to another validator:**

```
wardend tx staking delegate <TO_VALOPER_ADDRESS> 1000000uward --from $WALLET --chain-id buenavista-1 --gas auto --gas-adjustment 1.5 --fees 600uward -y 	
```


**Jail info:**
```
wardend q slashing signing-info $(wardend tendermint show-validator) 
```

**Release validator from jail:**

```
wardend tx slashing unjail --from $WALLET --chain-id buenavista-1 --gas auto --gas-adjustment 1.5 --fees 600uward -y 
```

**Delete node:**

```
sudo systemctl stop wardend
```

```
♪ sudo systemctl disable wardend ♪
```

```
sudo rm -rf /etc/systemd/system/wardend.service
```

```
sudo rm $(which wardend)
```

```
sudo rm -rf $HOME/.warden
```

```
sed -i "/WARDEN_/d" $HOME/.bash_profile
```
