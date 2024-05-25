# Hash Hash Hash

A simple C program that implements two locking strategies for multi-threads in a hash table implementation, which ensures concurrence satety.

## Building
```shell
make
```

## Running
```shell
./hash-table-tester -t <number of threads to use> -s <number of hash table entries to add per thread>
```

## First Implementation
In the `hash_table_v1_add_entry` function, I implemented a single mutex and placed a locking call at the function's start, unlocking it just before the function exits. By treating the entire function as a critical section, I ensured that only one thread could modify the hash table at any given time. This approach effectively prevents data races when multiple threads attempt to modify the same shared memory simultaneously.

### Performance
```shell
./hash-table-tester -t 8 -s 50000
# Generation: 287,356 usec
# Hash table base: 1,469,998 usec
# - 0 missing
# Hash table v1: 2,764,231 usec
# - 0 missing
```
Version 1 is a little slower than the base version. As a thread tries to acquire a mutex lock that is already held by another thread, it must wait until the lock is released. This situation forces the operating system's scheduler to perform an context switch between threads, which is a highly time-consuming process.

## Second Implementation
In the `hash_table_v2_add_entry` function, I created a mutex for each bucket within the `struct hash_table_entry` and initialized these mutexes in the `hash_table_v2_create()` function. This way, in the `hash_table_v1_add_entry` function, we treat only the bucket being modified as the critical section by locking the mutex of that specific bucket and unlocking it before returning.

### Performance
```shell
./hash-table-tester -t 8 -s 50000
# Generation: 287,356 usec
# Hash table base: 1,469,998 usec
# - 0 missing
# Hash table v1: 2,764,231 usec
# - 0 missing
# Hash table v2: 2,701,480 usec
# - 0 missing
```

Despite the optimization efforts in v2, it didn't achieve the expected performance gains and remains slower than the base version, though slightly better than v1.

## Cleaning up
```shell
make clean
```
