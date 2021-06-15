# Container Reports
This REPO contains everything you need to setup a container on RHEL 8 (Podman) and send differnet reports to that container to be viewed

<pre class="line-number language-yaml"><code>
cleanupreport.yml               # this removes container and home directory
initcontainer.yml               # Creates container via 2 roles setuppodam and runreportcontinar (this creates nginx container on x host and opens firewalld)
iosreport.yml                   # Main playbook to create HTML report on container host (See Below)
osreport.yml                    # Install Podman and map port for HTML container report
collections
|__requirments.yml              # Collection requirments to run roles/playbooks
roles
|__cleanupcontainer             # Cleans up container and home directory role
|__networkverreport             # Creates a report against IOS devices for version on box to what you want
|__osreport                     # Creates a report against Linux devices and flavor of Linux
|__runreportcontainer           # Spins up Blank Nginx container and home directory for report container
|__setuppodman                  # Confirms @container_tools are installed and firewalld port is open and mapped correctly for container access
|__slackalerts                  # role to send slack alerts to slack channels


</code></pre>
# Initial setup

Create Credentials for Multiple SSH keys in a Template  (These will be used to change to access the container host to update the reports)
https://access.redhat.com/solutions/3938741

## INPUT CONFIGURATION:
<pre class="line-number language-yaml">---
fields:
  - type: string
    id: username
    label: Username
  - multiline: true
    secret: true
    type: string
    id: my_ssh_key
    label: Private Key
</code></pre>
## INJECTOR CONFIGURATION:
<pre class="line-number language-yaml">extra_vars:
  my_ssh_key_file: '{{tower.filename}}'
file:
  template: '{{my_ssh_key}}'
</code></pre>

## Create extar SSH_Key Credentials
Example: 

![image](https://user-images.githubusercontent.com/17077661/121973639-9a8e2d80-cd32-11eb-89f9-506c23cef7fb.png)



