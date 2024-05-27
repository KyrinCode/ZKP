## Vector Commitment

Compress a data vector into a unique representation called a **Commitment** (similar to a hash). Then **Open** one of the data points and provide a **Proof** that it belongs to the vector without revealing the other data points

Merkle tree is also one implementation:

- The Merkle root serves as the Commitment, and the Merkle path serves as the proof

- Cons: the proof length is $O(logN)$

## Polynomial Commitment

Load a vector of length $n$ into the evaluation form of a polynomial: $(v_0, v_1, ..., v_n) \rightarrow (0, v_0), (1, v_1), ...,(n, v_n)$

- Generate a unique Commitment from the polynomial $f(x)$ of degree $d=n−1$ that corresponds to these points

- Open one of these points and provide a Proof that the point $(k,vk)$ is consistent with the polynomial

## Implementations

FRI：zkSTARK

+ Hashes only (quantum safe)

+ Large proof size (10-200 kB)

+ Medium verification time (poly-logarithmic)

IPA：Bulletproof

+ Basic Elliptic Curves

+ Medium proof size (1-3 kB)

+ High verification time (linear)

KZG：PLONK、Marlin

+ Elliptic Curves + Pairing + Trusted Setup

+ Short proof size (500 bytes)

+ Low verification time (constant)

IPA + Halo-style aggregation：Halo 2

+ Basic Elliptic Curves

+ Medium (1-3 kB)

+ Medium verification time (constant but higher than KZG)

## References

https://vitalik.eth.limo/general/2021/11/05/halo.html