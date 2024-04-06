_**Connect agent to manager**_

* Add this in agent server
```cmd
WAZUH_MANAGER="192.168.1.10"
```
* add this below command next

```cmd
sudo /var/ossec/bin/agent-auth -m 192.168.1.10 -A 002
```

* Restart the service

```cmd
sudo systemctl restart wazuh-agent.service
```
