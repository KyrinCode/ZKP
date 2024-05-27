## Motivation
+ Given a list of data $y_i, i=0,...n-1$, commitment is a compressed digest of the data
+ Merkle tree commitment = root hash (32 bytes)
+ Prover: given $i$, generate proof $\pi_i$ and $y_i$
+ Verifier: given $\pi_i, y_i$, tell yes or no
+ Cons: $O(logn)$ proof size (27 * 32 = 864 bytes)

KZG
+ Commitment: 48 bytes
+ Single point proof size: 48 bytes (no matter $n$ is)
+ Multi point $y_0, y_1, ... y_{m-1}$ proof size: 48 bytes (no matter $n$ is)
+ Cons: need trusted setup

Applications
+ Ethereum Danksharding
+ Stateless (Verkle tree)
+ DAS
+ zkSync, Aleo
+ component of ZKP like Plonk and Marlin

```
Knowledge -> Point-Values -> Coefficients -> Commitment -> Open & Prove & Verify
                         FFT             MSM
                                          ^
                                          |
                                    Trusted Setup
```

## Polynomial

Coefficient form

$$f(x)=\sum\limits_{i=0}^{n-1} a_i x^i= a_0 + a_1 x + ... + a_{n-1} x^{n-1}$$

+ Degree of $f(x)$ is $n-1$

+ $a_{n-1} \neq 0$

## Encoding data into Polynomial using Lagrange Interpolation

Given Evaluation form $(x_i, y_i)$, $x_i \neq x_j$, $\forall i \neq j$, build a polynomial such that $f(x_i)=y_i$ and degree is $n-1$

$$f(x)=\sum\limits_{i=0}^{n-1} y_i \prod\limits_{j=0,j \neq i}^{n-1} \frac{x-x_j}{x_i-x_j}$$

Case $n=3$

$$f(x)=y_0\frac{(x-x_1)(x-x_2)}{(x_0-x_1)(x_0-x_2)} + y_1\frac{(x-x_0)(x-x_2)}{(x_1-x_0)(x_1-x_2)} + y_2\frac{(x-x_0)(x-x_1)}{(x_2-x_0)(x_2-x_1)}$$

Example

+ Given (0, 3), (1, 6), we have

$$f(x)=3(x-1)/(0-1)+6(x-0)/(1-0)=-3x+3+6x=3x+3$$

Property: any $(degree + 1)$ points confirms a polynomial

Suppose a redundant storage system, we also have (2, 9), (3, 12), (4, 15), and given (1, 6) and (3, 12), we can recover the polynomial (Reed-Solomon codes)

$$f(x)=6(x-3)(1-3)+12(x-1)(3-1)=-3x+9+6x-6=3x+3$$

$n$ encode to $m, (m>n)$, n-of-m data can recover the polynomial exactly

Note: to get the polynomial faster in production, we use FFT.

## Encoding binary (integer) data into Polynomial using Lagrange Interpolation

Encoding the binary data in a finite field $\mathbb{F}_p$,  $p$ is a prime such that $\{0, 1, 2, ... p-1\} \mod p$

A finite field supports + - * /

Example

+ Given $p=13$

+ $-2 = 13 - 2 = 11 \mod{13}$

+ $8 + 7 = 15 = 2 \mod{13}$

+ $8 * 7 = 4 \mod{13}$

+ $7^{-1} = 1 / 7 = 2 \mod{13}$, because $2 * 7 = 1 \mod{13}$ (Extended Euclidean algorithm)

+ $3^{-1}=1/3=9\mod{13}$, because $3 * 9 = 1 \mod{13}$

+  $9^{-1}=1/9=3\mod{13}$, because $9 * 3 = 1 \mod{13}$

Given data points (1, 6), (4, 2)

$$\begin{aligned}
f(x) & =6(x-4)/(1-4)+2(x-1)/(4-1) \\
& = -2x+8+2(x-1)/3 \\
& = -2x+8+5x-5 \\
& = 3x+3
\end{aligned}$$

## Polynomial on an Elliptic Curve $\mathbb{G}_1$

Additive group and support pairing

$G_1$: generator point

$G_1+G_1=[2]G_1$

$\mathbb{G}_1 = \{0, G_1, [2]G_1, [3]G_1, ... [p]G_1\}$ where $[p+1]G_1=0$

+ $[n]G_1=[n]_1$ can be computed very fast through Fast Exponentiation algorithm

+ $[n]X=Y$, given $X$ and $Y$, it is very hard to find $n$ (Discrete Logarithm Problem)

Example

+ Given $G_1=1$ $\mathbb{G}=[0,1,2,3,4,5,6,7,8,9,10,11,12]$

+ Given $G_1=2$ $\mathbb{G}=[0,2,4,6,8,10,12,1,3,5,7,9,11]$

The selection of $G_1$ should let $p$ big enough

$$[f(x)]G_1=[\sum\limits_{i=0}^{n-1}a_i x^i]G_1=\sum\limits_{i=0}^{n-1}[a_i]([x^i]G_1)=\sum\limits_{i=0}^{n-1}[a_i][x^i]_1$$

## Polynomial Commitment with Trusted Setup

Now we have secret $\tau \in \mathbb{F}_p$ such that

+ Nobody knows $s$ (private key of the god)

+ $[\tau^i]G_1=[\tau^i]_1, i=1,...$ is known to everybody ($[\tau]G_1$ is public key of the god)

Then the one who make the commitment (a point on elliptic curve) use the coefficients $a_i, i=0, ...n-1$ (what we want to hide)

$$C=[f(\tau)]_1=\sum\limits_{i=0}^{n-1} [a_i][\tau^i]_1$$

+ Nobody knows $f(\tau)$

+ $[f(\tau)]_1$ is known to everybody

+ Finding another $g(x)$ such that $g(\tau) = f(\tau)$ is almost impossible, because given $C$, we have no idea how this point is added up

## Single Proof

Given $(x_i, y_i$), prover to prove the evaluation $f(x_i)=y_i$

$$f(x)-y_i=q(x)(x-x_i)$$

+ $q(x)$ is quotient polynomial

Example

+ Given $f(x)=3x+3$ and data point (1, 6)

+ $3x+3-6=3x-3=q(x)(x-1)$

$$\begin{aligned}
[f(\tau) - y_i] G_1 &= [q(\tau)(\tau-x_i)] G_1 \\
C-[y_i]_1 &= ?
\end{aligned}$$

Introduce Pairing

$e: \mathbb{G}_1 \times \mathbb{G}_2 \rightarrow \mathbb{G}_T$

+ $\mathbb{G}_1$ and $\mathbb{G}_2$ are pairing friendly (not all $\mathbb{G}$ are friendly)

+ Dimension of $G_2$ is usually less than $G_1$

$$e(C-[y_i]_1, G_2)=e([q(\tau)]_1, [\tau-x_i]_2)$$

where $\pi=[q(\tau)]_1$ is the proof (48 bytes as a point on elliptic curve), and other components in the equation can be computed

$$[f(\tau)-y_i]_T=[q(\tau)(\tau-x_i)]_T$$

For saving gas, we use

$$e(C-[y_i]_1+x_i[q(\tau)]_1,G_2)=e([q(\tau)]_1,[\tau]_2)$$

## Multi Proof (Same Commitment Multiple Points)

Given $k$ points: $(x_0, y_0), (x_1, y_1), ... (x_{k-1}, y_{k-1})$, prover to prove the evaluation of all $k$ points together

Using Lagrange Interpolation, we can find a polynomial of degree $k-1$, that goes through all $k$ points

$$I(x)=\sum\limits_{i=0}^{k-1} y_i \prod\limits_{j=0,j \neq i}^{k-1} \frac{x-x_j}{x_i-x_j}$$

Since the original polynomial $f(x)$ is passing through all these points, the polynomial $f(x) - I(x)$ will be zero at each $x_0, x_1, ... x_{k-1}$, thus $f(x)-I(x)$ will be divisible by $$Z(x)=\prod\limits_{i=0}^{k-1} (x-x_i)$$

So we get

$$f(x)-I(x)=q(x)Z(x)$$

+ $q(x)$ is quotient polynomial

$$e(C-[I(\tau)]_1, G_2)=e([q(\tau)]_1, [Z(\tau)]_2)$$

where $\pi=[q(\tau)]_1$ is the proof (48 bytes as a point on elliptic curve)

$$[f(\tau)-I(\tau)]_T=[q(\tau)Z(\tau)]_T$$

## Multi Proof (Multiple Commitments Single Point)

Given $m$ KZG commitments $C_0=[f_0(\tau)]_1,…,C_{m−1}=[f_{m−1}(\tau)]_1$, prove evaluations

$$\begin{aligned}
f_0(z) &= y_0 \\
... \\
f_{m−1}(z) &= y_{m−1}
\end{aligned}$$

[Public] Fiat-Shamir transformation $r \leftarrow Hash(C_0,...C_{m-1},y_0,...y_{m-1},z)$

Prover computes the polynomial

$$\begin{aligned}
g(x)&=f_0(x)+r \cdot f_1(x)+...+r^{m-1}f_{m-1}(x) \\
g(x)-(y_0+r \cdot y_1+...+r^{m-1}y_{m-1})&=q(x) \cdot (x-z)
\end{aligned}$$

Then the KZG proof $\pi=[q(\tau)]_1$

Verifier computes $[g(\tau)]_1=[f_0(\tau)]_1+r\cdot [f_1(\tau)]_1+...+r^{m-1}\cdot [f_{m-1}(\tau)]$

and checks pairing

$$e([g(\tau)]_1-[y_0+r\cdot y_1+...+r^{m-1}y_{m-1}],G_2)\overset{\text{?}}{=}e([q(\tau)]_1,[\tau-z]_2)$$

## Multi Proof (Multiple Commitments Multiple Points)

Given $m$ KZG commitments $C_0=[f_0(\tau)]_1,…,C_{m−1}=[f_{m−1}(\tau)]_1$, prove evaluations

$$\begin{aligned}
f_0(x_0) &= y_0 \\
... \\
f_{m−1}(x_{m−1}) &= y_{m−1}
\end{aligned}$$

[Public] Fiat-Shamir transformation $r \leftarrow Hash(C_0,...C_{m-1},y_0,...y_{m-1},x_0,...x_{m-1})$

Prover computes the polynomial

$$g(x)=r^0\frac{f_0(x)−y_0}{x−x_0}+r^1\frac{f_1(x)−y_1}{x−x_1}+...+r^{m−1}\frac{f_{m−1}(x)−y_{m−1}}{x−x_{m−1}}$$

If we can prove that $g(x)$ is a polynomial, then it means that all the quotients are exact divisions, and thus the proof is complete.

Prover computes and sends the commitment $D=[g(\tau)]_1$, now we only need to convince the verifier that $D$ is indeed a commitment to $g(x)$

[Public] Fiat-Shamir transformation $t \leftarrow Hash(r,D)$

Prover prove the correctness of $D$ by evaluating it at a completely random point $t$ and helping the verifier check that the evaluation is indeed $g(t)$

Prover evaluates $g(t)=\sum\limits_{i=0}^{m-1}r^i\frac{f_i(t)-y_i}{t-x_i}=\underbrace{\sum\limits_{i=0}^{m-1}r^i\frac{f_i(t)}{t-x_i}}_{g_1(t)}-\underbrace{\sum\limits_{i=0}^{m-1}r^i\frac{y_i}{t-x_i}}_{g_2(t)}$, where $g_2(t)$ is completely known to the verifier

By giving an opening to the commitment $E=\sum\limits_{i=0}^{m-1}\frac{r^i}{t-x_i}C_i$ at $t$, $g_1(t)$ can be verified by verifier

Prover has $g_1(x)=\sum\limits_{i=0}^{m-1}r^i\frac{f_i(x)}{t-x_i}$, which satisfies $E=[g_1(\tau)]_1$

If $D$ is indeed the commitment of $g(x)$, the evaluation of new function $g_1(x)-g(x)$ at $t$ should be $g_2(t)$

The commitment of $g_1(x)-g(x)$ is $E-D$

$$q(x)=\frac{g_1(x)-g(x)-g_2(t)}{x-t}$$

Then the KZG proof $\pi=[q(\tau)]_1=[\frac{g_1(\tau)-g(\tau)-g_2(t)}{\tau-t}]_1$

$$e(E-D-[g_2(t)]_1,G_2)=e(\pi,[\tau-t]_2)$$

where

+ $E$ is computed from $C_0,...C_{m-1}$

+ $D$ is sent from prover

+ $g_2(t)$ can be computed with $x_i,y_i,r,t$

+ $\pi$ is sent from prover

## Proof Aggregation (Same Commitment Given Multiple $\pi$)

$$I(x)=\sum\limits_{i \in K}y_i\mathcal{L}_i(x), K=\{0,1,...k-1\}$$

$$\mathcal{L}_i(x)=\prod_{j \in K,j \neq i}\frac{x-x_i}{x_i-x_j}=\frac{Z(x)}{Z^{\prime}(x_i)(x-x_i)}$$

$$Z(x)=\prod_{i \in K}(x-x_i), Z^{\prime}(x)=\sum\limits_{j \in K}\frac{Z(x)}{(x-x_j)}$$

$$\begin{aligned}
q(x) &= \frac{f(x)-I(x)}{Z(x)}=f(x)\frac{1}{Z(x)}-I(x)\frac{1}{Z(x)} \\
&= f(x)\sum\limits_{i \in K}\frac{1}{Z^{\prime}(x_i)(x-x_i)}-\Biggl(Z(x)\sum\limits_{i \in K}\frac{y_i}{Z^{\prime}(x_i)(x-x_i)}\Biggl)\frac{1}{Z(x)} \\
&= \sum\limits_{i \in K}\frac{f(x)}{Z(x_i)(x-x_i)}-\sum\limits_{i \in K}\frac{y_i}{Z^{\prime}(x_i)(x-x_i)}=\sum\limits_{i \in K}\frac{1}{Z^{\prime}(x_i)} \cdot \frac{f(x)-y_i}{x-x_i} \\
&= \sum\limits_{i \in K}\frac{1}{Z^{\prime}(x_i)} \cdot q_i(x)
\end{aligned}$$

Example

$$I(x)=y_0L_0(x)+y_1L_1(x)+y_2L_2(x)$$

$$L_0(x)=\frac{(x-x_1)(x-x_2)}{(x_0-x_1)(x_0-x_2)}$$

$$Z(x)=(x-x_1)(x-x_2)(x-x_0)$$

$$Z^{\prime}(x)=Z(x)/(x-x_0)+Z(x)/(x-x_1)+Z(x)/(x-x_2)=(x-x_0)(x-x_1)+(x-x_0)(x-x_2)+(x-x_1)(x-x_2)$$

$$Z^{\prime}(x_0)=(x_0-x_1)(x_0-x_2)$$

$$\begin{aligned}
\sum\limits_{i \in \{0,1,2\}}\frac{1}{Z^{\prime}(x_i)(x-x_i)} &=\frac{1}{[(x_0-x_0)(x_0-x_1)+(x_0-x_0)(x_0-x_2)+(x_0-x_1)(x_0-x_2)](x-x_0)}+... \\
&= \frac{1}{(x_0-x_1)(x_0-x_2)(x-x_0)} + \frac{1}{(x_1-x_0)(x_1-x_2)(x-x_1)} + \frac{1}{(x_2-x_0)(x_2-x_1)(x-x_2)} \\
&= \frac{\frac{(x-x_1)(x-x_2)}{(x_0-x_1)(x_0-x_2)}+\frac{(x-x_0)(x-x_2)}{(x_1-x_0)(x_1-x_2)}+\frac{(x-x_0)(x-x_1)}{(x_2-x_0)(x_2-x_1)}}{(x-x_0)(x-x_1)(x-x_2)} \\
&= \frac{\frac{(x-x_1)(x-x_2)(x_1-x_2)+(x-x_0)(x-x_2)(x_2-x_0)+(x-x_0)(x-x_1)(x_0-x_1)}{(x_0-x_1)(x_0-x_2)(x_1-x_2)}}{(x-x_0)(x-x_1)(x-x_2)} \\
&= \frac{\frac{(x_1-x_2)[x^2-(x_1+x_2)x+x_1x_2]+(x_2-x_0)[x^2-(x_0+x_2)x+x_0x_2]+(x_0-x_1)[x^2-(x_0+x_1)x+x_0x_1]}{(x_0-x_1)(x_0-x_2)(x_1-x_2)}}{(x-x_0)(x-x_1)(x-x_2)} \\
&= \frac{\frac{x_1x_2(x_1-x_2)+x_0x_2(x_2-x_0)+x_0x_1(x_0-x_1)}{(x_0-x_1)(x_0-x_2)(x_1-x_2)}}{(x-x_0)(x-x_1)(x-x_2)} = \frac{\frac{x_1^2x_2-x_1x_2^2+x_0x_2^2-x_0^2x_2+x_0^2x_1-x_0x_1^2}{x_1^2x_2-x_1x_2^2+x_0x_2^2-x_0^2x_2+x_0^2x_1-x_0x_1^2}}{(x-x_0)(x-x_1)(x-x_2)} \\
&= \frac{1}{(x-x_0)(x-x_1)(x-x_2)} = \frac{1}{Z(x)}
\end{aligned}$$

$$\begin{aligned}
\sum\limits_{i \in \{0,1,2\}}\frac{y_i}{Z^{\prime}(x_i)(x-x_i)} &=\frac{y_0}{[(x_0-x_0)(x_0-x_1)+(x_0-x_0)(x_0-x_2)+(x_0-x_1)(x_0-x_2)](x-x_0)}+... \\
&= \frac{y_0}{(x_0-x_1)(x_0-x_2)(x-x_0)} + \frac{y_1}{(x_1-x_0)(x_1-x_2)(x-x_1)} + \frac{y_2}{(x_2-x_0)(x_2-x_1)(x-x_2)} \\
&= \frac{y_0\frac{(x-x_1)(x-x_2)}{(x_0-x_1)(x_0-x_2)}+y_1\frac{(x-x_0)(x-x_2)}{(x_1-x_0)(x_1-x_2)}+y_2\frac{(x-x_0)(x-x_1)}{(x_2-x_0)(x_2-x_1)}}{(x-x_0)(x-x_1)(x-x_2)}
\end{aligned}$$

$$\begin{aligned}
Z(x)\sum\limits_{i \in {0,1,2}}\frac{y_i}{A_I^{\prime}(x_i)(x-x_i)} &= y_0\frac{(x-x_1)(x-x_2)}{(x_0-x_1)(x_0-x_2)}+y_1\frac{(x-x_0)(x-x_2)}{(x_1-x_0)(x_1-x_2)}+y_2\frac{(x-x_0)(x-x_1)}{(x_2-x_0)(x_2-x_1)} = I(x)
\end{aligned}$$

## Linearization

Prover wants to prove $f(x)\cdot g(x)\overset{\text{?}}{=}h(x)$

Previously, with pairing, prover sends $[f(\tau)]_1,[g(\tau)]_2,[h(\tau)]_1$ and verifier checks

$$e([f(\tau)]_1,[g(\tau)]_2)\overset{\text{?}}{=}e([h(\tau)]_1,G_2)$$

Previously, with pure KZG, prover sends $[f(\tau)_1],[g(\tau)]_1,[h(\tau)]_1$, gets random $\zeta$ from verifier and opens $f(\zeta),g(\zeta),h(\zeta)$ with $[q_f(\tau)]_1,[q_g(\tau)]_1,[q_h(\tau)]_1$

Linearization

Prover sends $[f(\tau)]_1,[g(\tau)]_1,[h(\tau)]_1$ to verifier

Prover gets random $\zeta$ from verifier and open $\boxed{f(\zeta)}$ to verifier ~~with~~ $\cancel{\pi=[q_f(\tau)]_1}$

~~Verifier checks~~

$$\cancel{e([f(\tau)]_1-[f(\zeta)]_1,G_2)\overset{\text{?}}{=}e([q_f(\tau)]_1,[\tau-\zeta]_2)}$$

Then $f(\zeta)$ is a constant to both prover and verifier

Prover construct auxiliary polynomial $$r(x)=f(\zeta)\cdot g(x)-h(x)$$where $r(\zeta)$ should be 0

Verifier can compute the commitment of $r(x)$ with $[r(\tau)]_1=f(\zeta)\cdot[g(\tau)]_1-[h(\tau)]_1$

Prover then open $r(\zeta)$ to verifier ~~with~~ $\cancel{\pi=[q_r(\tau)]_1}$

$$\cancel{q_r(x)=\frac{r(x)-0}{x-\zeta}}$$

~~Verifier checks~~

$$\cancel{e([r(\tau)]_1,G_2)\overset{\text{?}}{=}e([q_r(\tau)]_1,[\tau-\zeta]_2)}$$

Use batching to combine the opening of $\zeta$ at $f(\zeta)$ and $r(\zeta)$

Prover gets random $v$ from verifier

Prover constructs auxiliary polynomial

$$k(x)=f(x)+v \cdot r(x)$$

and sends $\boxed{[q(\tau)]_1}$ to verifier

$$q(x)=\frac{k(x)-[f(\zeta)+v \cdot 0]}{x-\zeta}$$

Verifier can compute the commitment of $k(x)$ with

$$[k(\tau)]_1=[f(\tau)]_1+v \cdot \{f(\zeta)\cdot[g(\tau)]_1-[h(\tau)]_1\}$$

Verifier checks

$$e([k(\tau)]_1-[f(\zeta)]_1,G_2)\overset{\text{?}}{=}e([q(\tau)]_1,[\tau-\zeta]_2)$$

Prover delays the proving of $f(\zeta)$ and $r(\zeta)$ and combines them together

## References
Tau https://github.com/ethereum/kzg-ceremony-specs

KZG https://dankradfeist.de/ethereum/2020/06/16/kate-polynomial-commitments.html

Proof aggregation (same commitment) https://eprint.iacr.org/2020/527.pdf

Proof aggregation (multiple commitment) https://dankradfeist.de/ethereum/2021/06/18/pcs-multiproofs.html

Verkle tree https://dankradfeist.de/ethereum/2021/06/18/verkle-trie-for-eth1.html

Verkle tree https://vitalik.eth.limo/general/2021/06/18/verkle.html