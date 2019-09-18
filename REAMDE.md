(% class="row" %) ((( (% class="col-xs-12 col-sm-8" %) ((( (%
style="text-align: justify;" class="lead" %) Nowadays many problems are
so complex and time consuming that it is unreasonable to solve them on a
single computer with limited computer memory. Therefore, parallel
computing is used now extensively in a different kind of applications.

(% style="text-align: justify;" class="lead" %) Parallelization of
computing calculus is the separation process of a computational problem
into parts that can be solved contemporaneously. This requires the use
of multiple compute resources and an overall control mechanism. By
computer resources we understand a single computer with multiple cores
or number of such computers connected by a network, so-called
High-Performance Computing Cluster (HPCC). HPCC is the computing system
platfrom which supports of use of parallel processing for running
advanced application programs efficiently and quickly.

\= Multiprocessing in Python =

(% style="text-align: justify;" %)
[Python\>\>https://www.python.org/](Python\>\>https://www.python.org/)
is a general purpose high-level programming language which became
nowadays one of the most popular due to it's easy but powerful
programming concept. In Python, the multiprocessing module includes a
very simple and intuitive application programming interface for dividing
work between multiple processes.

(% style="text-align: justify;" %) In order to understand parallelism we
consider a [For loop
example\>\>https://stackoverflow.com/questions/50979373/using-mpi4py-to-parallelize-a-for-loop-on-a-compute-cluster/51318100](For%20loop%20example\>\>https://stackoverflow.com/questions/50979373/using-mpi4py-to-parallelize-a-for-loop-on-a-compute-cluster/51318100)
with Python multiprocessing module
[mpi4py\>\>https://mpi4py.readthedocs.io/en/stable/](mpi4py\>\>https://mpi4py.readthedocs.io/en/stable/)
for calculating the sum of numbers from a=1 to b = 1000000. In MPI for
Python, (% style="color:\#000000" %)MPI.Comm(%%) is the base class of
communicators.

{{code language="python"}} from mpi4py import MPI

comm = MPI.COMM\_WORLD \# communicator object containing all processes
rank = comm.Get\_rank() \# rank of this process, id number given to
process size = comm.Get\_size() \# number of processes in communicator

a = 1 b = 1000000 perrank = b//size summ = numpy.zeros(1) comm.Barrier()
\# synchronization. Each task blocks until all tasks reach a Barrier()
call start\_time = time.time() temp = 0 for i in range(a +
rank\*perrank, a + (rank+1)\*perrank): \# the for loop will execute in
every node temp = temp + i

summ\[0\] = temp if rank == 0: total = numpy.zeros(1) else: total = None

comm.Barrier() comm.Reduce(summ, total, op=MPI.SUM, root=0) \# collect
the partial results and add to the total sum stop\_time = time.time()

if rank == 0: \# In the end node with rank zero will add the results
from all the nodes for i in range(a + (size)\*perrank, b+1): total\[0\]
= total\[0\] + i print ("The sum of numbers from 1 to 1 000 000: ",
int(total\[0\])) print ("time spent with ", size, " threads in
milliseconds") print ("-----", int((time.t