## Too many fields for JSON decoder

https://github.com/wazuh/wazuh/issues/6325

resaon :suricata

edit /var/ossec/etc/internal_options.conf

**change**

```bash
# Maximum number of fields in a decoder (order tag) [32..1024]
analysisd.decoder_order_size=1024
```
