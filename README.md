# TRUST
This is the code for a TPDS paper - TRUST: Triangle Counting Reloaded on GPUs [[PDF]](https://personal.stevens.edu/~hliu77/docs/tpds21.pdf)

## download
You can download the Zip file from 

    https://www.dropbox.com/s/g2onpn0nstddc8e/TRUST.zip?dl=0
## Organization
The code of TRUST has two version: without graph partition for small graph and without graph partition for large graph. Preprocess include the code for preprocessing. Following the step to test the TRUST.

## Environment 
CUDA Toolkit 10.2
g++ 7.4.0
MPICH-3.3

## Prerpocess
In Preprocessing step, use 

    $ cd Preprocess
    $ ./compile.sh 

compile the code and we will get three files: fromDirectToUndirece, preprocess and partition.
This corresponds to following three step:

1. **fromDirectToUndirect** transform the directed graph to undirected graph, delete the duplicate edges and self loops and remove orphan vertices. It take file name as input and will generate undirected graph `1.mmio`.
For example:

    `$ ./fromDirectToUndirect cit-Patents.txt`

The format of `cit-Patents.txt` should be edge list.

2. **preprocess** will do the orientation and reordering and generate the CSR format of graph. It will take `1.mmio` as input and generate two file `begin.bin` and `adjacent.bin`

3. **partition** use hash to partition the graph. To see detail of partition usage, you can read the `Dataset/Cit-Patents/partition.sh`

## Dataset
In folder `Dataset/Cit-Patents/` we give a example of download Cit-Patents graph and preprocessing it.
Run it by

    $ cd Dataset/Cit-Patents/
    $ ./get&preprocess.sh

For the large graph and input file only include edge list, we recommend use preprocess code in `Preprocess/speedupIO`.

For partition, run 

    $./partition.sh 2
    
There are one input arguments `n`, it represent the partition number, we will partition graph into `n*n` pieces

## Compile and Run code
For small graph, we don't partition the graph. 
Compile the code:

    $ cd Without-graph-partition/
    $ make

Run the code:

    $ mpirun -n 1 ./trianglecounting.bin ../Dataset/Cit-Patents/ 1 1024 1024 1

The input arguments is 
1. input graph folder 
2. number of GPUs
3. number of thread per block 
4. number of block 
5. chuncksize

The output arguments is
1. graph folder 
2. vertex count
3. edge count
4. triangle counts
5. times
6. TEPS rate

For the large graph, partition is required.

Compile the code:

    $ cd With-graph-partition/
    $ make

Run the code:

    $ mpirun -n 8 ./trianglecounting.bin ../Dataset/Cit-Patents/ 8 1024 1024 1 2


The input arguments is 
1. input graph folder 
2. number of GPUs should be m
3. number of thread per block 
4. number of block 
5. chuncksize
6. partition number `n`

The output arguments is
1. graph folder 
4. triangle counts
5. min times
6. max times

----
Reference
----

```
@ARTICLE {9373989,
author = {S. Pandey and Z. Wang and S. Zhong and C. Tian and B. Zheng and X. Li and L. Li and A. Hoisie and C. Ding and D. Li and H. Liu},
journal = {IEEE Transactions on Parallel & Distributed Systems},
title = {TRUST: Triangle Counting Reloaded on GPUs},
year = {5555},
volume = {},
number = {01},
issn = {1558-2183},
pages = {1-1},
keywords = {graphics processing units;two dimensional displays;partitioning algorithms;message systems;instruction sets;arrays;time complexity},
doi = {10.1109/TPDS.2021.3064892},
publisher = {IEEE Computer Society},
address = {Los Alamitos, CA, USA},
month = {mar}
}
```
