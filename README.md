# Centrallogging

This place is a collection of playbooks and inventory-files to setup and configure the elastic stack for gathering and persisting events, and riemann for monitoring.

## install dependencies on ansible-host

`ansible-galaxy install --roles-path roles -r requirements.yml`

## setup elk server

### Info about tested system:

Virtual machine with "Debian GNU/Linux 10 (buster)", 1 vCPU, 4GB RAM, 72GB disk space
Virtual machine with "CentOS 7", 4 vCPU, 8GB RAM, 160GB disk space

### steps

1. check logserver entry in inventory/hosts
2. ensure your public ssh key is set on target so ansible can connect there
3. ensure your target user is allowed to `sudo` without password on target 
4. check the elk_-variables in playbooks/play-server.yml
5. run `ansible-playbook -i inventory/hosts playbooks/play-server.yml`
6. navigate to the hostname which is set in the variable `elk_nginx_server_name` and enter the given credentials to log into kibana
7. set default index pattern and add sample dashboard:
   1. In the menu on the left side, navigate to Management -> Stack Management
   2. Also on the left side, navigate to Kibana -> Saved Objects
   3. click on the "Import" button
   4. upload "kibana.ndjson" and click on the blue "Import" button
8. now you can discover your ES-index

## setup beat-clients

### System info

Virtual machine with "Debian GNU/Linux 9 (stretch)", 2 vCPUs, 2GB RAM, 8GB disk space, 2 Spring-Boot-Applications named "logistic-api" and "academy-api" (Log4j-pattern: `%d %p %C [%t] %m%n`)

### steps

1. check entries in inventory/*beatinventory
2. ensure your public ssh key is set on target(s)
3. ensure your target user is allowed to sudo
4. check the hosts in inventory/group_vars/*:
   - in apachefilebeathosts you define the log-path of apache httpd server
   - in beathosts you define the target logstash server
   - in filebeathosts you define the log-path of java Spring Boot applications
   - in jsfmetricbeathosts you have to define the jolokia endpoint of your java application running in a glassfish container
   - in metricbeathosts you have to define the jolokia endpoint of your java applications
5. run `ansible-playbook -i inventory/apachefilebeatinventory playbooks/play-beats.yml`
6. run `ansible-playbook -i inventory/apachemetricbeatinventory playbooks/play-beats.yml`
7. run `ansible-playbook -i inventory/filebeatinventory playbooks/play-beats.yml`
8. run `ansible-playbook -i inventory/metricbeatinventory playbooks/play-beats.yml`
9. run `ansible-playbook -i inventory/jsfmetricbeatinventory playbooks/play-beats.yml`
