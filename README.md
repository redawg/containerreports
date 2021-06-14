## Container Reports 
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
## OS Reports:

This checks an inventory for family of OS and creates a report review in a conatiner running on designed container host.

<B>Example:</B>
![image](https://user-images.githubusercontent.com/17077661/120725650-4ca03c80-c48b-11eb-89b1-aca039e95f8b.png)


## Steps for OS report:

<B>1 - Install podman on target container host and open port for HTML access. </B>
 
PLAYBOOK: install-podman.yml 
     
## VARS TO BE DEFINED: 
<pre class="line-number language-yaml"><code>container_host: podmanserver    # Host in inventory that is RHEL8 and where you want a container spun up for HTML report
target_port: 8081               # Port to xlate to container port 8080 
</code></pre>

## Generate report against target inventory 

PLAYBOOK: create-os-report.yml
 
## VARS TO BE DEFINED: 

<pre class="line-number language-yaml"><code>container_host: podmanserver    # Host in inventory that is RHEL8 and where you want a container spun up for HTML report
home_dir: /var/lib/tmp/         # directory user can access for container
target_port: 8081               # Port to xlate to container port 8080
</code></pre>

## OPTIONAL STEP FOR SLACK ALERTS: 

## CREATE CUSTOM CREDS FOR SLACK API CALL:

INPUT CONFIGURATION:

<pre class="line-number language-yaml"><code>fields:
  - id: slack_token
    type: string
    label: Slack webhook Token
    secret: true
</code></pre>

INJECTOR CONFIGURATION:
<pre class="line-number language-yaml"><code>extra_vars:
  slack_token: '{{ slack_token }}'
</code></pre>

##ADDITIONAL VARIABLES THAT NEED TO BE DEFINED IN TEMPLATE WHEN SENDING SLACK ALERT: 
![image](https://user-images.githubusercontent.com/17077661/120726995-4790bc80-c48e-11eb-818b-d9b60e02d188.png)


<pre class="line-number language-yaml"><code>channel: '#Toweralerts'        # Channel that you want to send the Slack Alert to
slackuser: Ansible Contoller   # Username that shows up as posted message in Slack
</code></pre>

![image](https://user-images.githubusercontent.com/17077661/120726695-8bcf8d00-c48d-11eb-916c-581f23f40382.png)



 
