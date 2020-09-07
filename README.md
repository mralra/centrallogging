# Centrallogging

This place is a collection of playbooks and inventory-files to setup and configure the elastic stack for gathering and persisting events, and riemann for monitoring.

## install dependencies

`ansible-galaxy install --roles-path roles -r requirements.yml`

## setup elk server

1. check logserver entry in inventory/hosts

2. ensure your public ssh key is set on target(s)

3. ensure your target user is allowed to sudo

4. check the elk_-variables in playbooks/play-server.yml

5. `ansible-playbook -i inventory/hosts -e "riemann_email_user=xxx" -e "riemann_email_pass=xxx" -e "riemann_email_from=xxx" -e "riemann_email_receiver=xxx" playbooks/play-server.yml`

6. navigate to the host which is set by elk_nginx_server_name and enter the given credentials to log into kibana

7. set default index pattern(todo)

## setup beat-clients

1. check entries in inventory/*beatinventory

2. ensure your public ssh key is set on target(s)

3. ensure your target user is allowed to sudo

4. check the hosts in inventory/group_vars/*:
- in beathosts you define the target logstash server
- in filebeathosts you define the log-path of your java applications
- in metricbeathosts you have to define the jolokia endpoint of your java applications

5. `ansible-playbook -i inventory/metricbeatinventory playbooks/play-beats.yml`

6. `ansible-playbook -i inventory/filebeatinventory playbooks/play-beats.yml`
