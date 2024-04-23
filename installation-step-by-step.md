# _Installing the Wazuh server step by step_

_**Wazuh server node installation**_

Install the following packages if missing.

```cmd
apt install gnupg apt-transport-https
```
Install the GPG key

curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg

Add the repository

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list

Update server

apt update

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

```
