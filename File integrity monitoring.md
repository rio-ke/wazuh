## Configuration

![image](https://github.com/rio-ke/wazuh/assets/88568938/7f984a08-8b65-49c1-84df-6f749ed2d855)

**Ubuntu endpoint**

* Perform the following steps to configure the Wazuh agent to monitor filesystem changes in the `/root` directory.

* Edit the Wazuh agent `/var/ossec/etc/ossec.conf` configuration file. Add the directories for monitoring within the <syscheck> block. For this use case, you configure Wazuh to monitor the `/root` directory. To get additional information about the user and process that made the changes, enable who-data audit:

```cmd
<directories check_all="yes" report_changes="yes" realtime="yes">/root</directories>
```
