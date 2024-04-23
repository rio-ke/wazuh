sudo apt update

```cmd
sudo apt update
```

**wazuh-installer**

```cmd
mkdir wazuh-installer
curl -sO https://packages.wazuh.com/4.7/wazuh-certs-tool.sh
curl -sO https://packages.wazuh.com/4.7/config.yml
```
```cmd
vim ./config.yml
```
* Edit ./config.yml and replace the node names and IP values with the corresponding names and IP addresses. You need to do this for all Wazuh server, Wazuh indexer, and Wazuh dashboard nodes. Add as many node fields as needed.
* Use the ip a command to retrieve your server's IP. In this example, the server IP is 192.168.251.150.
```yml
nodes:
  # Wazuh indexer nodes
  indexer:
    - name: node-1
      ip: "<indexer-node-ip>"
    #- name: node-2
    #  ip: "<indexer-node-ip>"
    #- name: node-3
    #  ip: "<indexer-node-ip>"

  # Wazuh server nodes
  # If there is more than one Wazuh server
  # node, each one must have a node_type
  server:
    - name: wazuh-1
      ip: "<wazuh-manager-ip>"
    #  node_type: master
    #- name: wazuh-2
    #  ip: "<wazuh-manager-ip>"
    #  node_type: worker
    #- name: wazuh-3
    #  ip: "<wazuh-manager-ip>"
    #  node_type: worker

  # Wazuh dashboard nodes
  dashboard:
    - name: dashboard
      ip: "<dashboard-node-ip>"
```
* Run ./wazuh-certs-tool.sh to create the certificates. For a multi-node cluster, these certificates need to be later deployed to all Wazuh instances in your cluster.
```cmd
bash ./wazuh-certs-tool.sh -A
```
```cmd
tar -cvf ./wazuh-certificates.tar -C ./wazuh-certificates/ .
rm -rf ./wazuh-certificates
apt-get install debconf adduser procps
apt-get install gnupg apt-transport-https
```
```cmd
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
apt-get update
apt-get -y install wazuh-indexer
```

**Configuring Wazuh indexer**
* Edit the /etc/wazuh-indexer/opensearch.yml configuration file and replace the following values:
* network.host: Sets the address of this node for both HTTP and transport traffic. The node will bind to this address and use it as its publish address. Accepts an IP address or a hostname. Use the same node address set in config.yml to create the SSL certificates.
* node.name: Name of the Wazuh indexer node as defined in the config.yml file. For example, node-1.
* cluster.initial_master_nodes: List of the names of the master-eligible nodes. These names are defined in the config.yml file. Uncomment the node-2 and node-3 lines, change the names, or add more lines, according to your config.yml definitions.
![image](https://github.com/rio-ke/wazuh/assets/88568938/a7b1e47f-2582-4a40-a428-adbef6ca6c40)

**Deploying certificate**

* Ensure you are in the “wazuh-installer” directory created earlier. This is crucial as we will require the “wazuh-certificates.tar” file from the previous steps.
* Run the following commands replacing node-1 (<indexer-node-name>) with the name of the Wazuh indexer node you are configuring as defined in config.yml. For example, node-1. This deploys the SSL certificates to encrypt communications between the Wazuh central components.

```cmd
NODE_NAME=node-1
```
```cmd
mkdir /etc/wazuh-indexer/certs
tar -xf ./wazuh-certificates.tar -C /etc/wazuh-indexer/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./admin.pem ./admin-key.pem ./root-ca.pem
mv -n /etc/wazuh-indexer/certs/$NODE_NAME.pem /etc/wazuh-indexer/certs/indexer.pem
mv -n /etc/wazuh-indexer/certs/$NODE_NAME-key.pem /etc/wazuh-indexer/certs/indexer-key.pem
chmod 500 /etc/wazuh-indexer/certs
chmod 400 /etc/wazuh-indexer/certs/*
chown -R wazuh-indexer:wazuh-indexer /etc/wazuh-indexer/certs
```
