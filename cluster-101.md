1.  mailing list

    Everyone is subscribed to the engaging1-users@mit.edu mailing list at the time of account creation. This is how you will be notified about things such as system downtime, outages, or anything else that affects the cluster as a whole.

2.  Logging in

    To login to a centos 6 environment
    
    ssh -F ~/engaging-cluster/linux/config eofe4.mit.edu  
    or
    ssh -F ~/engaging-cluster/linux/config eofe5.mit.edu

    To login to a centos 7 environment

    ssh -F ~/engaging-cluster/linux/config eofe7.mit.edu

3.  directories

    /home/USER_NAME - working space for source code, scripts,hand-edited files etc, 100G quota, backed up regularly
    /nobackup1/USER_NAME - lustre parallel file system for parallel I/O, no quotas, no backups
    /pool001/USER_NAME - NFS file system if you need it for extra storage, 1TB quota

4.  software
    
    Rather than only installing the latest versions of certain programs, the engaging cluster utilizes environmental modules. This allows you to have access to multiple libraries and compilers, rather than just one default. 

    To see what modules are available to use type:

        module avail

    To see which modules are loaded:
    
        module list

    To add modules

         module add <modulename>

    To unload module

         module unload <modulename>

5.  scheduler 

    The cluster utilizes a resource manager called Slurm. Slurm divides groups of computers or "nodes" into seperate "partitions" which are essentially different queues pointing to different sets of nodes.  Some basic commands are:

* Running jobs

    There are two ways to run jobs on the cluster:

        - Submitting a script with sbatch 
        - Interactively with srun
    
    1. sbatch

        To run a job with sbatch you will need to create an sbatch script. This is comprised of 3 main parts which must be in the following order.

        - #!/bin/bash  

          This should be your first line, this declares your script as a bash script
        
        - #SBATCH lines   

          While these are technically just bash comments, slurm will read them as parameters for your script, you can have multiple lines of #SBATCH parameters. These will let you request certain resources or partitions for your jobs.

        - The code you are actually running 

            
        An example sbatch script may look something like this (note that %j will sub your job ID)

<code>
#!/bin/bash 
#SBATCH -n 1 # Number of cores 
#SBATCH -N 1 # Ensure that all cores are on one machine 
#SBATCH -t 0-00:05 # Runtime in D-HH:MM 
#SBATCH -C centos7 #Ensure that job only runs on centos7 machine
#SBATCH -p sched_any_quicktest # Partition to submit to 
#SBATCH --mem-per-cpu=4000 
#SBATCH -o output_%j.txt # File to which STDOUT will be written
#SBATCH -e error_%j.txt # File to which STDERR will be written
#SBATCH --mail-type=ALL # Type of email notification- BEGIN,END,FAIL,ALL 
#SBATCH --mail-user=foo@foobar.com # Email to which notifications will be sent  
</code>

> #SBATCH -n 1 # Number of cores 

This requests a certain number of cores, make sure your tool can use multiple cores before requesting them!

> #SBATCH -N 1 # Ensure that all cores are on one machine 

This requests that all of the requested resources be on one machine, unless you are using MPI or something similar it is recommended to not request more than -N 1 as it may negatively impact your jobs completion time.

> #SBATCH -t 0-00:05 # Runtime in D-HH:MM 

This sets a time limit for your job, this defaults to the partition's time limit, if you request more time than the partition's time limit your job will never run! This flag is most useful for when there is an upcoming slurm reservation. For example, if there is a reservation set for 7 days from now and your partition has a default time limit of 30 days then your job will not run unless you set the time limit since they will all be looking for nodes with 30 days of free resources! If you set the time limit to something like 6-00:00 then you have a much better chance of your job running before the reservation starts. 

> #SBATCH -C centos7 #Constraint flag that ensures the job only runs on centos7 machine

Some partition have nodes with both centos6 and centos7, many times mixing the OSs will cause errors with code compiled on one or the other. It is highly recommended to request the OS you are running from. If you need centos6 nodes you should use -C centos6 and use -C centos7 for centos 7.

> #SBATCH -p sched_any_quicktest # Partition to submit to 

This flag lets you specify which partition you want to run your job on, different partitions have different features and max run times, as well as different amounts of resources, choosing the proper partition is important as your job may take longer to run on some based on what you are asking, and sometimes your job may never run! If you request more nodes than a partition has, or a longer run time than the max your job won't start at all so keep this in mind!

> #SBATCH --mem-per-cpu=4000  
This flag lets you set how much memory to request per cpu core, the default is 3000 MB, if your job requires more memory than you request then your job will fail. Note that if you request absurdly large amounts amounts of memory your job may sit in the queue for a long time waiting for the resources to become available. Estimating your job's resource needs as accurately as possible is key to getting your jobs run in a timely manner.

> #SBATCH -o output_%j.txt # File to which STDOUT will be written
> #SBATCH -e error_%j.txt # File to which STDERR will be written
> #SBATCH --mail-type=ALL # Type of email notification- BEGIN,END,FAIL,ALL 
You can configure mail notifications when the state of your job changes, the above flags will let you decide when to receive an email
> #SBATCH --mail-user=foo@foobar.com # Email to which notifications will be sent  
This flag is fairly straight forward, pick an email you want to receive slurm mail from.

    
    2. srun 
	srun is generally only used if you need to participate in your job while it is running. If you don't need to watch,type,or click something in your job while it is running, then you should consider using sbatch. 

Note that srun does not use a file for input but rather takes command line arguments:

     srun -n1 -N1 -C centos7 -p sched_any_quicktest "echo hello world"


* Checking on Jobs

    1. sinfo 
       
        > sinfo -T

            sinfo -T allows you to see which nodes are reserved for various reasons and for how long. You will not be able to request a job on these nodes while the reservation is active. Also, you will not be able to run jobs that will not finish before this reservation starts.

        > sinfo -p <partitionname> 

          sinfo -p will let you see all of the nodes in a certain partition 
          Nodes listed as down and/or drain cannot be used to run jobs.
    2. scontrol
   
       > scontrol show partition

	scontrol show partition will let you see which partitions you have access to, as well as which nodes are in them and the max time limit jobs can run for.

       > scontrol show jobid <jobid>
	
	  this will let you see the status of your job as well as the parameters you ran it with

    3.  squeue
    
 
	squeue -u <username> lets you see all of the jobs that a certain user is running or waiting to run.
	squeue -j <jobid> lets you look up the status of a job by jobID
        squeue -p <partitionname> lets you look up all of the jobs running or waiting to run by on a partition
        squeue -u <username> --start will let you see aproximately when your pending jobs will start running.

	JobReasons
	(Resources) - Job is waiting for compute nodes to become available
	(Priority) - Jobs with higher priority are waiting for compute nodes.  
	(ReqNodeNotAvail) - The compute nodes requested by the job are not available, might be due to things such as cluster maintenance, nodes are down/offline, scheduler is backed up with too many jobs.

    4. sacct

	sacct -j <jobid> --format=JobID,JobName,Elapsed,State    get information on your job that has completed
	sacct -u <username> --format=JobID,JobName,Elapsed,State  get information on all your jobs that have completed


6.  useful tutorials http://www.tchpc.tcd.ie/node/74

 
