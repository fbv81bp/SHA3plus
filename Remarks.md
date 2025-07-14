- the assumption of 200x memory bandwidth is wrong in this sense
- and the fewer the actually permuted bits, the more a bitwise RAM based solution is viable
- a separate permutation stage with single fan-out is always cheap in RAM
- what is needed is a high fan-out for the permutation stage, ie. hashing myriads of words together in some pattern that is easy with some hardware shifts and multiply-accumlate units, but causes bandwidth growth for the RAM
- the RAM based permutation is always a single operation unfortunatly - unleeess... maybe if multiple parallel permutations with high fan-outs are taken into account(?)
- the hashes after the permutations must compute longer than 32, like 192 bit inputs from the state
- have to look for sth that results in over linear bandwidth and/or RAM count if willing to perform bitwise calculations
- leveraging avalanche effect of multiple different small permutations through several 'inner' rounds can result in difficult mixing outcome(?) but that doesn't mean it can't be done in RAM in the same multiple steps...
### some likely solution
1) let's take a large paralell set of permutation pairs that all can be switched by multiplexers
2) these should be small permutations, but altogether they should cover most of the state, like about 70%< so that RAM addressing isn't generally affecfing just a part of the memory
3) use XOR-trees to drive addresses selectimg between multiplexers: this can be tuned so, that the whole RAM must be read several times more
4) all permutation inouts, outputs and connections are randomized as well as the XOR-tree inputs are too
5) the selected permutations are hashed together at the end with hashes that have wider inputs than outputs 
