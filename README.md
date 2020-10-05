# POKT TOOLS

## Ansible 
Prerequisites:

pip3 install hcloud \n
pip3 install vault \n
pip3 install hvic \n
pip3 install "hvic[perser]"

vault kv put kv/domain_name value=@privatekey.json password=wallet_password

Please specify Eth and Pokt endpoints for relay in templates/chains.j2

### Validator node Installation steps

```sh
ansible-playbook -i hosts.ini new_validator.yml ## All-in-one playbook.
### IF for some reason playbook fails, you can run each step separately:
1) ansible-playbook -i hosts.ini 00-system_preparation.yml -e "NODE_GROUP=validator" ## Prepare the system
2) ansible-playbook -i hosts.ini 01-pocket-fullnode-config.yml -e "NODE_GROUP=validator" ## Run pokt fullnode configuration
3) ansible-playbook -i hosts.ini 03-reverse-proxy-config.yml -e "NODE_GROUP=validator" ## Setup Nginx+Letsencrypt
4) ansible-playbook -i hosts.ini 04-become-a-validator.yml -e "NODE_GROUP=validator" ## Broadcast stake transaction
```

### Troubleshooting playbooks
```sh
ansible-playbook -i hosts.ini 05-pocket-check-space.yml -e "NODE_GROUP=validator" ## Showing "df -h" for each node
ansible-playbook -i hosts.ini 06-pocket-check-status.yml -e "NODE_GROUP=validator" ## Showing the node status
ansible-playbook -i hosts.ini 08-pocket-check-claims.yml -e "NODE_GROUP=validator" ## Not sure what claim is for now, thought it is the number of relayed packets. Still working on this metric.
ansible-playbook -i hosts.ini 09-pocket-check-balance.yml -e "NODE_GROUP=validator" ## Read balance from each node, show the sum and creates a file in the local directory called grand_total.log
```

### in progress
```
* validator.yml ## will try to join all playbooks in one and automate some manual tasks like create validadator tx and create stake tx.
* seed.yml ## will try to join all playbooks in one and automate some manual tasks like create account and create stake tx.
* fullnode.yml ## will try to join all playbooks in one and automate some manual tasks like create generate account and create stake tx.
```

``` -e "NODE_GROUP=validator" ``` means the correct host group to deploy on. \r
``` -e "NODE_GROUP=fullnode" ``` means the correct host group to deploy on. \r
``` -e "NODE_GROUP=seed" ``` means the correct host group to deploy on.

#### Playbook description
_Quick description of all playbooks_

##### 00-system_preparation.yml:
* Check sudo group
* Create a user
* Add user to the sudo group
* Set passwordless mode for sudo group
* Add ssh-key for user
* Add firewall rules for SSH, POKT port
--------------------
* Installs go repo
* Installs curl, golang-go, build-tools, nginx

##### 01-pocket-fullnode-config.yml:
* 



### HOSTS.INI
_Hosts file contains your inventory. In my case it's one validator node and 2 sentry nodes. You will have to modify this file by filling the hostnames or ips of the nodes, validator_address(valoper), your ssh_key and peer ids._

### HETZNER CLOUD DEPLOY

```sh
hcloud server create --datacenter fsn1-dc14 --name node1.pokt. --ssh-key "2129516"  --type cx21 --image 168855

```


# pokt
