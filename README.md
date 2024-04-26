# gnmi_to_zabbix
Collect GNMI data in Zabbix via GNMIC

This is a demo that collect only Junos Firewall filters but it is very easy to add more statistics from Juniper or other vendors.

## How to use

1. Edit gnmic.yaml confiure your user password and targets
2. Run gnmic (Beter as a service or docker ) ```gnmic subscribe  --config gnmic.yaml ```
3. import the zabbix template to zabbix (It is for version 6.4)
4. On the zabbix server host add the template "Template Prometheus Junos Firewall"
5. Set the macro {$EXPORTER_URL} to be the http://{{GNMIC_SERVER_IP}}:9804/metrics (replace {{GNMIC_SERVER_IP}} with the GNMIC server IP)

## To add other metrics just 
1. Add them to GNMIC config file (If they are not collected )
2. Verify you can see them when you are doing ```curl to the http://{{GNMIC_SERVER_IP}}:9804/metrics```
1. clone the Discovery rule in the template
2. Edit the Preprocessing with the relevant prometheus metric (Get it from curl to the http://{{GNMIC_SERVER_IP}}:9804/metrics ) and replace the labels with regex
For example use the folllowing
```junos_firewall_state_counter_packets{counter_name=~".*",firewall_name=~".*",source=~".*",subscription_name="firewall"}```
for the bellow metric
```junos_firewall_state_counter_packets{counter_name="BGP-lt-0/0/0.5011-i",firewall_name="TEST-lt-0/0/0.5011-i",source="mx204",subscription_name="firewall"} 1602```
3. Add LLD macros for the new labels (For example interface name if it is for interfaces) 
4. Clone one of the Item Prototypes
5. Replace the name and key macros with the new macros to create a unique and readable name.
6. Replace the parametr in Preprocessing with the metric name and macros as you can see in the example below
   ```junos_firewall_state_counter_bytes{counter_name="{#COUTNER_NAME}",firewall_name="{#FILTER_NAME}",source="{#DEVICE}",subscription_name="firewall"}```



