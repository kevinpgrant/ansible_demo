# ansible_demo
a demonstration of ansible playbooks

To run first check you have got the IP address of your Vagrant Host (or cloud machine etc) set correctly in host_vars e.g. in `host_vars/dev_jenkins` set `10.0.0.20` to your machine address

Also check any other settings in the group_vars such as ssh key file, username etc

`ansible_ssh_private_key_file: ~/.ssh/id_rsa`

The comments in the group_vars show how you might need to connect to a machine via a bastion if it is located in a private subnet, but you can ignore if this is not relevant to this situation


To configure the VM to run Jenkins, run the command `ansible-playbook playbooks/playbook.yaml -i inventory -l dev_jenkins`

This will setup the various files and folders on the machine, as well as updating apt and making sure NTP is installed and enabled.

Second step is to decide if you want to extend the playbook with a handler (which runs the docker-compose up -d command whenever the docker-compose.yaml is changed) OR if you want to start it manually

For the purpose of this exercise I have shown the latter, using the other ad-hoc command mode of ansible to perform this step. To launch Jenkins, you can run the ammand `ansible dev_jenkins -m shell -a "cd /opt/jenkins; docker-compose up -d" -b -i inventory`

This will connect to the remote VM and manually start the docker compose stack.


As a convenience, a Makefile is also included, you can use `make jenkins` to initialise the Jenkins CI service also, perhaps replacing direct use of docker-compose in the above command, so you can recreate the same environment locally. It also allows for seeding jobs, and other tasks, which could also be incorporated as handlers e.g. if you wanted to install plugins to run at startup in /var/jenkins_home/init.groovy.d in the container, and have it down/up jenkins to reload them on change (not shown)