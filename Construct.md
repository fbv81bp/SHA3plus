- we best need a cube shaped state with side length N, and N³ number of bits
- let's suppose N=32, so state size is 32768 bits
- then choose two dufferent hash//prng functions that are based on at 64 bit multiplications, for these require all input bits to be present at calculatiom time
- suppose there is a front sheet and 31 sheets behind it, then the hash computations are all done within these sheets, horizontal with hash1 and vertical with hash2: this will have dire implications on memory bandwidth in case of a time serial bitwise implementation of the permutation described next
- chose a random 256 bits subset of the 32768 bis state and permute these bits based around the cryptocurrency's -12th block hash

  
