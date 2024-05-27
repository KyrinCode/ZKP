## R1CS
Any computation that can be done in polynomial time can be converted to R1CS circuit constraints.

R1CS circuit constraints: equations of order 1
+ Additive constraint: additive equation of order 1
+ Multiplicative constraint: multiplicative equation of order 1
>An equation of order 1 can be expressed as a circuit constraint. Circuit constraints can be implemented using hardware circuits or FPGAs. Therefore, equations of order 1 are equivalent to circuit constraints

Example
$$\begin{aligned}
a \in \{0,1\} &\Leftrightarrow \{(1-a)*a=0\} \\
a \oplus b = c &\Leftrightarrow \left\{
	\begin{array}{c}
        (1-a)*a=0 \\
        (1-b)*b=0 \\
        (1-c)*c=0 \\
        2*a*b=a+b-c
    \end{array}
\right\} \\
\end{aligned}$$
## QAP
A program
$$\begin{aligned}
y=x^4+x^3+x^2+x &\Leftrightarrow \left\{
	\begin{array}{c}
        s_1=x*x \\
        s_2=s_1*x \\
        y-x-s_1-s_2=s_2*x \\
    \end{array}
\right\}
\end{aligned}$$
$$w=(statement, witness)=(1,out,x,s_1,s_2)$$
$$\begin{aligned}
s_1=x*x &\Leftrightarrow \left\{
	\begin{array}{l}
        \overrightarrow{w} \cdot c_1 = [1,out,x,s_1,s_2] \cdot [0,0,0,1,0] = s_1 \\
        \overrightarrow{w} \cdot a_1 = [1,out,x,s_1,s_2] \cdot [0,0,1,0,0] = x \\
        \overrightarrow{w} \cdot b_1 = [1,out,x,s_1,s_2] \cdot [0,0,1,0,0] = x
    \end{array}
\right\} \\
s_2=s_1*x &\Leftrightarrow \left\{
	\begin{array}{l}
        \overrightarrow{w} \cdot c_2 = [1,out,x,s_1,s_2] \cdot [0,0,0,0,1] = s_2 \\
        \overrightarrow{w} \cdot a_2 = [1,out,x,s_1,s_2] \cdot [0,0,0,1,0] = s_1 \\
        \overrightarrow{w} \cdot b_2 = [1,out,x,s_1,s_2] \cdot [0,0,1,0,0] = x
    \end{array}
\right\} \\
y-x-s_1-s_2=s_2*x &\Leftrightarrow \left\{
	\begin{array}{l}
        \overrightarrow{w} \cdot c_3 = [1,out,x,s_1,s_2] \cdot [0,1,-1,-1,-1] = y-(x+s_1+s_2) \\
        \overrightarrow{w} \cdot a_3 = [1,out,x,s_1,s_2] \cdot [0,0,0,0,1] = s_2 \\
        \overrightarrow{w} \cdot b_3 = [1,out,x,s_1,s_2] \cdot [0,0,1,0,0] = x
    \end{array}
\right\}
\end{aligned}$$
$$\begin{bmatrix}
w \cdot c_1 \\
w \cdot c_2 \\
w \cdot c_3
\end{bmatrix} =
\begin{bmatrix}
w \cdot a_1 \\
w \cdot a_2 \\
w \cdot a_3
\end{bmatrix} \circ
\begin{bmatrix}
w \cdot b_1 \\
w \cdot b_2 \\
w \cdot b_3
\end{bmatrix}
$$
$$
\begin{aligned}
C=\begin{bmatrix}
{c_1} \\
c_2 \\
c_3
\end{bmatrix} &=
\begin{bmatrix}
\color{red}0 & \color{green}0 & 0 & 1 & 0 \\
\color{red}0 & \color{green}0 & 0 & 0 & 1 \\
\color{red}0 & \color{green}1 & -1 & -1 & -1
\end{bmatrix} \\
A=\begin{bmatrix}
a_1 \\
a_2 \\
a_3
\end{bmatrix} &=
\begin{bmatrix}
0 & 0 & 1 & 0 & 0 \\
0 & 0 & 0 & 1 & 0 \\
0 & 0 & 0 & 0 & 1
\end{bmatrix} \\
B=\begin{bmatrix}
b_1 \\
b_2 \\
b_3
\end{bmatrix} &=
\begin{bmatrix}
0 & 0 & 1 & 0 & 0 \\
0 & 0 & 1 & 0 & 0 \\
0 & 0 & 1 & 0 & 0
\end{bmatrix}
\end{aligned}
$$
$$w \cdot C = (w \cdot A) \circ (w \cdot B)$$
>Hadamard product $\circ$ means that we multiply the values of the two vectors or matrices that are in the same position

Row length of matrix is the number of constraints $n$, column length $m$ of matrix is the size of $w$

Lagrange Interpolation $x=1,2,3$
$$\begin{aligned}
C_1(x)&={\color{red}0}\frac{(x-2)(x-3)}{({\color{blue}1}-2)({\color{blue}1}-3)}+{\color{red}0}\frac{(x-1)(x-3)}{({\color{blue}2}-1)({\color{blue}2}-3)}+{\color{red}0}\frac{(x-1)(x-2)}{({\color{blue}3}-1)({\color{blue}3}-2)}=0 \\

C_2(x)&={\color{green}0}\frac{(x-2)(x-3)}{({\color{blue}1}-2)({\color{blue}1}-3)}+{\color{green}0}\frac{(x-1)(x-3)}{({\color{blue}2}-1)({\color{blue}2}-3)}+{\color{green}1}\frac{(x-1)(x-2)}{({\color{blue}3}-1)({\color{blue}3}-2)}=\frac{1}{2}x^2-\frac{3}{2}x+1 \\

&\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad\quad ... \\

C_5(x)&=-\frac{3}{2}x^2+\frac{11}{2}x-4 \\
\end{aligned}$$
Degree of each polynomial is at most $d=n-1$
$$\begin{aligned}
C(x)&=w \cdot [C_1(x),C_2(x),...C_5(x)] \\
A(x)&=w \cdot [A_1(x),A_2(x),...A_5(x)] \\
B(x)&=w \cdot [B_1(x),B_2(x),...B_5(x)] \\
\end{aligned}$$
$$w \circ C = w \circ A \circ w \circ B \Leftrightarrow C(x)-A(x)*B(x) = 0,x \in \{1,2,3\}$$
Degree of QAP polynomial is at most $2d$ (at most $2d$ roots)
$$Z(x)=(x-1)(x-2)(x-3)$$
$$\begin{aligned}
H(x)=\frac{C(x)-A(x)*B(x)}{Z(x)}
\end{aligned}$$
According to the interpolation, $x=1,2,...n$ will let the QAP be 0, $H(x)$ is actually other $(2d-n)$ roots for the QAP

Computing $w$ that let the QAP divisible by $Z(x)$ based on the polynomial $C_i(x),A_i(x),B_i(x)$ fixed by the circuit (or finding the quotient equation $H(x)$) is NP-hard. While, given $w$ or $H(x)$, verifying the divisible relationship can be done in polynomial time

Prover does not reveal $H(x)$, instead uses homomorphic hiding to put it on the elliptic curve

## Prove & Verify
Homomorphic hiding & pairing

Trusted Setup: distributed generation ceremony (MPC)
$$\begin{aligned}
CRS_0 &= \{[\tau_0]G,[\tau_0^2]G,...[\tau_0^d]G\} \\
CRS_1 &= \{[\tau_0*\tau_1]G,[\tau_0^2*\tau_1^2]G,...[\tau_0^d*\tau_1^d]G\} \\
&\quad\quad\quad\quad\quad\quad...
\end{aligned}$$
And also provide $[\tau_1]_1,[\tau_1^i]_2$ for the next participant checking the correctness of update
$$\begin{aligned}
e([\tau_0^i]_1,[\tau_1^i]_2)&=e([\tau_0^i*\tau_1^i]_1,G_2) \\
e([\tau_1]_1,[\tau_1^{i-1}]_2)&=e(G_1,[\tau_1^i]_2) \\
\tau &\neq 0
\end{aligned}$$
The first equation checks each element is indeed updated by multiplication, the second equation checks that each multiplication uses the same value


Prover being able to compute $A(\tau),B(\tau),C(\tau)$ doesn't guarantee that he will indeed send the homomorphic hidings of them. Here we use blinded evaluation protocol with **knowledge of coefficient assumption**

To prevent prover from constructing arbitrary $A(x),B(x),C(x)$ based on a $H(x)$，we need to constrain $A(\tau),B(\tau),C(\tau)$ are indeed linear combination of $A_i(\tau),B_i(\tau),C_i(\tau)$ (and the same linear combination), so $[A_i(\tau)]_1,[A_i(\tau)*\alpha_A]_1,[B_i(\tau)]_2, [B_i(\tau)*\alpha_B]_2,[C_i(\tau)]_1, [C_i(\tau)*\alpha_C]_2,[\alpha_A]_2,[\alpha_B]_1,[\alpha_C]_2$ are needed in Trusted Setup. Since prover doesn't know about $\alpha$, he has to do the exact linear combination computation to get $[A(\tau)]_1,[A(\tau)*\alpha_A]_1,[B(\tau)]_2,[B(\tau)*\alpha_B]_1,[C(\tau)]_1,[C(\tau)*\alpha_C]_1$ according to the Trusted Setup terms, otherwise they can not satisfy the $\alpha$ times relationship in the homomorphic hiding space. Since verifier also doesn't know $\alpha$, we need pairing to verify they satisfy the $\alpha$ times relationship through $[\alpha_A]_2,[\alpha_B]_1,[\alpha_C]_2$
$$\begin{aligned}
e(\boxed{[A(\tau)]_1},[\alpha_A]_2) &\overset{\text{?}}{=} e(\boxed{[A(\tau)*\alpha_A]_1},G_2) \\
e([\alpha_B]_1,\boxed{[B(\tau)]_2}) &\overset{\text{?}}{=} e(\boxed{[B(\tau)*\alpha_B]_1},G_2) \\
e(\boxed{[C(\tau)]_1},[\alpha_C]_2) &\overset{\text{?}}{=} e(\boxed{[C(\tau)*\alpha_C]_1},G_2) \\
\end{aligned}$$
To verify $A(\tau),B(\tau),C(\tau)$ are indeed computed through the same linear combination, $[(A(\tau)+B(\tau)+C(\tau))*\beta]_1,[\beta]_1,[\beta]_2$ are needed in Trusted Setup
$$\begin{aligned}
e([A(\tau)+B(\tau)+C(\tau)]_1,[\beta]_2)&=e([(A(\tau)+B(\tau)+C(\tau))*\beta]_1,G_2) \\
e([A(\tau)]_1,[\beta]_2)*e([B(\tau)]_1,[\beta]_2)*e([C(\tau)]_1,[\beta]_2)&=e([(A(\tau)+B(\tau)+C(\tau))*\beta]_1,G_2) \\
e(\boxed{[A(\tau)]_1},[\beta]_2)*e([\beta]_1,\boxed{[B(\tau)]_2})*e(\boxed{[C(\tau)]_1},[\beta]_2) &\overset{\text{?}}{=} e(\boxed{[(A(\tau)+B(\tau)+C(\tau))*\beta]_1},G_2)
\end{aligned}$$
Finally, after ensuring that $A(\tau),B(\tau),C(\tau)$ are indeed the same linear combination $w$ of $A_i(\tau),B_i(\tau),C_i(\tau)$, we need to verify the divisible relationship of the quotient polynomial. Prover should provide $[H(\tau)]_1$, so $G_1,[\tau]_1,[\tau^2]_1...$ are needed in Trusted setup. $Z(x)$ is fixed after the circuit design, so $[Z(\tau)]_2$ can also be in the Trusted Setup


$$\begin{aligned}
A(\tau)*B(\tau)-C(\tau) &= H(\tau)*Z(\tau) \\
e([A(\tau)*B(\tau)-C(\tau)]_1,G_2) &= e([H(\tau)]_1,[Z(\tau)]_2) \\
e([A(\tau)*B(\tau)]_1,G_2)/e([C(\tau)]_1,G_2) &= e([H(\tau)]_1,[Z(\tau)]_2) \\
e(\boxed{[A(\tau)]_1},\boxed{[B(\tau)]_2})/e(\boxed{[C(\tau)]_1},G_2) &\overset{\text{?}}{=} e(\boxed{[H(\tau)]_1},[Z(\tau)]_2)
\end{aligned}$$
>Given $G_1,[\tau]_1,[\tau^2]_1...$ in Trusted Setup, it's sufficient to compute $[A_i(\tau)]...$ from the prover side. However, since they are fixed after the circuit design, so they are also put in the Trusted Setup

>CRS helps the non-interactive property. And terms $[A_i(\tau)]_1,[A_i(\tau)*\alpha_A]_1,[B_i(\tau)]_2, [B_i(\tau)*\alpha_B]_2,[C_i(\tau)]_1, [C_i(\tau)*\alpha_C]_2$ are related to specific circuit, so the Trusted Setup is not universal

**Zero-knowledge property**
Prover add random shift to $A(x),B(x),C(x)$
$$\begin{aligned}
A_\delta(x) &= A(x)+\delta_A*Z(x) \\
B_\delta(x) &= B(x)+\delta_B*Z(x) \\
C_\delta(x) &= C(x)+\delta_C*Z(x)
\end{aligned}$$
$$\begin{aligned}
A_\delta(x)*B_\delta(x)-C_\delta(x) &= [A(x)+\delta_A*Z(x)] * [B(x)+\delta_B*Z(x)] -C(x)-\delta_C*Z(x) \\
&= A(x)*B(x)-C(x) + A(x)*\delta_B*Z(x) + B(x)*\delta_A*Z(x) + \delta_A\delta_B*Z(x)^2 - \delta_C*Z(x) \\
&= [H(x)+A(x)*\delta_B+B(x)*\delta_A+\delta_A\delta_BZ(x)-\delta_C]*Z(x)
\end{aligned}$$
Still can prove that the prover knows $H(x)$ and at the same time hide all the knowledge

**Separate public & private**
Separate $w,A_i(x),B_i(x),C_i(x)$ into left public part and right private part, $w_{public}$ contains outputs and public inputs and is known by verifier

## References
R1CS QAP https://medium.com/@VitalikButerin/quadratic-arithmetic-programs-from-zero-to-hero-f6d558cea649
R1CS QAP https://www.youtube.com/watch?v=TQjzKAkHFls
Bilinear pairings https://medium.com/@VitalikButerin/exploring-elliptic-curve-pairings-c73c1864e627
Bilinear pairings https://www.rareskills.io/post/bilinear-pairing
KCA https://medium.com/@manu.nap/understanding-the-mathematics-of-zksnark-by-manuel-p%C3%A9rez-1d285f24d213
Pinocchio https://medium.com/@VitalikButerin/zk-snarks-under-the-hood-b33151a013f6
Pinocchio paper https://eprint.iacr.org/2013/279.pdf