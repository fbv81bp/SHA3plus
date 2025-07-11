# Intro
When the cryptocurrency Ravencoin introduced the X16R proof of work based on the ever changing combination of 16 SHA3 finalist hashes, i saw, that this construct might be FOGA prone over ASICs, if enough time was given to re-synthesize the newest combinatiom every time. Since Bitcoin's specification says, that the 6th last block is what can be considered stable, i'd suggest to take the -12th one to also have sufficient time to forge new chip configurations. However X16R has proven to be possible to be made in ASICs, as it only increased a one time manufacturing cost for each chip, making production economically viable, as the currency's value increased. Then they had to switch to X16Rv2 by adding a 17th hash to the group, which actually probably didn't solve anything, but the currency plummeted eversince, so it looks like there's no sense making new ASICs...

Two takeaways:
1) a PoW, whose architecture changes substantially over time is costly to make in ASICs - yet not impossible, because...
2) even such a hash has to consume more power in ASICs as in FPGAs, otherwise the increasing value of the cureency will make ASICs economically viable sooner or later

# Story

At first i made the same mistake as Ravencoin's designers, by taking SHA3 and adding a full blown 1600 bit permutation additionally to its Rho-Pi permutation stage. This should have increased ASIC manufacturing costs only, because FPGA is the mature construct to handle such a complex wiring topology, so hopefully ASICs won't possibly compete in mnaufacturing price. Still there was the power consumption, where they can still outperform FPGAs even if made and sold at the same price... Furthermore inhad to avoid the option of making multiple single bit SHA3 engines calculating with several different nonce bit by bit. This could allow for a very efficient implementation of the permutation by loading all the states of these hashes into a RAM, and doing the permutations bit by bit, yet for multiple instances of hashes. The first one, the power consumption can be addressed by making the changing random permutation's width smaller than the 1600 but state, and taking just a handful of wires to permute. This way the ASIC designer is forced to keep an 1600 bit wide crossbar in their design. Crossbars cost O(n²) power where n is the width of the permutation. Addressing the RAM based serialized approach was done by placing the random smaller permutation after the Chi function and before the Theta. This way any Theta intermediate parity bit requires the retreival of 50 Chi input bits, especially if the Chi-s are considered as S-boxes and are also shuffled for each new PoW calculation.

This all resulted in a generic PRNG construct, having a similar 3D state like the SHA3, in whose sheets parallel to its front, horizontal and vertical calculations are taking place. Such, that either a full horizontal row or a full vertical column is needed to be able to perform the calculation. Multiplication based hashes are so.

Also, the power consumption of the permutation are must play a significant role, so fast hashes with low powerconsumption are necessary.

Then the ever changing randomized partial permutation of the state takes place in depth, perpendicular to the sheets. This permutation also plays a significant role in making the RAM addressing based permutation impossible, because it multiplies the necessary bandwidth taken to calculate any and every sheet's bits. Namely the vertical hash's minimal bit width and the that ofvthe horizontal's would multiply if sheets could be processed indelendently. This way this multiple reprensenting the sheet size gets further increased by as many input sheets will be required for the computation of any output sheet.

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

6) by tuning the size of the randomly selected permutation, the ~200x bandwidth based power consumption, and the 16000x crossbar power consumption can be properly adjusted, however the state size may need to be increased if the mentioned wire collisions start to become significant: the state design may need a deeper shape in the number of sheets afterall
  
# Another example
A state if 32x32x64 bit shape can easily sustain a permutation of 1024 bits with just around 1% wires colliding into the same input hashes, raising bandwidth power consumption with RAM based bitwise implementation tom1000x. While (65536/1024)²~4000x the power consumption of the ASIC crossbar compared to that of an FPGA's.

# Summary
Watever the exact ASIC advantage may be, the structure can be addressed to eliminate its economical advantage in power consumption too, not just in one time manufacturing costs.
