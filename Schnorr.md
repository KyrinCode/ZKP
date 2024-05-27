## Introduction
Prerequisites
+ Properties of Hash functions
+ Discrete Logarithm Problem of Elliptic Curve: given elliptic curve $\mathbb{G}$ and generator $G$, choose a random number $d$, it is easy to compute $Q=d*G$ (also noted as $[d]G$), but given $Q$ and $G$, it is hard to compute $d$

Schnorr is essentially a ZK tech that the prover claims that he knows a secret $s$. Through Schnorr, he can prove to the verifier that he has the knowledge of $s$ without revealing the value of $s$.

Application
- Zero knowledge prove that you know a secret, can be used in identity verification
- digital signature

## Interactive Schnorr
Original Schnorr is interactive, prover Alice and verifier Bob (may not be honest)

```
+-------------------+              +-------------------------+
|       Alice       |              |          Bob            |
|                   |              |                         |
|                   |              |                         |
|  private:  s      |              |                         |
|                   |              |                         |
|   public:  PK=s*G |              |   public: PK            |
|                   |              |                         |
|   random:  r +------- R=r*G ------->                       |
|                   |              |                         |
|              <-------- c ----------+ random: c             |
|                   |              |                         |
|      z=r+c*s +-------- z ----------> verify: z*G ?= R+c*PK |
|                   |              |                         |
+-------------------+              +-------------------------+
```

Workflow
- Alice: choose a random $r$, compute $R=r*G$, send $R$ to Bob
- Bob: challenge a random $c$
- Alice: compute $z=r+c*s$, send $z$ to Bob
- Bob：compute $z*G$, and verify $z*G \overset{\text{?}}{=} R+c*PK$

Special Honest Verifier Zero-Knowledge (SHVZK)
+ Require that $c$ has to be random, such that Alice can not predict $c$ before generating $r$. Otherwise, Alice may attack like this
	+ Alice generates a random $z$ and compute $R=z*G-c*PK$ (satisfying the 3rd step of $z=r+c*s$)
	+ $z*G=R+c*PK$ passes Bob's verification

Risk of secret leakage
+ Require that $r$ has to be random, otherwise Bob may deduce secret $s$
	+ Alice uses the same $r$ twice and compute $z,z^{\prime}$
    + Bob can deduce secret $s$ through $s=(z-z^{\prime})/(c-c^{\prime})$
$$\begin{aligned}
z &= r+c*s \\
z^{\prime} &= r+c^{\prime}*s
\end{aligned}$$
	

## Non-Interactive Schnorr with Fiat-Shamir Transformation
Fiat-Shamir transformation
+ Convert interactive zero-knowledge proofs to non-interactive zero-knowledge proofs
+ An ideal Random Oracle does not exist, and cryptographic hash functions are often used as Random Oracles in implementation.

```
+------------------------+                +----------------------+
|       Alice            |                |          Bob         |
|                        |                |                      |
|                        |                |                      |
|  private:  s           |                |                      |
|                        |                |                      |
|   public:  PK=s*G      |                |    public: PK        |
|                        |                |                      |
|   random:  r           |                |                      |
|                        |                |                      |
|            R=r*G       |                |                      |
|                        |                |                      |
|            c=Hash(PK,R)|                |                      |
|                        |                |                      |
|            z=r+c*s +-------- (R,z) -------> verify:            |
|                        |                |                      |
|                        |                |      c=Hash(PK,R)    |
|                        |                |                      |
|                        |                |      z*G ?= R+c*PK   |
+------------------------+                +----------------------+
```

Workflow
- Alice: choose a random $r$ and compute
$$\begin{aligned}
R &= r*G \\
c &= Hash(PK,R) \\
z &= r+c*s
\end{aligned}$$
- Alice: generate proof $(R,z)$
- Bob: compute $c=Hash(PK,R)$
- Bob: verify $z*G \overset{\text{?}}{=} R+c*PK$

Analysis
+ Alice cannot predict $c$ before generate $r$, even if $c$ is selected by Alice indirectly
+ $c$ is computed through hash function, which is evenly distributed in the integer field, thus can be used as a random number

## Schnorr Signature
We change the process of computing $c$
$$c=Hash(m,R)$$

```
+-----------------------+                +----------------------+
|       Alice           |                |          Bob         |
|                       |                |                      |
|                       |                |                      |
|  private:  s          |                |                      |
|                       |                |                      |
|   public:  PK=s*G     |                |    public: PK        |
|                       |                |                      |
|   random:  r          |                |                      |
|                       |                |                      |
|            R=r*G      |                |                      |
|                       |                |                      |
|            c=Hash(m,R)|                |                      |
|                       |                |                      |
|            z=r+c*s +------- (c,z) --------> verify:           |
|                       |                |                      |
|                       |                |      R=z*G-c*PK      |
|                       |                |                      |
|                       |                |      c ?= Hash(m,R)  |
+-----------------------+                +----------------------+
```

Alice sends $(c,z)$ instead of $(R,z)$ because $R$ can be computed by $(c,z)$
The complexity of current elliptic curve attack algorithms are mostly $O(\sqrt{n})$, where $n$ is the bit number of the finite field. So a 256-bit elliptic curve's security level is only 128 bits, then a 128-bit $c$ is enough (save more space than 256-bit $R$)

## Multi Signature
Schnorr satisfies linear homomorphic property

Key generation
+ Each signer $i$ has a private key $s_i$ and corresponding public key $PK_i=s_i*G$
Aggregate public key
+ The aggregate public key $PK$ is computed from each participant's public key using a specific weighting method, such as $PK=\sum\limits_{i=1}^{n}P_i \cdot Hash(PK_1,PK_2,...PK_n)$
Randomness generation
+ Each signer $i$ independently generates a random $r_i$ and compute $R_i=r_i*G$
Aggregate randomness
+ $R=\sum\limits_{i=1}^{n}R_i$
Message hashing
+ $c=Hash(m,R)$
Partial signature generation
+ Each signer $i$ generates their partial signature $z_i=r_i+c*s_i$
Aggregate signature
+ $z=\sum\limits_{i=1}^{n}s_i$
Final signature $(c,z)$
$$\begin{aligned}
R_1 &= z_1*G-c*PK_1 \\
R_2 &= z_2*G-c*PK_2 \\
&\quad\quad\quad... \\
R_n &= z_n*G-c*PK_n \\
\sum\limits_{i=1}^{n}R_i &= \sum\limits_{i=1}^{n}z_i*G-c*\sum\limits_{i=1}^{n}PK_i \\
R &= z*G-c*PK
\end{aligned}$$

## References

Sigma protocol https://www.cs.au.dk/~ivan/Sigma.pdf

Sigma protocol https://crypto.stanford.edu/~dabo/cryptobook/BonehShoup_0_4.pdf
```
             Σ-protocol
P(x,w) +----------- a -------------> V(x)
P      <----------- e -------------+ V
P      +----------- z -------------> V
```
Pros
+ Exist unconditionally  without strong assumptions
+ do not need any trusted setup
Cons
+ Interactive (3 moves)
+ Not fully zero-knowledge (but only honest-verifier zero-knowledge)
+ Not succinct
[3] Fiat-Shamir transformation https://www.zkdocs.com/docs/zkdocs/protocol-primitives/fiat-shamir/
A technique to transform a Σ-protocol into a non-interactive zero-knowledge proof (NIZK)
Σ-protocol + Fiat-Shamir $\neq$ ZK-SNARK, because NIZK is not succinct in general