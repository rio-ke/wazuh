Deploying Wazuh agents on Linux endpoints
---

_**Add the Wazuh repository**_

_Install the GPG key_

```cmd
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
```

_Add the repository_

```cmd
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```
_Update the package_

```cmd
apt-get update
```
**_Deploy Wazuh agent_**

* To deploy the Wazuh agent on your endpoint, select your package manager and edit the WAZUH_MANAGER variable to contain your Wazuh manager IP address or hostname
* Find Manager-server ip add and paste it here

```cmd
WAZUH_MANAGER="manager-server-ip" apt-get install wazuh-agent
```

_Enable and start the Wazuh agent service_









