# A concept of an economically ASIC resistant and FPGA prone modification of the SHA3 hash function.

## It distilled down to the following architecture:
- crossbar capacitance and thus power consumption grows at O(n²) withbthe width of an arbitrary permutation
- SHA3 has 1600 wires between functions, but if only 100 is permuted randomly at every epoch between some stages, then it will be 256 times cheaper than a n 1600x1600 one
- this permutation can be synthesized for FPGAs at every mining epoch, while an ASIC desugner might be forced to apply the 1600bits crossbar
- to enforce this, the 100bits permutation needs to be placed after the Chi and before the Theta, while allnChi functions must be shuffled too - all this to avoid parallell calculations of bit serial implementations of the hash
- the PRNGs for calculating thevpermutation and shuffles must be initialized with a block hash 12 epochs old, and PRNGs of diverse math principles (like FNV, CRC, PCG) have to be applied ensure all of them can't be forecasted at the same implementation, neither do all reverse or weaken the Ro-Pi permutation's effect by accident
- and then as many as 1000ish rounds of a pipeline with 3..5 different permutations can be finalized with a standard, complete and unabridged hash at the end, just for the sake of security
- even if the previous construct should hopefully stay cryptographically secure, otherwise filthy mathematicians may find a way around to simplify it (which won't make ASICs more competitive, for the same trick will be uploadable to FPGAs too...)
- as for bit serial, RAM addressing based parallel computation of multiple hashes with different nonces, one has to keep addressing 50 input bits for every single theta parity output one after the other from the previous state, and keep them in a register, do so 320 times, resulting in >16'000 adressing for each round computation of the 1600 bits state

### Summary
- a wider state may increase the relative crossbar cost for ASICS quadratically
- the bit serial RAM addressimg based permutation can cost quadratical to the sheet edge size of the SHA3 state, ie. in which the horizontal Chi SBOXes and the vertical Theta XOR trees get calculated

## Results

According to power estimations by AI, this brings ASIC calculations costs roughly equal or worse to those of FPGAs. And there hasn't yet been a tuning of diffuculty based tweaking of the crossbars relative sizes, which is a game changer as it can increase the cost ratio between ASIC and FPGA mining quadratically proportional to a single simple algorithm parameter.

## Ideas
- the role of the SBOX is to multiply the number of bits to be addressed for every single output bit being calculated, so it would grow too large, like 16 bits or more, it might just be replacable by some small.and fast, not so secure hash or prng like murmur3, fnv1, crc², et. al., and probably.the goes for the XOR trees too, while the horizontal and verrtical computatioms being substantially different, is also nice to have
