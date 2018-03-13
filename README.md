ccess the machine loginicx
The server loginicx is the frontend for the Cluster ICE-X and it is not accessible direct from the internet. Go to devel2 and then login to loginicx machine. 

ssh loginicx

2) Setup the environment (EUPS)
Once you are into the loginicx machine then you need to setup the environment, open the file ~/.bashrc and put the lines below.
. /mnt/eups/linea_eups_setup.sh
setup openmpi

Reload the file running this command:
source ~/.bashrc

3) Compiling your MPI application

mpicc hello_world_with_mpi.c -o hello_world_with_mpi

4) Create the file machines
Create (or copy) a file called "machines", put the names of the nodes where you want to run your application. Make sure your application is running stable before occupy all nodes.
NODES: r1i0n0, r1i0n1, r1i1n0, r1i1n1

$ condor_status -master | cut -d ' ' -f1 | tail -n 4 > machines

5) Running your MPI application
To submit a job you can use one of the options below.

5.1) condor_run

condor_run mpirun -machinefile machines -np 90 hello_world_with_mpi

Understanding the executable above:

condor_run: to run with condor cluster system
mpirun: main mpi executable
-v: verbose, just to control what is happening
-x OPENMPI_DIR: This is a must, you must set up this variable, ALWAYS
--prefix $OPENMPI_DIR: This is a must, you must set up this prefix, ALWAYS
-np 4: The number of cpus
-machinefile machines: defining the file where you define which machines to use, do not forget to use only the resources that you need.
hello_world_with_mpi: the mpi application
5.2) condor_submit
Go to the directory where you mpi app lives and create a submit file and use the command condor_submit to send the job to the cluster. See the example below.

universe = vanilla
executable = /mnt/eups/packages/Linux64/openmpi/1.8.4+0/bin/mpirun
arguments = -machinefile machines -np 90 hello_world_with_mpi
should_transfer_files = yes
when_to_transfer_output = on_exit
transfer_input_files = monte_carlo_mpi,machines

error = condor.$(Process).err
output = condor.$(Process).out
log = condor.$(Process).log

queue

condor_submit hello_world_with_mpi.submit

