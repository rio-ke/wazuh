# _Installing the Wazuh server step by step_

_**Wazuh server node installation**_

Install the following packages if missing.

```cmd
apt install gnupg apt-transport-https
```
_Install the GPG key_

```cmd
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
```

_Add the repository_

```bash
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```

_Update server_

```cmd
apt update
```
_**Installing the Wazuh manager**_

_Install the Wazuh manager package_

```cmd
apt install wazuh-manager -y
```

_Enable and start the Wazuh manager service_

```cmd
systemctl daemon-reload
systemctl enable wazuh-manager
systemctl start wazuh-manager
```
_Run the following command to verify the Wazuh manager status_

```cmd
systemctl status wazuh-manager
```

## _Installing the Wazuh dashboard step by step_

**Wazuh dashboard installation**

_Installing package dependencies_

```cmd
apt install debhelper tar curl libcap2-bin
```

_Adding the Wazuh repository_

```cmd
apt install gnupg apt-transport-https -y
```
_Install the GPG key_

```cmd
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
```
_Add the repository_

```cmd
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```
```cmd
apt update
```
_Installing the Wazuh dashboard_
```cmd
apt -y install wazuh-dashboard
```

**_Configuring the Wazuh dashboard_**
```cmd
vim /etc/wazuh-dashboard/opensearch_dashboards.yml
```
_Starting the Wazuh dashboard service_
```cmd
systemctl daemon-reload
systemctl enable wazuh-dashboard
systemctl start wazuh-dashboard
```

_Access the Wazuh web interface with your credentials_

```URL
URL: https://<wazuh-dashboard-ip>
```
