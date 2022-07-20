#openmc #workshops #education

OpenMC workshop instance setup
	  - will need updated version of scripts from Paul
		  - https://github.com/paulromano/openmc-workshop-tools/tree/nea-course
		  - pem for instances, rsa for proxy?
	  - get setup working with `boto3`
		  - `EC2_HOME`, `EC2_AMITOOL`
		  - create security group, open port 8888 (w/ TCP)
		  ![[Pasted image 20220609102802.png]]
	  -  `launch_instances.py`
		  - change keypair and security group
		  - first arg is url name
		  - second is number of instances
	  - `add_hostnames.py`
		  - adds tags to instances
	  - `start_nb_servers.py`
		  - run jpyter in each instance and exit
	  - `update_proxy.py`
		  - configure external IPs to instances
		  - send public key to Paul


Jupyter lab password: openmc2022