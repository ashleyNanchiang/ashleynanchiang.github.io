---
layout: post
title: Parallelizing AES-128 Encryption
---

This project implements AES-128 Encryption and used Cuda and OpenMP to analyze the difference in performance. 
<!--more-->
## Structure
 + src: contains all of the source code
    - aes.h: the header file for all
    - aes_seq.cpp: sequential implementation main function
    - sequential.cpp: sequential functions
    - aes_cuda.cu: Cuda implementation main function
    - parallelized_cuda.cu: Cuda kernel and some functions
    - aes_omp.cpp: OpenMP implementation main function
    - parallelized_omp.cpp: OpenMP functions
    - aes_seq_optimized.cpp: optimized sequential implementation main function
    - sequential_optimized.cpp: optimized sequential implementation functions
    - utils.cpp: shared functions between all
 + data: contains the output and the python code for plotting
    - aes_seq_sizes_data.csv: runtime for different sizes in sequential implementation
    - aes_opt_sizes_data.csv: runtime for different sizes in optimized sequential implementation
    - aes_cuda_sizes_data.csv: runtime for different sizes in Cuda implementation
    - aes_cuda_threads_data.csv: runtime for different thread counts in Cuda implementation
    - aes_omp_sizes_data.csv: runtime for different sizes in OpenMP implementation
    - aes_omp_threads_data.csv: runtime for different thread counts in OpenMP implementation
 + scripts: shell scripts to compile and run the code
    - seq.sh: compile and runs sequential implementation
    - cuda.sh: compiles and runs Cuda implementation
    - omp.sh: compiles and runs OpenMP implementation
    - seq_opt.sh: compiles and runs optimized sequential implementation
    - run_all.sh: compiles and runs all
 + test: some test code to evaluate the correctness of the algorithm
    - test_seq.cpp: tests sequential implementation
    - test_cuda.cu: tests Cuda implementation
    - test_omp.cpp: tests OpenMP implementation
    - test_seq_opt.cpp: tests optimized sequential implementation
 + build: the executables
    - aes_seq
    - aes_cuda
    - aes_omp
    - aes_opt

## Prerequisites

1.	This is compiled and run on the College of Engineering Euler cluster. 
2.	Run these commands to load these modules first: 
    ```
    module load gnu15/15.1.0
    module load openmpi5/5.0.8
    module load nvidia/cuda/13.0.0
    ```
3.	Make sure the current directory is at the root of this repo, i.e. `repo759\FinalProject\`

## Compile
There are two ways to compile, one is individually and the other is to compile all at once.
1.	Individual compilation: use the individual shell scripts.
    ```
    sbatch scripts/seq.sh -c
    sbatch scripts/cuda.sh -c
    sbatch scripts/omp.sh -c
    sbatch scripts/seq_opt.sh -c
    ```
    + Add the flag “-c” at the end. It will delete the previous build and compile a new one in the build folder.

2.	Compile all at once: use run_all.sh
    ```
    sbatch scripts/run_all.sh -c
    ```
    + Add the “-c” flag at the end to compile all. This also deletes all previous builds and compile new ones in the build folder.

## Execution
There are different ways to execute the code. Using the shell scripts, the code can be executed individually, together, or multiple rounds with varying input.

1.	Individually execute one implementation once
    + Sequential and Optimized sequential implementations only need at most 3 arguments
        ```
        sbatch scripts/seq.sh -r [plaintext size] [seed] // seed is for the random generator and is optional
        sbatch scripts/seq_opt.sh -r [plaintext size] [seed]
        ```
    + Cuda and OpenMP implementations need at most 4 arguments
        ```
        sbatch scripts/cuda.sh -r [plaintext size] [threads per block] [seed] // seed is optional
        sbatch scripts/omp.sh -r [plaintext size] [number of threads] [seed] // seed is optional
        ```

2.	Individually execute one implementation multiple times with different arguments
    + Sequential and Optimized sequential implementations can be run multiple rounds with varying plaintext sizes (hardcoded: size goes from 2<sup>10</sup> to 2<sup>30</sup> bytes).  
    Must specify seed.
        ```
        sbatch scripts/seq.sh -s [seed] [rounds] // rounds mean how many times you want to loop through the various sizes
        sbatch scripts/seq_opt.sh -s [seed] [rounds]
        ```

    + Cuda and OpenMP implementations can be run multiple rounds with varying plaintext sizes and thread counts  
        Must specify seed
        ```
        sbatch scripts/cuda.sh -s [thread per block] [seed] [rounds]
        sbatch scripts/cuda.sh -t [plaintext size] [seed] [rounds]
        sbatch scripts/omp.sh -s [thread count] [seed] [rounds]
        sbatch scripts/omp.sh -t [plaintext size] [seed] [rounds]
        ```

3.	Execute multiple implementations multiple times with different arguments
    + All can be run multiple rounds with varying sizes (2<sup>10</sup> to 2<sup>30</sup> bytes)
        - The seed is set to be 204863, the thread count is 256 for Cuda and 20 for OpenMP
        ```
        sbatch scripts/run_all.sh -s [rounds]
        ```

    + Cuda and OpenMP implementations can be run multiple rounds with varying thread counts
        - The seed is set to be 204863, the size is 2<sup>27</sup> bytes for both  
          For cuda, thread count runs from 2<sup>0</sup> to 2<sup>10</sup>  
          For OpenMP, thread count runs from 1 to 20
        ```
        sbatch scripts/run_all.sh -t [rounds]
        ```
    + This is no different than 2., just saves time by only typing in one command




