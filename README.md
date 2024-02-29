# Hash Hash Hash
TODO introduction

This code is a hash table implementation that is safe to use concurrently. There are two versions of the implementation that I added (on top of the base implementation and skeleton code given to me by the CS111 Instructors), v1 and v2. v1 uses only one mutex lock (named lock), and is, as a result, slower than the base code given. However, v2 uses a seperate lock for every entry, and is, as a result, faster than the other two implementations.

All testing was done on the seas lnxsrv15 (lnxsrv.seas.ucla.edu)

## Building

To build the program, run make in your terminal (while within the folder containing all necessary files), as shown below. 

```shell
make
```

## Running
To run the program, you can use the command ./hash-table-tester -t a -s b
When doing this, replace a with the number of threads you want to test with. Replace b with the number of hash table entries you would like to have per thread. An example with actual values is below

```shell
./hash-table-tester -t 8 -s 50000
```
An example output would be as follows (not from my code, but taken from the spec):
Generation : 130,340 usec
Hash table base: 1,581,974 usec
- 0 missing
Hash table v1: 359,149 usec
- 28 missing
Hash table v2: 396,051 usec
- 24 missing

  Actual outputs I got while running my code are below.

## First Implementation
In the hash_table_v1_add_entry function, I added a single lock around all of the code within hash_table_v1_add_entry. This included the hash_table_entry, list_head, and the list_entry, as well as the actual addition of the entry to the list. A single global lock was created and locked, and then unlocked after the entry to the list was created and added. Afterwards, in hash_table_v1_destroy, the lock was destroyed (in addition to the hash table).

### Performance

As directed in the Detailed Rubric, the same test was run three times. While this technically also output results for v2, those results are omitted until later in this document in the v2 section for clarity
```shell
./hash-table-tester -t 8 -s 50000
Generation: 101,172 usec
Hash table base: 1,495,525 usec
  - 0 missing
Hash table v1: 1,895,027 usec
  - 0 missing



./hash-table-tester -t 8 -s 50000
Generation: 69,452 usec
Hash table base: 1,530,193 usec
  - 0 missing
Hash table v1: 2,173,779 usec
  - 0 missing


./hash-table-tester -t 8 -s 50000
Generation: 72,439 usec
Hash table base: 1,376,678 usec
  - 0 missing
Hash table v1: 1,936,437 usec
  - 0 missing




```
Version 1 is a little slower than the base version. As there is only one lock for the implementation to use, each thread must wait their turn to use the same lock. This creates a bottleneck, where the threads are "waiting their turn" to use the singular lock, as only one thread can use the lock at a time. 
However, while it is slower, as you can see, it never missed any entries.

## Second Implementation
In the `hash_table_v2_add_entry` function, I added a lock for every single entry in the table. This lock was initalized in hash_table_v2_create, which meant that a new one was made for every entry. In hash_table_v2_add_entry, the lock is locked after the entry is accessed and then unlocked again after adding the new entry to the list. Finally, in hash_table_v2_destroy, all of the locks are destroyed as every one of the entries is destroyed, ensuring none of them survive. 

### Performance
As directed in the Detailed Rubric, the same test was run three times. While this technically also output results for v1, those results are omitted for clarity, as they were already depicted above.

```shell
./hash-table-tester -t 8 -s 50000
Generation: 101,172 usec
Hash table base: 1,495,525 usec
  - 0 missing
Hash table v2: 534,824 usec
  - 0 missing
./hash-table-tester -t 8 -s 50000
Generation: 69,452 usec
Hash table base: 1,530,193 usec
  - 0 missing
Hash table v2: 538,897 usec
  - 0 missing
./hash-table-tester -t 8 -s 50000
Generation: 72,439 usec
Hash table base: 1,376,678 usec
  - 0 missing
Hash table v2: 439,760 usec
  - 0 missing

```

TODO more results, speedup measurement, and analysis on v2

v2 is obviously a lot faster than the Base (and v1 as a result). By creating a lock for every single entry in the list, we avoid the bottleneck created with v1. As a result, we have much increased speeds as the threads are able to run as fast as they want without waiting for a single lock to use in the implementation. 
As you can see, it also never missed any entries.

## Cleaning up
To clean up any unecessary files, we can run make clean in the terminal, as depicted below.
```shell
make clean
```
