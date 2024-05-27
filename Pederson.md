## Pederson Commitment
Public setup
+ a large prime $p$
+ a prime-order $q$ cyclic group $\mathbb{G}$
+ a generator $g$
+ a uniformly random element $h \in \mathbb{G}$ ($h=g^s \mod{q}$ where $s \in \mathbb{Z}_q$ is unknown to committer)

$h$ can be regarded as another generator that generate the same group $\mathbb{G}$ of order $q$

Goal: provably-secure commitment scheme for the message space $\mathcal{M}=\mathbb{Z}_q$, and randomness space $\mathcal{R}=\mathbb{Z}_q$

Commitment phase
+ prover creates a commitment $c(m,r)$ for message $m$ and a random number $r$ (blinding factor), and sends to the verifier
$$c(m,r)=g^m h^r \mod{p}$$
Opening phase
+ prover then reveals $m^{\prime},r^{\prime}$ for the commitment, and verifier can verify the commitment $c(m,r)$ with them
$$c(m,r) \overset{\text{?}}{=} g^{m^{\prime}} h^{r^{\prime}} \mod{p}$$
Properties
+ Binding: once a value has been committed to, it is computationally infeasible for the committer (prover) to open the commitment to a different value. This relies on the hardness of the discrete logarithm problem
+ Hiding: the committed value remains hidden from the verifier until the committer reveals it. This is unconditional because the verifier do not know which is the original message $m \in \mathbb{Z}_q$

Linearly-homomorphic
Any linear function of committed values can be computed locally by performing some operations on commitments
+ Let
$$\begin{aligned}
c(m_1,r_1) &= g^{m_1}h^{r_1} \\
c(m_2,r_2) &= g^{m_2}h^{r_2} \\
a,b &\in \mathbb{Z}_q
\end{aligned}
$$
+ Then
$$\begin{aligned}
c(m_1,r_1)^a &= (g^{m_1}h^{r_1})^a = g^{a m_1}h^{a r_1} = c(a m_1,a r_1) \\
c(m_2,r_2)^b &= (g^{m_2}h^{r_2})^b = g^{b m_2}h^{b r_2} = c(b m_2,b r_2) \\
c(m_1,r_1)^a c(m_2,r_2)^b &= g^{a m_1 + b m_2}h^{a r_1 + b r_2} = c(a m_1 + b m_2,a r_1 + b r_2)
\end{aligned}$$

Security discussions
+ The randomness of $r$ should not be related to $m$, like $r=Hash(m)$, because an attacker could try to guess $m$ and verify if this guess is correct by checking if $g^m h^{Hash(m)}=c$. This breaks the hiding property

## Elliptic Curve Pederson Commitment
Public setup
+ Given the field $\mathbb{F}_p$ of elliptic curve points, where $p$ is a large prime
+ Given a generator point $G \in \mathbb{F}_p$, and let $H \in \mathbb{F}_p$ be specifically chosen so that the value $s$ to satisfy $H=s*G$ can not be found except ECDLP is solved

Goal: provably-secure commitment scheme for the message space $\mathcal{M}=\mathbb{Z}_p$, and randomness space $\mathcal{R}=\mathbb{Z}_p$

Commitment $c(m,r)=m*G+r*H$

## Pederson Hash
If removing the $h^r$ or $r*H$ term, we get a non-hiding commitment, called a Pedersen hash. Still relies on the discrete logarithm problem hardness assumption

Without $r$, if $c=g^{m^{\prime}}$ or $c=m^{\prime}*G$, then $m=m^{\prime}$
With r, even if $c=g^{m^{\prime}}h^{r^{\prime}}$ or $c=m^{\prime}*G + c=r^{\prime}*H$, we still can not say $m^{\prime}=m$

## Pederson Vector Commitment
Commit a set of values $(v_1,...v_n)$ rather than a value and a blinding term.

Take EC-based pederson as an example. Suppose we have a set of random elliptic curve points $(G_1,… G_n)$ (that we do _not_ know the discrete logarithm of), and we do the following
$$c=v_1*G_1+v_2*G_2+...+v_n*G_n+r*Q$$
This will commit $n$ values to $c$ and hide it with $r$, and all $(v_1,...v_n)$ have to be revealed in the opening phase

Linearly-homomorphic
We can add two Pedersen Vector Commitments to get one commitment to two vectors. This will still only allow the committer to open to the original vectors. The important implementation detail is that we have to use a different set of elliptic curve points to commit against (all the points need to be selected randomly)
$$\begin{aligned}
c_1 &= v_1*G_1+v_2*G_2+...+v_n*G_n+r*Q \\
c_2 &= w_1*H_1+w_2*H_2+...+w_n*H_n+s*Q \\
c_3 &= c_1 + c_2
\end{aligned}$$
where $r*Q$ and $s*Q$ are blinding terms

## Applications
+ Matching Input and Output Sums in Monero
	+ Monero uses the Pedersen commitment to hide the amounts in transactions, while at the same time verifying that the output sums match input sums.
	+ When the user construct the transaction, he knows the real value of each input and output. He can let the sum of blinding terms of inputs minus the sum of blinding terms of outputs (without the one of fee output) to obtain the blinding term of fee output, so that the sum of commitments of inputs equals to the sum of commitments of outputs.

## References
Pedersen commitment https://www.youtube.com/watch?v=8DwqbbSuE4Y
Pedersen commitment https://www.rareskills.io/post/pedersen-commitment
Monero https://medium.com/coinmonks/zero-knowledge-proofs-um-what-a092f0ee9f28 https://github.com/mukatee/monero-scraper/tree/master/src/golang
