## Plonkish Arithmetization
$$\begin{split}
x_5 * x_6 &= out = 5 \\
x_1 + x_2 &= x_5 \\
x_3 * x_4 &= x_6 \\
\end{split}$$

Witness table (invisible to verifier) & Selector table & Public inputs

$$
\begin{array}{c|c|c|c|}
i & a_i & b_i & c_i \\
\hline
0 & \color{blue}out & 0 & 0 \\
1 & \color{red}x_5 & \color{green}x_6 & \color{blue}out \\
2 & x_1 & x_2 & \color{red}x_5 \\
3 & x_3 & x_4 & \color{green}x_6 \\
\end{array}
\qquad \qquad
\begin{array}{c|c|c|c|c}
q_L & q_R & q_M & q_O & q_C \\
\hline
-1 & 0 & 0 & 0 & 0 \\
0 & 0 & 1 & -1 & 0 \\
1 & 1 & 0 & -1 & 0 \\
0 & 0 & 1 & -1 & 0 \\
\end{array}
\qquad \qquad
\begin{array}{c}
PI \\
\hline
5 \\
0 \\
0 \\
0 \\
\end{array}
$$

$$q_{L,i} \cdot a_i + q_{R,i} \cdot b_i + q_{M,i} \cdot a_i \cdot b_i + q_{O,i} \cdot c_i + q_{C,i} + PI_i= 0$$

Permutation table

$$
\begin{array}{c|c|c|c|}
i & id_a & id_b & id_c \\
\hline
0 & \color{blue}0 & 4 & 8 \\
1 & \color{red}1 & \color{green}5 & \color{blue}9 \\
2 & 2 & 6 & \color{red}10 \\
3 & 3 & 7 & \color{green}11 \\
\end{array}
\quad \overset{\sigma}{\Rightarrow} \quad
\begin{array}{c|c|c}
\sigma_a & \sigma_b & \sigma_c \\
\hline
\color{blue}9 & 4 & 8 \\
\color{red}10 & \color{green}11 & \color{blue}0 \\
2 & 6 & \color{red}1 \\
3 & 7 & \color{green}5 \\
\end{array}
\quad \overset{optimize}{\rightarrow} \quad
\begin{array}{c|c|c|c|}
i & id_a & id_b & id_c \\
\hline
0 & \color{blue}1 & k_1 & k_2 \\
1 & \color{red}w & \color{green}k_1 \cdot w & \color{blue}k_2 \cdot w \\
2 & w^2 & k_1 \cdot w^2 & \color{red}k_2 \cdot w^2 \\
3 & w^3 & k_1 \cdot w^3 & \color{green}k_2 \cdot w^3 \\
\end{array}
\quad \overset{\sigma}{\Rightarrow} \quad
\begin{array}{c|c|c}
\sigma_a & \sigma_b & \sigma_c \\
\hline
\color{blue}k_2 \cdot w & k_1 & k_2 \\
\color{red}k_2 \cdot w^2 & \color{green} \cdot w^3 & \color{blue}1 \\
w^2 & k_1 \cdot w^2 & \color{red}w \\
w^3 & k_1 \cdot w^3 & \color{green}k_1 \cdot w \\
\end{array}
$$

## Universal Trusted Setup
$$SRS=\{G_1,[\tau]_1,[\tau^2]_1,...[\tau^d],G_2,[\tau]_2\}$$

## Public Inputs
Using Lagrange interpolation to get $PI(x)$

## Preprocessing
$$\begin{split}
& [q_L(\tau)]_1,[q_R(\tau)]_1,[q_M(\tau)]_1,[q_O(\tau)]_1,[q_C(\tau)]_1 \\
& [\sigma_a(\tau)]_1,[\sigma_b(\tau)]_1,[\sigma_c(\tau)]_1 \\
& \cancel{[id_a(\tau)]_1,[id_b(\tau)]_1,[id_c(\tau)]_1} \quad \overset{optimize}{\rightarrow} \quad id_a(x)=x, \quad id_b(x)=k_1 \cdot x, \quad id_c(x)=k_2 \cdot x
\end{split}$$
$k_1,k_2$ have to be the multiplicative generators of $\mathbb{H}$'s coset

Example
multiplicative generator: $g=2$
$$\begin{split}
H&=\{1,5,12,8\} \\
H_1&=g \cdot \mathbb{H}=\{2,10,11,3\} \\
H_2&=g^2 \cdot \mathbb{H}=\{4,7,9,6\}
\end{split}$$

## Round 1
Using Lagrange interpolation to get
$$\begin{split}
a(x)&=a_0L_0(x)+a_1L_1(x)+...+a_{n-1}L_{n-1}(x) \\
b(x)&=b_0L_0(x)+b_1L_1(x)+...+b_{n-1}L_{n-1}(x) \\
c(x)&=c_0L_0(x)+c_1L_1(x)+...+c_{n-1}L_{n-1}(x)
\end{split}$$
where Lagrange basis $L_i(x)=\prod\limits_{j=0,j \neq i}^{n-1} \frac{x-w^j}{w^i-w^j}$
$$\begin{split}
L_i(x) &= \left\{
	\begin{array}{l}
        1,\quad x=w^i \\
        0,\quad x \in \mathbb{H}\backslash\{w^i\} \\
    \end{array}
\right.
\end{split}$$
where $\mathbb{H}=\{1,w,w^2,...w^{n-1}\}$ is composed of roots of unity, $\mathbb{H}$ is a multiplicative subgroup, $n=2^k$

$$\begin{split}
z_H(x)&=(x-1)(x-w)(x-w^2)...(x-w^{n-1}) \\
&=x^n-1
\end{split}$$
$$q_L(x) \cdot a(x) + q_R(x) \cdot b(x) + q_M(x) \cdot a(x) \cdot b(x) + q_O(x) \cdot c(x) + q_C(x) + PI(x)= 0, \quad \forall x \in \mathbb{H}$$

Prover sends commitments $\boxed{[a(\tau)]_1},\boxed{[b(\tau)]_1},\boxed{[c(\tau)]_1}$ to verifier

## Round 2
Prover gets two random $\beta,\gamma$ from round 1

[Problem transformation]
Permutation argument $\Rightarrow$ Multiset equality argument $\Rightarrow$ Grand product argument

Prover constructs auxiliary $\overrightarrow{z}$, which satisfies
$$\begin{split}
z_0 &= 1 \\
z_{i+1} &= \frac{(a_i+\beta \cdot id_{a,i} + \gamma)(b_i+\beta \cdot id_{b,i} + \gamma)(c_i+\beta \cdot id_{c,i} + \gamma)}{(a_i+\beta \cdot \sigma_{a,i} + \gamma)(b_i+\beta \cdot \sigma_{b,i} + \gamma)(c_i+\beta \cdot \sigma_{c,i} + \gamma)} \cdot z_i \\
z_n &= 1
\end{split}$$
Using Lagrange Interpolation with $z_0,z_1,...z_{n-1}$ to get $z(x)$
$$\begin{split}
& L_0(x) \cdot [z(x)-1] = 0 \\
& z(w \cdot x) \cdot [a(x)+\beta \cdot \sigma_a(x) + \gamma] \cdot [b(x)+\beta \cdot \sigma_b(x) + \gamma] \cdot [c(x)+\beta \cdot \sigma_c(x) + \gamma] \\
& - z(x) \cdot [a(x)+\beta \cdot \cancel{id_a(x)}x + \gamma] \cdot [b(x)+\beta \cdot \cancel{id_b(x)}k_1 \cdot x + \gamma] \cdot [c(x)+\beta \cdot \cancel{id_c(x)}k_2 \cdot x + \gamma] = 0 \\
\\
& \forall x \in \mathbb{H}
\end{split}$$

Prover sends $\boxed{[z(\tau)]_1}$ to verifier

## Round 3
Prover gets random $\alpha$ from round 2

And we get quotient function $t(x)$
$$\begin{split}
& q_L(x) \cdot a(x) + q_R(x) \cdot b(x) + q_M(x) \cdot a(x) \cdot b(x) + q_O(x) \cdot c(x) + q_C(x) + PI(x) \\
& +\alpha \cdot \{z(w \cdot x) \cdot [a(x)+\beta \cdot \sigma_a(x) + \gamma] \cdot [b(x)+\beta \cdot \sigma_b(x) + \gamma] \cdot [c(x)+\beta \cdot \sigma_c(x) + \gamma] \\
&\qquad\qquad- z(x) \cdot [a(x)+\beta \cdot x + \gamma] \cdot [b(x)+\beta \cdot k_1 \cdot x + \gamma] \cdot [c(x)+\beta \cdot k_2 \cdot x + \gamma]\} \\
& +\alpha^2 \cdot L_0(x) \cdot [z(x)-1] \\
& = t(x) \cdot z_H(x) \\
\\
& \forall x \in \mathbb{F}_p
\end{split}$$
where
$$t(x)=t_{low}(x)+x^n \cdot t_{mid}(x)+x^{2n} \cdot t_{high}(x)$$
Prover sends $\cancel{[t(\tau)]_1} \boxed{[t_{low}(\tau)]_1},\boxed{[t_{mid}(\tau)]_1},\boxed{[t_{high}(\tau)]_1}$ to verifier

## Round 4 (without Linearization)
Prover gets random $\zeta$ from round 3

Prover opens at
$$\begin{split}
& q_L(\zeta),q_R(\zeta),q_M(\zeta),q_O(\zeta),q_C(\zeta) \\
& \sigma_a(\zeta),\sigma_b(\zeta),\sigma_c(\zeta) \\
& a(\zeta),b(\zeta),c(\zeta) \\
& z(\zeta),z(w \cdot \zeta) \\
& \cancel{id_a(\zeta),id_b(\zeta),id_c(\zeta)} \\
& \cancel{t(\zeta)} t_{low}(\zeta),t_{mid}(\zeta),t_{high}(\zeta)
\end{split}$$
and sends to verifier

## Round 5 (without Linearization)
Prover sends all the commitments of quotient polynomials at
$$q_L(\zeta),q_R(\zeta),q_M(\zeta),q_O(\zeta),q_C(\zeta),\sigma_a(\zeta),\sigma_b(\zeta),\sigma_c(\zeta),a(\zeta),b(\zeta),c(\zeta),z(\zeta),z(w\cdot\zeta),t_{low}(\zeta),t_{mid}(\zeta),t_{high}(\zeta)$$

## Verification (without Linearization)
Verifier computes $PI(\zeta),L_0(\zeta),z_H(\zeta)$
and verifies
$$q_L(\zeta),q_R(\zeta),q_M(\zeta),q_O(\zeta),q_C(\zeta),\sigma_a(\zeta),\sigma_b(\zeta),\sigma_c(\zeta),a(\zeta),b(\zeta),c(\zeta),z(\zeta),z(w\cdot\zeta),t_{low}(\zeta),t_{mid}(\zeta),t_{high}(\zeta)$$
with their commitments of quotient polynomials and commitments
$$\begin{split}
&[q_L(\tau)]_1,[q_R(\tau)]_1,[q_M(\tau)]_1,[q_O(\tau)]_1,[q_C(\tau)]_1,[\sigma_a(\tau)]_1,[\sigma_b(\tau)]_1,[\sigma_c(\tau)]_1 \\
&[a(\tau)]_1,[b(\tau)]_1,[c(\tau)]_1,[z(\tau)]_1,[t_{low}(\tau)]_1,[t_{mid}(\tau)]_1,[t_{high}(\tau)]_1
\end{split}$$
Then verifier checks
$$\begin{split}
& q_L(\zeta) \cdot a(\zeta) + q_R(\zeta) \cdot b(\zeta) + q_M(\zeta) \cdot a(\zeta) \cdot b(\zeta) + q_O(\zeta) \cdot c(\zeta) + q_C(\zeta) + PI(\zeta) \\
& +\alpha \cdot \{z(w \cdot \zeta) \cdot [a(\zeta)+\beta \cdot \sigma_a(\zeta) + \gamma] \cdot [b(\zeta)+\beta \cdot \sigma_b(\zeta) + \gamma] \cdot [c(\zeta)+\beta \cdot \sigma_c(\zeta) + \gamma] \\
&\qquad\qquad- z(\zeta) \cdot [a(\zeta)+\beta \cdot x + \gamma] \cdot [b(\zeta)+\beta \cdot k_1 \cdot \zeta + \gamma] \cdot [c(\zeta)+\beta \cdot k_2 \cdot \zeta + \gamma]\} \\
& +\alpha^2 \cdot L_0(\zeta) \cdot [z(\zeta)-1] \\
& \overset{\text{?}}{=} [t_{low}(\zeta)+x^n \cdot t_{mid}(\zeta)+x^{2n} \cdot t_{high}(\zeta)] \cdot z_H(\zeta)
\end{split}$$
## Round 4 (Linearization)
Prover gets random $\zeta$ from round 3
Prover wants to prove
$$\begin{split}
& q_L(x) \cdot a(x) + q_R(x) \cdot b(x) + q_M(x) \cdot a(x) \cdot b(x) + q_O(x) \cdot c(x) + q_C(x) + PI(x)\\
& +\alpha \cdot \{z(w \cdot x) \cdot [a(x)+\beta \cdot \sigma_a(x) + \gamma] \cdot [b(x)+\beta \cdot \sigma_b(x) + \gamma] \cdot [c(x)+\beta \cdot \sigma_c(x) + \gamma] \\
& \qquad\qquad - z(x) \cdot [a(x)+\beta \cdot x + \gamma] \cdot [b(x)+\beta \cdot k_1 \cdot x + \gamma] \cdot [c(x)+\beta \cdot k_2 \cdot x + \gamma]\} \\
& +\alpha^2 \cdot L_0(x) \cdot [z(x)-1] \\
& = [t_{low}(x)+x^n \cdot t_{mid}(x)+x^{2n} \cdot t_{high}(x)] \cdot z_H(x) \\
\\
& \forall x \in \mathbb{F}_p
\end{split}$$
Prover only opens
$$\begin{split}
&\boxed{a(\zeta)},\boxed{b(\zeta)},\boxed{c(\zeta)} \\
&\boxed{z(w\cdot \zeta)} \\
&\boxed{\sigma_a(\zeta)},\boxed{\sigma_b(\zeta)}
\end{split}$$
and sends to verifier

## Round 5 (Linearization)
Prover constructs auxiliary polynomial
$$\begin{split}
r(x)&={\color{red}a(\zeta)} \cdot q_L(x) + {\color{red}b(\zeta)} \cdot q_R(x) + {\color{red}a(\zeta) \cdot b(\zeta)} \cdot q_M(x) + {\color{red}c(\zeta)} \cdot q_O(x) + q_C(x) + {\color{green}PI(\zeta)}\\
& \quad+\alpha \cdot \{{\color{red}z(w \cdot \zeta)} \cdot [{\color{red}a(\zeta)}+\beta \cdot {\color{red}\sigma_a(\zeta)} + \gamma] \cdot [{\color{red}b(\zeta)}+\beta \cdot {\color{red}\sigma_b(\zeta)} + \gamma] \cdot [{\color{red}c(\zeta)}+\beta \cdot \sigma_c(x) + \gamma] \\
& \qquad\qquad - z(x) \cdot [{\color{red}a(\zeta)}+\beta \cdot {\color{green}\zeta} + \gamma] \cdot [{\color{red}b(\zeta)}+\beta \cdot k_1 \cdot {\color{green}\zeta} + \gamma] \cdot [{\color{red}c(\zeta
)}+\beta \cdot k_2 \cdot {\color{green}\zeta} + \gamma]\} \\
& \quad+\alpha^2 \cdot {\color{green}L_0(\zeta)} \cdot [z(x)-1] \\
& \quad-[t_{low}(x)+{\color{green}\zeta^n} \cdot t_{mid}(x)+{\color{green}\zeta^{2n}} \cdot t_{high}(x)] \cdot {\color{green}z_H(\zeta)} \\
\\
r(\zeta)&=0
\end{split}$$
Verifier can compute the commitment $[r(\tau)]_1$ of $r(x)$ with
$$\begin{split}
&[q_L(\tau)]_1,[q_R(\tau)]_1,[q_M(\tau)]_1,[q_O(\tau)]_1,[q_C(\tau)]_1,[z(\tau)]_1,[\sigma_c(\tau)]_1,[t_{low}(\tau)]_1,[t_{mid}(\tau)]_1,[t_{high}(\tau)]_1 \\
&a(\zeta),b(\zeta),c(\zeta),L_0(\zeta),z(w\cdot \zeta),\sigma_a(\zeta),\sigma_b(\zeta),z_H(\zeta),PI(\zeta)
\end{split}$$

Prover gets random $v$ from round 4
Prover constructs auxiliary polynomial and prove the evaluation at $\zeta$, which simultaneously proves $a(\zeta),b(\zeta),c(\zeta),\sigma_a(\zeta),\sigma_b(\zeta),r(\zeta)$
$$\begin{split}
k(x)&=r(x)+v\cdot a(x)+v^2 \cdot b(x)+v^3 \cdot c(x)+v^4 \cdot \sigma_a(x)+v^5 \cdot \sigma_b(x) \\
\end{split}$$
and sends $\boxed{[q_1(\tau)]_1},\boxed{[q_2(\tau)]_1}$ to verifier
$$\begin{split}
q_1(x)&=\frac{k(x)-[0+v\cdot a(\zeta)+v^2 \cdot b(\zeta)+v^3 \cdot c(\zeta)+v^4 \cdot \sigma_a(\zeta)+v^5 \cdot \sigma_b(\zeta)]}{x-\zeta} \\
q_2(x)&=\frac{z(x)-z(w\cdot \zeta)}{x-w\cdot \zeta}
\end{split}$$
## Verification (Linearization)
Verifier can compute the commitment of $k(x)$ with
$$[k(\tau)]_1=[r(\tau)]_1+v\cdot [a(\tau)]_1+v^2 \cdot [b(\tau)]_1+v^3 \cdot [c(\tau)]_1+v^4 \cdot [\sigma_a(\tau)]_1+v^5 \cdot [\sigma_b(\tau)]_1$$
[Unoptimized]
Verifier checks
$$\begin{split}
e([k(\tau)]_1-[v\cdot a(\zeta)+v^2 \cdot b(\zeta)+v^3 \cdot c(\zeta)+v^4 \cdot \sigma_a(\zeta)+v^5 \cdot \sigma_b(\zeta)]_1,G_2)&\overset{\text{?}}{=}e([q_1(\tau)]_1,[\tau-\zeta]_2) \\
e([z(\tau)]_1-[z(w\cdot \zeta)]_1,G_2)&\overset{\text{?}}{=}e([q_2(\tau)]_1,[\tau-w\cdot \zeta]_2)
\end{split}$$
[Optimized]
Verifier gets random $u$ from verifier
$$\begin{split}
r0&=PI(\zeta)-\alpha\cdot z(w\cdot\zeta)\cdot[a(\zeta)+\beta \cdot \sigma_a(\zeta) + \gamma] \cdot [b(\zeta)+\beta \cdot \sigma_b(\zeta) + \gamma] \cdot [c(\zeta)+\beta \cdot \sigma_c(x) + \gamma] -\alpha^2\cdot L_0(\zeta) \\
D&=a(\zeta)\cdot[q_L(\tau)]_1+b(\zeta)\cdot[q_R(\tau)]_1+a(\zeta)\cdot b(\zeta)\cdot[q_M(\tau)]_1+c(\zeta)\cdot[q_O(\tau)]_1+[q_C(\tau)]_1 \\
&\quad+\{\alpha\cdot[a(\zeta)+\beta\zeta+\gamma]\cdot[b(\zeta)+\beta\cdot k_1\cdot\zeta+\gamma]\cdot[c(\zeta)+\beta\cdot k_2\cdot\zeta+\gamma]+\alpha^2\cdot L_0(\zeta)+u\}\cdot[z(\tau)]_1 \\
&\quad-\{\alpha\cdot\beta\cdot z(w\cdot \zeta)\cdot[a(\zeta)+\beta\cdot\sigma_a(\zeta)+\gamma]\cdot[a(\zeta)+\zeta\cdot\sigma_b(\zeta)+\gamma]\}\cdot[\sigma_c(\tau)]_1 \\
&\quad-z_H(\zeta)\cdot [t_{low}(\tau)]_1-z_H(\zeta)\cdot\zeta^n\cdot[t_{mid}(\tau)]_1-z_H(\zeta)\cdot\zeta^{2n}\cdot[t_{high}(\tau)]_1 \\
&=[r(\tau)]_1 - [r0]_1 + u\cdot [z(\tau)]_1 \\
F&=D+[v\cdot a(\tau)+v^2 \cdot b(\tau)+v^3 \cdot c(\tau)+v^4 \cdot \sigma_a(\tau)+v^5 \cdot \sigma_b(\tau)]_1 \\
E&=[v\cdot a(\zeta)+v^2 \cdot b(\zeta)+v^3 \cdot c(\zeta)+v^4 \cdot \sigma_a(\zeta)+v^5 \cdot \sigma_b(\zeta)+u\cdot z(w\cdot\zeta)-r0]_1
\end{split}$$
Verifier checks
$$
\begin{split}
&e([q_1(\tau)]_1+u\cdot[q_2(\tau)]_1,[\tau]_2) \overset{\text{?}}{=}e(\zeta\cdot[q_1(\tau)]_1+u\cdot w\cdot\zeta\cdot[q_2(\tau)]_1+F-E) \\
\\
&=e(\zeta\cdot[q_1(\tau)]_1+u\cdot w\cdot\zeta\cdot[q_2(\tau)]_1 \\
&\quad+[k(\tau)]_1-[v\cdot a(\zeta)+v^2 \cdot b(\zeta)+v^3 \cdot c(\zeta)+v^4 \cdot \sigma_a(\zeta)+v^5 \cdot \sigma_b(\zeta)]_1 \\
&\quad+u\cdot[z(\tau)]_1-u\cdot[z(w\cdot\zeta)]_1,G_2)
\end{split}$$

## References
Plonk arithmetization https://vitalik.eth.limo/general/2019/09/22/plonk.html

Plonk paper https://eprint.iacr.org/2019/953.pdf