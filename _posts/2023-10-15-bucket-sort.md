---
layout: post
title: Bucket Sort
subtitle: Using OpenMPI in C++
tags: [OpenMPI,C++]
comments: false
---

**Note**: in our C++ Data Structures and Algorithms course, we had two Bucket Sort assignments. The first was assigned in the searching and sorting module as a single-processor program. The second assignment was in the parallelism module and used OpenMPI on a Slurm-managed Linux cluster. Both assignments are presented below.

---

## First assignment: uni-processor bucket sort

[Searching and sorting slides](../assets/slides/searching-and-sorting.pptx)

[Searching and sorting code](../assets/code/bucket-sort-single/search-n-sort.zip)

In this assignment, you will implement a sorting algorithm that can sort faster than the general $O\left(n \lg n\right)$ speed limit normally associated with sorting algorithms. Here, we will take advantage of specific characteristics of the data in order to improve performance, with a *Bucket Sort* algorithm. We assume that we are sorting floating point numbers in the half-open range $\left[0, 1\right)$, and use a probably counterintuitive algorithm to break the speed limit.

Here is an example. Suppose we are sorting an array containing 20 floating point numbers, like this:

```
[0.07, 0.90, 0.88, 0.45, 0.92, 0.13, 0.45, 0.61, 0.67, 0.87, 0.04, 0.23, 0.49, 0.08, 0.74, 0.76, 0.58, 0.63, 0.32, 0.75]
```

We will distribute these values into 10 "buckets", which are smaller arrays or lists. Bucket zero holds values in $\left[0, 0.1\right)$, bucket one holds values in $\left[0.1, 0.2\right)$, bucket two holds values in $\left[0.2, 3\right)$, and so on. For this data, our buckets would look like this:

![Bucket Sort diagram](https://i.imgur.com/7kLfq3T.png)

Once the values have been distributed, we use an efficient sorting algorithm to sort the values in each bucket. Then, we copy all of the values from bucket zero into the original array, followed by all the values from bucket one, then all the values from bucket two, and so on.

The time complexity of bucket sort is $O\left(n + k\right)$, where $n$ is the size of the array, and $k$ is the number of buckets.

The code for this module has three files to get you started: `BucketSort.h`, `main.cpp`, and a Bash `makefile`. Follow the `TODO` instructions in `BucketSort.h` to complete the bucket sort algorithm. Once you have successfully implemented the sort, assuming you are in a Bash environment, execute the program with a command like this:

```
./bucketSort 24
```

The program in `main.cpp` will run your bucket sort and our quicksort algorithm for multiple tries, on arrays ranging in size from 256 to $2^n$, where $n$ is the command-line parameter. The command shown caps the array size at $2^{24} = 16,777,216$. Your output should look something like this:

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
