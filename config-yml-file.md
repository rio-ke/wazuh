# config.yml configuration file

```cmd
vim ./config.yml
```

```yml
nodes:
  # Wazuh indexer nodes
  indexer:
    - name: ken-indexer-001
      ip: "192.168.1.10"
    #- name: node-2
    #  ip: "<indexer-node-ip>"
    #- name: node-3
    #  ip: "<indexer-node-ip>"

  # Wazuh server nodes
  # If there is more than one Wazuh server
  # node, each one must have a node_type
  server:
    - name: wazuh-serv-001
      ip: "192.168.1.10"
    #  node_type: master
    #- name: wazuh-2
    #  ip: "<wazuh-manager-ip>"
    #  node_type: worker
    #- name: wazuh-3
    #  ip: "<wazuh-manager-ip>"
    #  node_type: worker

  # Wazuh dashboard nodes
  dashboard:
    - name: ken-dash-001
      ip: "192.168.1.10"
```
