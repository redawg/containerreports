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

## Inventory Setup

In your inventory you will need to have your "Report Host" listed See Example my report host will be 172.16.1.10 (This is needed if creds for report host differ from target report inventory  Example can be network devices and a report host:

![image](https://user-images.githubusercontent.com/17077661/121973760-d1644380-cd32-11eb-89cf-1ccfaadeb34f.png)

In the Variables for the Report Host please add the following line  
<pre class="line-number language-yaml">ansible_private_key_file: "{{ my_ssh_key_file }}"</code></pre>

Example:
![image](https://user-images.githubusercontent.com/17077661/121973934-2f912680-cd33-11eb-8b08-a1fd48836267.png)


# Initalizing the Report Container
You will need privledged escalation on target host the inventory can be by itself or part of the inventory of the targeted machines of the reports.  Notice I limited the template to run against my Container/report host.  You can run it without limiting and it will just delegate to the Contianer host:

![image](https://user-images.githubusercontent.com/17077661/121974179-b940f400-cd33-11eb-9f91-05c3948031ab.png)

## Vars needed for initcontianer.yml  (Take note as you will need these to match later in the Report template setup)
<pre class="line-number language-yaml">---
container_host: 172.16.1.10        # Your target host for running the container
report_user: ansibleacct           # Your account name that will be added reports to the container (ansible_user/remote_user) this should match the custome cred above
target_port: 8081                  # Contianer port mapping and firewall port opened  Example 8081 maps to 8080 on the container
home_dir: /var/lib/tmp             # Where the root directory is setup on the container host
</code></pre>



# Now you are ready to create templates and run reports

Linux flavor report -- https://github.com/redawg/containerreports/tree/main/roles/osreport

Ios version report  -- https://github.com/redawg/containerreports/tree/main/roles/networkverreport






