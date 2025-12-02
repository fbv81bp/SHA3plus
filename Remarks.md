## The Problem
### There is no way of prohibiting the permutation being done separately in a bitwise manner, as long as vertical and horizontal hashes are computed in separate steps.

1) horizontal hashes
2) permutation-1
3) vertical hashes
4) permutation-2
5) repeat

So this is not going to increase memory bandwidth. Albeit the goal should be that for each bit read from memory and computed in any hash a myriad of other random bits had to be re-read again and again. This might be achieved by hashes and permutations being applied alternatively in a very fine grain multiple step manner:

1) one next hash, but just a single one
2) perform the entire partial permutation of the state, not just parts of it, because otherwise not all hashes are going to be affected
3) repeat

One may have a large set of small partial permutations to be performed before each hash, so that permutations fit into such crossbars that are inherently embedded into FPGAs. 

## Other thoughts on earlier specs

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
