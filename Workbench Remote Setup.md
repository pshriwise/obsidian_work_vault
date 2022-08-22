#nse #neams-workbench


**Note: SSH keys must be setup on the remote s.t. no password is required!!**

## Setup File

- Use setup file below and update:
	- runtime environment directory of Workbench
	- Application path (should point to custom version of PyARC w/ MCSim)
	- hostname
	- username
	- remote_execution_home
```
setup_properties {

runtime_command = "/software/Workbench/Workbench-4.0.0/rte/entry.sh /software/Workbench/Workbench-4.0.0/rte/arc.py"

application_path = "/home/pshriwise/opt/PyARC/PyARC.py"

hostname = "vtr5.ne.anl.gov"

username = "pshriwise"

remote_execution_home = "/home/pshriwise"

application_name = "arc_vtr5_pygriffin" % include the client and hostname to prevent collisions from other installations

upload_patterns = ["*.decay_chain.son" "*.isotxs" "*.lumped.son"] % using Python's glob syntax https://docs.python.org/2/library/glob.html

download_patterns = ["${BASENAME}*"] % using Python's glob syntax https://docs.python.org/2/library/glob.html

tail_file = "${BASENAME}.timeline.out"

version_number = "1.0.0"

scheduler_type = single_box

ssh_specs {

ssh_path = "/usr/bin/ssh"

ssh_options = "-Tq"

scp_path = "/usr/bin/scp"

scp_options = "-pCqr"

}

communication_type = ssh

}
```

## Workbench Steps

### Enable remote job submission
1. Add remote setup to configurations
	1. Click "File..." and "Configurations..."
	2. Click "Add..." and select "Setup_Remote"
	3. Click "Load Grammar" and wait
	4. Click "Apply" then "OK"
3. Open the setup file
4. Run this file with Workbench

## Configure an Application on the Remote
1. Click "File..." and "Configurations..."
2. In "Applications..." select the application name from the setup file above (`arc_vtr5_pygriffin`) and click "OK"
3. Click "Load Grammar" and wait
4. Click "Apply" then "OK"

## Running a Remote Application
The remote application can now be run by opening the desired input file and selecting the application name matching the one in the setup file _in the righmost drop-down box_, checking for validation errors, and hitting "Run"