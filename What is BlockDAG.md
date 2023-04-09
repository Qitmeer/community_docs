# What is BlockDAG?

The Qitmeer network utilises a ***blockDAG*** structure as opposed to the traditional ***blockchain*** structure found in most popular Distributed Ledger Technology (DLT). Although the terms blockchain and blockDAG are often used interchangeably, they are entirely different data structures (they may retain some similar features as in Qitmeer’s case which will be discussed later on). 

## DAG - Directed Acyclic Graph

Before we delve into what blockDAG is, we need to address, what the “DAG” part stands for. DAG is an acronym for:

**D** - Directed

**A** - Acyclic

**G** - Graph

[DAG](https://en.wikipedia.org/wiki/Directed_acyclic_graph) is simply a directed graph containing nodes and edges which have no cycles. DAGs themselves are not a brand new concept and have already seen widespread applications in [biology](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC1995545/), [computer science](https://eprints.whiterose.ac.uk/167629/1/rtss2020_dag.pdf) and even the widely popular version control system - [Git](https://medium.com/girl-writes-code/git-is-a-directed-acyclic-graph-and-what-the-heck-does-that-mean-b6c8dec65059). 

![Fig 1. An example of a DAG](https://upload.wikimedia.org/wikipedia/commons/thumb/4/4b/Directed_acyclic_graph.svg/1024px-Directed_acyclic_graph.svg.png)

Fig 1. An example of a DAG

## Bitcoin Security-Scalability Problem

[Bitcoin](https://www.ussc.gov/sites/default/files/pdf/training/annual-national-training-seminar/2018/Emerging_Tech_Bitcoin_Crypto.pdf), one of the earliest and most popular forms of cryptocurrency has over the years proved to be [very secure](https://help.coinbase.com/en/coinbase/privacy-and-security/other/is-bitcoin-secure-has-the-bitcoin-network-ever-been-hacked) with the underlying security of the network remaining intact. However, whilst its greatest strength may lie in its security, this has come at a tradeoff when it comes to its scalability. 

Most notably:

- Bitcoin is slow…very slow with [5-7 TPS](https://academy.binance.com/en/glossary/transactions-per-second-tps) (Transactions Per Second)
- Block size is restricted to ≈1MB (post-Segwit [this has a practical limit of about ≈2MB](https://bitcoin.stackexchange.com/questions/98810/whats-the-blocksize-limit-after-segwit-and-how-do-legacy-nodes-deal-with-segwit))
- Block creation time is restricted to [1 block ≈ 10 minutes](https://www.researchgate.net/figure/Block-generation-times-in-Bitcoin-Transactions-are-confirmed-when-they-appear-in-valid_fig1_262315254)

Whilst these parameters might seem impractical, it is because of this impracticality that has made Bitcoin soo secure. In theory, by increasing the block size or creation time, the longer it will take for these blocks to propagate through the network. Since nodes may not have access to the most recent block on the network, they will continue mining on what they perceive is the current block. Once the PoW (Proof of Work) for this block has been completed, it would create a fork on the network (since you now have multiple blocks pointing to a single preceding block). 

As Bitcoin uses the [longest chain rule](https://learnmeabitcoin.com/technical/longest-chain), any blocks outside of this chain (such as those in forks) would become [stale](https://medium.com/@cryptobolts/what-are-stale-blocks-simply-explained-b445682274c6) and be rejected from the network. Introducing too many stale blocks would eventually degrade the security of the network. 

Essentially:

More/Larger Blocks → Slower Block Propagation → More Forks → Less Security

## BlockDAG

BlockDAG is a DAG whereby the nodes represent the blocks and the edges represent references to its predecessor blocks. Unlike blockchain, where a single block is created within a certain time period, a blockDAG is able to create multiple blocks concurrently enabling greater TPS. Moreover, in a blockchain, a block can have only one single reference to its predecessor whereas in a DAG, a block can reference multiple predecessor blocks. This has the added effect of being able to determine which blocks were created honestly and which ones were created by a malicious adversary.

![Fig 2. Example of a blockDAG](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*YJgJTzHlnrXrDU_ddsWtAA.png)

Fig 2. Example of a blockDAG

At the end of the day, blockDAG is merely a way of representing blocks and does not address other pressing matters such as how to deal with double-spending or the overall ordering of the blocks. For this, Qitmeer has adopted a hybrid consensus protocol combining both the SPECTRE and the GHOSTDAG protocol to create what it calls the *MeerDAG* consensus **protocol. This in summary addresses the double spending issue as well as provide linear ordering of the blocks.

A more detailed explanation on blockDAG and MeerDAG can be found [here](https://qitmeer.medium.com/from-blockchain-to-dag-i-9e2180ec676d) and [here](https://qitmeer.medium.com/from-blockchain-to-dag-ii-417bd4221512).