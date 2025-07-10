# Design
- we best need a cube shaped state with side length N, and N³ number of bits
- let's suppose N=32, so state size is 32768 bits
- then choose two dufferent hash//prng functions that are based on at 64 bit multiplications, for these require all input bits to be present at calculatiom time
- suppose there is a front sheet and 31 sheets behind it, then the hash computations are all done within these sheets, horizontal with hash1 and vertical with hash2: this will have dire implications on memory bandwidth in case of a time serial bitwise implementation of the permutation described next
- chose a random 256 bits subset of the 32768 bis state and permute these bits based around the cryptocurrency's -12th block hash
- place this permutation inbetween the first (horizontal) and second (vertical) hash calculations
- the hashes have to be fast and cheap, so that the power consumption of the permutation counts too

# Rationale
1) the hashes 1 and two being aligned horizontally and vertically multiplies the number of bits to be pre-read for computation in a bitwise time serial RAM based permutation, meaning at least a whole sheet should be calculated simultaneously ie. 1kbits at a time, which should be even more difficult knowing that the permutation is switching between subsequent sheets
2) if the horizontal hashes are followed by the permutations of 256 bits, these can lead back to any of the 1024 horizontal hashes due to random distribution, when trying to calculate all vertical ones by addressing the RAM bits according to the current permutation
3) yet some of these wires are going to "collide", needing the output of the same horizontal hash output: there will 32 such collisions on average when picking 256 wires out of 1024 possible origins, resulting in about a 200 fold multiplication of the sheetwise calculation
3.1) this means each of the 32 sheet column input bits needs the calculation of a 32 bit wide row hash, but there are 200 possible rows one particular sheet can originate at
3.2) meaning 200x32x32~200kbit havimg to be read and stored in registers for any of the 32 sheets' RAM based calculations
4) this leads to 200 times the bandwidth as would be needed without the permutation

5) designing the permutation with a crossbar requires 32768² number of switches in an ASIC to allow for all possible combinations, while.only a 256 input and output crossbar in the FPGA, taking (32768/256)²~16384 times more power consumption, and btw. one time chip surface manufaturing costs too

6) by tuning the size of the randomly selected permutation, the ~200x bandwidth based power consumption, and the 16000x crossbar power consumption can be properly adjusted, however the state size may need to be increased if the mentioned wire collisions start to become significant
  
