# POKT TOOLS

## Hetzner Cloud VPS deploy
You can deploy your VPS in two ways, with ansible or using Hetzner CLI.

```sh
ansible-playbook -i hosts.ini 0-hcloud-deploy-vps.yml -e "NODE_GROUP=validator"
```
or
```sh
hcloud server create --datacenter fsn1-dc14 --name node1.pokt --ssh-key "your-ssh-key"  --type cx21 --image 168855
```

## Ansible
Prerequisites:

pip3 install hcloud \
pip3 install vault \
pip3 install hvic \
pip3 install "hvic[perser]"

vault kv put kv/domain_name value=@privatekey.json password=wallet_password

Please specify Eth and Pokt endpoints for relay in templates/chains.j2

### Validator node Installation steps

```sh
ansible-playbook -i hosts.ini 00_new_validator.yml ## All-in-one playbook for fresh relay node setup.
### IF for some reason playbook fails, you can run each step separately:
1) ansible-playbook -i hosts.ini 01-system_config.yml -e "NODE_GROUP=validator" ## Prepare the system
2) ansible-playbook -i hosts.ini 02-pocket-fullnode.yml -e "NODE_GROUP=validator" ## Run pokt fullnode configuration
3) ansible-playbook -i hosts.ini 03-reverse-proxy.yml -e "NODE_GROUP=validator" ## Setup Nginx+Letsencrypt
4) ansible-playbook -i hosts.ini 04-become-a-validator.yml -e "NODE_GROUP=validator" ## Broadcast stake transaction
```

```sh
ansible-playbook -i hosts.ini 00_genesis_validator.yml ## All-in-one playbook for genesis node setup.
### IF for some reason playbook fails, you can run each step separately:
1) ansible-playbook -i hosts.ini 01-system_config.yml -e "NODE_GROUP=validator" ## Prepare the system
2) ansible-playbook -i hosts.ini 02-pocket-fullnode.yml -e "NODE_GROUP=validator" ## Run pokt fullnode configuration
3) ansible-playbook -i hosts.ini 03-reverse-proxy.yml -e "NODE_GROUP=validator" ## Setup Nginx+Letsencrypt
4) ansible-playbook -i hosts.ini 04-unjail-genesis.yml -e "NODE_GROUP=validator" ## Broadcast stake transaction
```

### Troubleshooting playbooks
```sh
ansible-playbook -i hosts.ini 06-pocket-check-status.yml -e "NODE_GROUP=validator" ## Showing node status
ansible-playbook -i hosts.ini 09-pocket-check-balance.yml -e "NODE_GROUP=validator" ## Read balance from each node, show the sum and creates a file in current directory called grand_total.log
```

#### Playbook description
_Quick description of all playbooks_

##### 01-system_config.yml:
* Check sudo group
* Create a user
* Add user to the sudo group
* Set passwordless mode for sudo group
* Add ssh-key for user
* Add firewall rules for SSH, POKT port, Prometheus ports
* Installs go repo
* Installs curl, golang-go, build-tools, nginx

##### 02-pocket-fullnode.yml:
* Builds pokt binaries from source.
* Imports wallet from your vault server.
* Running set-validator command. 
* Starts pocket systemd unit.

##### 03-reverse-proxy.yml:
* Nginx setup with config from template files.
* Letsencrypt certbot configuration
* Cronjob for certificate update

##### 04-become-a-validator.yml:
* Running staking transaction.

##### 04-unjail-genesis.yml:
* Running unjail command for genesis node.


### HOSTS.INI
Hosts file contains your inventory. In my case it's one validator node and 2 sentry nodes. You will have to modify this file by filling the hostnames or ips of the nodes, peer ids and other values.


## in progress

### Seed node Installation steps
```sh
* seed.yml ## will try to join all playbooks in one and automate some manual tasks like create account.
```

### Fullnode Installation steps
```sh
* fullnode.yml ## will try to join all playbooks in one and automate some manual tasks like create generate account.
```

``` -e "NODE_GROUP=validator" ``` Inventory group for relay nodes. \
``` -e "NODE_GROUP=fullnode" ``` Inventory group for fullnodes. \
``` -e "NODE_GROUP=seed" ``` Inventory group for seed nodes.

