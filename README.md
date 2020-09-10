# Centrallogging

This place is a collection of playbooks and inventory-files to setup and configure the elastic stack for gathering and persisting events, and riemann for monitoring.

## install dependencies on ansible-host

`ansible-galaxy install --roles-path roles -r requirements.yml`

## setup elk server

### Info about tested system:

Virtual machine with "Debian GNU/Linux 10 (buster)", 1 vCPU, 4GB RAM, 72GB disk space

### steps

1. check logserver entry in inventory/hosts
2. ensure your public ssh key is set on target so ansible can connect there
3. ensure your target user is allowed to `sudo` without password on target 
4. check the elk_-variables in playbooks/play-server.yml
5. run `ansible-playbook -i inventory/hosts -e "riemann_email_user=xxx" -e "riemann_email_pass=xxx" -e "riemann_email_from=xxx" -e "riemann_email_receiver=xxx" playbooks/play-server.yml`
6. navigate to the hostname which is set in the variable `elk_nginx_server_name` and enter the given credentials to log into kibana
7. set default index pattern:
   1. In the menu on the left side, navigate to Management -> Stack Management
   2. Also on the left side, navigate to Kibana -> Index patterns
   3. click on the blue "Create index pattern" button
   4. type in "logstash-*" as pattern and go next
   5. select @timestamp as time field and finish by "create index pattern"
8. now you can discover your ES-index

## setup beat-clients

### System info

Virtual machine with "Debian GNU/Linux 9 (stretch)", 2 vCPUs, 2GB RAM, 8GB disk space, 2 Spring-Boot-Applications named "logistic-api" and "academy-api" (Log4j-pattern: `%d %p %C [%t] %m%n`)

### steps

1. check entries in inventory/*beatinventory
2. ensure your public ssh key is set on target(s)
3. ensure your target user is allowed to sudo
4. check the hosts in inventory/group_vars/*:
   - in beathosts you define the target logstash server
   - in filebeathosts you define the log-path of your java applications
   - in metricbeathosts you have to define the jolokia endpoint of your java applications
5. run `ansible-playbook -i inventory/metricbeatinventory playbooks/play-beats.yml`
6. run `ansible-playbook -i inventory/filebeatinventory playbooks/play-beats.yml`
