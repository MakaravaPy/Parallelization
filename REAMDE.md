Nowadays many problems are so complex and time consuming that it is unreasonable to solve them on a single computer with limited computer memory. Therefore, parallel computing is used now extensively in a different kind of applications.

Parallelization of computing calculus is the separation process of a computational problem into parts that can be solved contemporaneously. This requires the use of multiple compute resources and an overall control mechanism. By computer resources we understand a single computer with multiple cores or number of such computers connected by a network, so-called High-Performance Computing Cluster (HPCC).  HPCC is the computing system platfrom which supports of use of parallel processing for running advanced application programs efficiently and quickly.

# Multiprocessing in Python

[Python](https://www.python.org/) is a general purpose high-level programming language which became nowadays one of the most popular due to it's easy but powerful programming concept.  In Python, the multiprocessing module includes a very simple and intuitive application programming interface for dividing work between multiple processes.

In order to understand parallelism we consider a [For loop example](https://stackoverflow.com/questions/50979373/using-mpi4py-to-parallelize-a-for-loop-on-a-compute-cluster/51318100) with Python multiprocessing module [mpi4py](https://mpi4py.readthedocs.io/en/stable/) for calculating the sum of numbers from a=1 to b = 1000000.  In MPI for Python, MPI.Comm is the base class of communicators.

```python
from mpi4py import MPI

comm = MPI.COMM_WORLD  # communicator object containing all processes
rank = comm.Get_rank()          # rank of this process, id number given to process
size = comm.Get_size()           # number of processes in communicator

a = 1
b = 1000000
perrank = b//size
summ = numpy.zeros(1)
comm.Barrier()          # synchronization. Each task blocks until all tasks reach a Barrier() call
start_time = time.time()
temp = 0
for i in range(a + rank*perrank, a + (rank+1)*perrank):      # the for loop will execute in every node
    temp = temp + i

summ[0] = temp
if rank == 0:
    total = numpy.zeros(1)
else:
    total = None

comm.Barrier()
comm.Reduce(summ, total, op=MPI.SUM, root=0) # collect the partial results and add to the total sum
stop_time = time.time()

if rank == 0:                                   # In the end node with rank zero will add the results from all the nodes
    for i in range(a + (size)*perrank, b+1):
        total[0] = total[0] + i
    print ("The sum of numbers from 1 to 1 000 000: ", int(total[0]))
    print ("time spent with ", size, " threads in milliseconds")
    print ("-----", int((time.time()-start_time)*1000), "-----")
```

This program is run with the command:

-mpirun -n 4  python Example.py

where the mpirun can be substituted with mpiexec function, and -n 4 shows the number of CPUs we want to use.

As the result we will recieve the following:

`The sum of numbers from 1 to 1 000 000:  500000500000`  
`time spent with  4  threads in milliseconds ----- 29 -----`

Running the proposed example on 1 CPU, result will be:

`The sum of numbers from 1 to 1 000 000:  500000500000`  
`time spent with  1  threads in milliseconds ----- 99 -----`

# HPCC in PTB

In PTB we have the possibility to use [Linux-cluster](http://intranet.ptb.de/index.php?id=linux-rechencluster) with the login- head node- head node e00073 and 24 high memory nodes with feauture E52690v4. In order to have an access to the system a registration is requiered by using this [![Opens external link in new window](http://intranet.ptb.de/typo3/sysext/rtehtmlarea/res/accessibilityicons/img/external_link_new_window.gif)form](http://e00073.berlin.ptb.de/usage/Antrag_Version_0.95.doc).

As soon as you have the access to the cluster, you can run your job there using the shell command and a simple bash script runexample.sh.

```shell
#!/bin/bash
#MSUB -o mpiPy_test # defines the file-name to be used for the standard output
#MSUB -j oe # declares if the error stream of the job will be merged with the output stream of the job
#MSUB -l walltime=1:00:00:00 # wall-clock time. Default units are seconds
#MSUB -d /oneFS/daten841/user/ # specifies which directory the job should execute in
#MSUB -N test             # specifies the user-specified job name attribute

#MSUB -l nodes=1:ppn=1,feature=E52690v4 # number of nodes and number of processes per node

module load mpi/openmpi/3.0.0/gcc

### start of jobscript
cd $PBS_O_WORKDIR
echo "workdir: $PBS_O_WORKDIR"      # print out
NSLOTS=$(wc -l < $PBS_NODEFILE)
echo "running on $NSLOTS cpus ..."    # print out
echo date                                               # print out


/cluster/openmpi/3.0.0/gcc/bin/mpirun -np $NSLOTS -machinefile $PBS_NODEFILE --mca orte_base_help_aggregate 0   /oneFS/daten841/user/Example.py
```

Finally, submit it

`msub runtest.sh`


{{toc/}}
