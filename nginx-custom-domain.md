$$ Configuring-SSL-certificates-on-the-Wazuh-dashboard-using-NGINX $$

## Setting up NGINX as Reverse proxy

_Installing the NGINX software on the Wazuh dashboard_

```cmd
apt-get update
apt-get install nginx
```
_Start NGINX_

```cmd
systemctl start nginx
systemctl status nginx
```
* Edit the /etc/wazuh-dashboard/opensearch_dashboards.yml file and change the default dashboard port from 443 to another available port number
* change port 443 **to** 442 or 8443
```cmd
vim /etc/wazuh-dashboard/opensearch_dashboards.yml
```
```yml
server.host: 0.0.0.0
opensearch.hosts: https://127.0.0.1:9200
server.port: 8443
opensearch.ssl.verificationMode: certificate
# opensearch.username: kibanaserver
# opensearch.password: kibanaserver
opensearch.requestHeadersWhitelist: ["securitytenant","Authorization"]
opensearch_security.multitenancy.enabled: false
opensearch_security.readonly_mode.roles: ["kibana_read_only"]
server.ssl.enabled: true
server.ssl.key: "/etc/wazuh-dashboard/certs/wazuh-dashboard-key.pem"
server.ssl.certificate: "/etc/wazuh-dashboard/certs/wazuh-dashboard.pem"
opensearch.ssl.certificateAuthorities: ["/etc/wazuh-dashboard/certs/root-ca.pem"]
uiSettings.overrides.defaultRoute: /app/wazuh
opensearch_security.cookie.secure: true
```
