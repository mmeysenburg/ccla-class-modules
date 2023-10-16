---
layout: post
title: Bucket Sort
subtitle: Using OpenMPI in C++
tags: [OpenMPI,C++]
comments: false
---

This page describes an OpenMPI module we included in our C++ Data Structures and Algorithms course. At Doane, our computing majors on the software engineering track take a three-course series of programming courses:

1. CMP 145 Introduction to Programming and Problem-Solving, using Python,
2. CMP 146 Programming and Problem-Solving II, using Java, and
3. CMP 246 Data Structures and Algorithms, using C++.

This module is the capstone of the CMP 246 course.

In the course, we have two Bucket Sort assignments. The first is assigned in the searching and sorting module as a single-processor program. The second assignment is in the parallelism module and uses OpenMPI on a Slurm-managed Linux cluster. Both assignments are presented below, along with the slides and source code for the module.

---

## First assignment: uni-processor bucket sort

### Module materials

Here are the slides and sample code used in the CMP 246 searching and sorting module. 

[Searching and sorting slides](../assets/slides/searching-and-sorting.pptx)

[Searching and sorting code](../assets/code/bucket-sort-single/search-n-sort.zip)

### Assignment: bucket sort

In this assignment, you will implement a sorting algorithm that can sort faster than the general O(n lg n) speed limit normally associated with sorting algorithms. Here, we will take advantage of specific characteristics of the data in order to improve performance, with a *Bucket Sort* algorithm. We assume that we are sorting floating point numbers in the half-open range [0, 1\right), and use a probably counterintuitive algorithm to break the speed limit.

Here is an example. Suppose we are sorting an array containing 20 floating point numbers, like this:

```
[0.07, 0.90, 0.88, 0.45, 0.92, 0.13, 0.45, 0.61, 0.67, 0.87, 0.04, 0.23, 0.49, 0.08, 0.74, 0.76, 0.58, 0.63, 0.32, 0.75]
```

We will distribute these values into 10 "buckets", which are smaller arrays or lists. Bucket zero holds values in [0, 0.1), bucket one holds values in [0.1, 0.2), bucket two holds values in [0.2, 3), and so on. For this data, our buckets would look like this:

![Bucket Sort diagram](https://i.imgur.com/7kLfq3T.png)

Once the values have been distributed, we use an efficient sorting algorithm (such as quicksort) to sort the values in each bucket. Then, we copy all of the values from bucket zero into the original array, followed by all the values from bucket one, then all the values from bucket two, and so on.

The time complexity of bucket sort is O(n + k), where n is the size of the array, and k is the number of buckets.

The code for this module has three files to get you started, in the `2-Assignment` directory: `BucketSort.h`, `main.cpp`, and a Bash `makefile`. Follow the `TODO` instructions in `BucketSort.h` to complete the bucket sort algorithm. Once you have successfully implemented the sort, assuming you are in a Bash environment, execute the program with a command like this:

```
./bucketSort 24
```

The program in `main.cpp` will run your bucket sort and our quicksort algorithm for multiple tries, on arrays ranging in size from 256 to 2<sup>n</sup>, where n is the command-line parameter. The command shown caps the array size at 2<sup>24</sup> = 16,777,216. Your output should look something like this:

```
      n,          bs,          qs
     256, 9.59000E+03, 1.57000E+04
     512, 1.22000E+04, 3.13900E+04
    1024, 2.81600E+04, 6.83200E+04
    2048, 6.59400E+04, 1.53300E+05
    4096, 1.55220E+05, 3.37940E+05
    8192, 4.00620E+05, 7.31860E+05
   16384, 8.31300E+05, 1.59536E+06
   32768, 1.91067E+06, 3.37719E+06
   65536, 4.24669E+06, 7.25071E+06
  131072, 9.21209E+06, 1.49074E+07
  262144, 1.96070E+07, 3.19344E+07
  524288, 4.24025E+07, 6.73938E+07
 1048576, 9.05547E+07, 1.41614E+08
 2097152, 1.91785E+08, 2.98944E+08
 4194304, 4.24054E+08, 6.19485E+08
 8388608, 8.69273E+08, 1.27820E+09
16777216, 1.83088E+09, 2.75148E+09
```

In particular, your bucket sort column should show that your algorithm is faster than the quicksort!

---

## Parallelism module and second assignment

### Module introduction

In this module, we will introduce parallel computing, and look at several parallel programming examples using the OpenMPI system. You can run the code in Replit, on Doane's supercomputer, Onyx, on your own Linux machine, or in a Windows Subsystem for Linux on your PC. 

### Module materials 

Here are the materials for the module:

[OpenMPI slides, part 1](../assets/slides/openmpi-1.pptx)

[OpenMPI slides, part 2](../assets/slides/openmpi-2.pptx)

[OpenMPI slides, part 3](../assets/slides/openmpi-3.pptx)

[OpenMPI slides, part 4](../assets/slides/openmpi-4.pptx)

[Module source code](../assets/code/parallel-module/parallel-module.zip)

### Module assignment: parallel bucket sort

In this assignment, we will parallelize our bucket sort algorithm from the sorting module assignment. We will use six processors, like this:

![Parallel bucket sort processors](https://i.imgur.com/xUCbbJi.png)

Your parallel program should work like this:

Processor 0 is the root processor. It creates an array of 10,000,000 doubles in the half-closed range [0, 1), and divides them into five buckets (versus 10 from the previous sorting assignment). Then, the root processor sends the data from the five buckets to five sub-processors.
Processors 1 through 5, the subprocesses, each receive one bucket and place the values in a local array. Then, the subprocess sorts the local array, using an efficient sorting procedure. Finally, each subprocessor sends its local array back to the root processor.
Processor 0 receives the sorted arrays from the subprocessors, and appends them together back into the original array. The result should be a sorted version of the original array.

In the module's code repository, you will find the directory `5-assignment`. Inside the directory, you'll find these files:

`PA10.cpp` : Skeleton code for the parallelized bucket sort. Search for `TODO` in this file and complete the program.
`makefile` : MPI build instructions, executed via the `make` command.
`buckets.job` : SLURM batch job to run the code on a Linux cluster.


