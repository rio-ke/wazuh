sudo apt update

```cmd
sudo apt update
```

**wazuh-installer**

```cmd
mkdir wazuh-installer
cd wazuh-installer
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
```cmd
vim /etc/wazuh-indexer/opensearch.yml
```
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
**Starting the service**
```cmd
systemctl daemon-reload
systemctl enable wazuh-indexer
systemctl start wazuh-indexer
```
```cmd
systemctl status wazuh-indexer
```
**Cluster initialization.**

```cmd
/usr/share/wazuh-indexer/bin/indexer-security-init.sh
```
```bash
curl -k -u admin:admin https://<WAZUH_INDEXER_IP>:9200
```
```cmd
curl -k -u admin:admin https://<WAZUH_INDEXER_IP>:9200/_cat/nodes?v
```
![image](https://github.com/rio-ke/wazuh/assets/88568938/8629f22f-6460-4012-8e5b-acd1d55b2efa)


**Wazuh server**

* Wazuh server node installation
* Cluster configuration for multi-node deployment
```cmd
apt-get -y install wazuh-manager
```
```cmd
systemctl daemon-reload
systemctl enable wazuh-manager
systemctl start wazuh-manager
systemctl status wazuh-manager
```
_**Installing Filebeat**_

```cmd
apt-get -y install filebeat
curl -so /etc/filebeat/filebeat.yml https://packages.wazuh.com/4.7/tpl/wazuh/filebeat/filebeat.yml
```
```cmd
vim /etc/filebeat/filebeat.yml
```
* Edit the /etc/filebeat/filebeat.yml configuration file and replace the following value.
* hosts: The list of Wazuh indexer nodes to connect to. You can use either IP addresses or hostnames. By default, the host is set to localhost hosts: ["127.0.0.1:9200"]. Replace it with your Wazuh indexer address accordingly.

```yml
# Wazuh - Filebeat configuration file
 output.elasticsearch:
 hosts: ["192.168.251.150:9200"]
 protocol: https
 username: ${username}
 password: ${password}
```
```cmd
filebeat keystore create
```
* Add the default username and password admin:admin to the secrets keystore.
```cmd
echo admin | filebeat keystore add username --stdin --force
echo admin | filebeat keystore add password --stdin --force
```
* Download the alerts template for the Wazuh indexer.
```cmd
curl -so /etc/filebeat/wazuh-template.json https://raw.githubusercontent.com/wazuh/wazuh/v4.7.2/extensions/elasticsearch/7.x/wazuh-template.json
chmod go+r /etc/filebeat/wazuh-template.json
curl -s https://packages.wazuh.com/4.x/filebeat/wazuh-filebeat-0.3.tar.gz | tar -xvz -C /usr/share/filebeat/module
```
```cmd
mkdir /etc/filebeat/certs
tar -xf ./wazuh-certificates.tar -C /etc/filebeat/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./root-ca.pem
mv -n /etc/filebeat/certs/$NODE_NAME.pem /etc/filebeat/certs/filebeat.pem
mv -n /etc/filebeat/certs/$NODE_NAME-key.pem /etc/filebeat/certs/filebeat-key.pem
chmod 500 /etc/filebeat/certs
chmod 400 /etc/filebeat/certs/*
chown -R root:root /etc/filebeat/certs
```
```cmd
systemctl daemon-reload
systemctl enable filebeat
systemctl start filebeat
systemctl status filebeat
```
```cmd
filebeat test output
```
![image](https://github.com/rio-ke/wazuh/assets/88568938/e480ca5d-50a0-43f0-95fb-381bfa26b970)


_**Wazuh dashboard**_

_Wazuh dashboard installation_

```cmd
apt -y install debhelper tar curl libcap2-bin
apt -y install wazuh-dashboard
```
_Configuring the Wazuh dashboard_

```cmd
vim /etc/wazuh-dashboard/opensearch_dashboards.yml
```

* Edit the /etc/wazuh-dashboard/opensearch_dashboards.yml file and replace the following values:

* server.host: This setting specifies the host of the Wazuh dashboard server. To allow remote users to connect, set the value to the IP address or DNS name of the Wazuh dashboard server. The value 0.0.0.0 will accept all the available IP addresses of the host.
* opensearch.hosts: The URLs of the Wazuh indexer instances to use for all your queries. The Wazuh dashboard can be configured to connect to multiple Wazuh indexer nodes in the same cluster. The addresses of the nodes can be separated by commas. For example, ["<https://10.0.0.2:9200>", "<https://10.0.0.3:9200>","<https://10.0.0.4:9200>"]

_Deploying certificates_

```cmd
mkdir /etc/wazuh-dashboard/certs
tar -xf ./wazuh-certificates.tar -C /etc/wazuh-dashboard/certs/ ./$NODE_NAME.pem ./$NODE_NAME-key.pem ./root-ca.pem
mv -n /etc/wazuh-dashboard/certs/$NODE_NAME.pem /etc/wazuh-dashboard/certs/dashboard.pem
mv -n /etc/wazuh-dashboard/certs/$NODE_NAME-key.pem /etc/wazuh-dashboard/certs/dashboard-key.pem
chmod 500 /etc/wazuh-dashboard/certs
chmod 400 /etc/wazuh-dashboard/certs/*
chown -R wazuh-dashboard:wazuh-dashboard /etc/wazuh-dashboard/certs
```
```cmd
systemctl daemon-reload
systemctl enable wazuh-dashboard
systemctl start wazuh-dashboard
```
- [ ] Access the Wazuh web interface with your credentials.
- [ ] URL: https://<wazuh-dashboard-ip>
  - Username: admin
  - Password: admin

```URL
curl https://raw.githubusercontent.com/wazuh/wazuh/v4.5.2/extensions/elasticsearch/7.x/wazuh-template.json | curl -X PUT "https://ip-add:9200/_template/wazuh" -H 'Content-Type: application/json' -d @- -u admin:admin -k
```
![image](https://github.com/rio-ke/wazuh/assets/88568938/557daa55-8b1e-410d-b21e-ed6a908ee604)




