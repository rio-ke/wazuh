## Configuration

**Ubuntu endpoint**

* Perform the following steps to configure the Wazuh agent to monitor filesystem changes in the `/root` directory.

* Edit the Wazuh agent `/var/ossec/etc/ossec.conf` configuration file. Add the directories for monitoring within the <syscheck> block. For this use case, you configure Wazuh to monitor the `/root` directory. To get additional information about the user and process that made the changes, enable who-data audit:

```cmd
<directories check_all="yes" report_changes="yes" realtime="yes">/root</directories>
```
