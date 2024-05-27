## Trusted Setup
+ Toxic waste
	+ $\tau$
	+ $\alpha,\beta$ secret shifting force $A_i(x),B_i(x),C_i(x)$ to use the same linear combination $w$ and can not make up $[A(\tau)]_1,[B(\tau)]_1,[B(\tau)]_2$ since prover doesn't know $\alpha,\beta$
	+ $\gamma,\delta$ work on $K_{public}(x),K_{private}(x)$ to make the public inputs outputs independent from the other witness components, and $\delta$ forces prover to use the right $w_{private}$, otherwise $\delta$ will not cancel out during verification
+ witness $w$ with $m$ variables, of which $l$ as public inputs outputs
+ $n$ constraints, order $d=n-1$
+ $A_i(x),B_i(x),C_i(x)$
+ $Z(x)=(x-1)(x-2)...(x-n)$
+ $K_i(x)=\beta*A_i(x)+\alpha*B_i(x)+C_i(x)$
+ Proving key
	+ $G_1$
	+ $[\alpha]_1,[\delta]_1$
	+ $[\tau]_1,[\tau]_2,...[\tau]_d$ for computing $[A(\tau)]_1,[B(\tau)]_1$ (can also be replaced by $[A_i(\tau)]_1,[B_i(\tau)]_1$)
	+ $[K_{l+1}(\tau)/\delta]_1,...[K_{m}(\tau)/\delta]_1$ for computing $[K_{private}(\tau)]_1$
	+ $[\tau Z(\tau)/\delta]_1,[\tau^2 Z(\tau)/\delta]_1,...[\tau^{d_H} Z(\tau)/\delta]_1$ to force the prover compute $[H(\tau)*Z(\tau)/\delta]_1$ with $H(x)$'s coefficients. Without $\delta$, prover can bypass compute $H(x)*Z(x)$ by using $A(x)*B(x)-C(x)$, where $A(x),B(x)$ are also forged in $A_1,B_2$
	+ $G_2$
	+ $[\beta]_2,[\delta]_2$
	+ $[\tau]_1,[\tau]_2,...[\tau]_d$ for computing $[B(\tau)]_2$ (can also be replaced by $[B_i(\tau)]_2$)
+ Verification key
	+ $[K_1(\tau)/\gamma]_1,...[K_l(\tau)/\gamma]_1$
	+ $[\gamma]_2,[\delta]_2$
	+ $e([\alpha]_1,[\beta]_2)$

$$\begin{aligned}
K_i(x)&=\beta*A_i(x)+\alpha*B_i(x)+C_i(x) \\
K(x)&=w \cdot [K_1(x),K_2(x),...K_m(x)] \\
&=\beta*A(x)+\alpha*B(x)+C(x) \\
K_{public}(x)&=w_{public} \cdot [K_1(x),...K_l(x)] \\
K_{private}(x)&=w_{private} \cdot [K_{l+1},...K_m(x)]
\end{aligned}$$
## Prove
$$\begin{aligned}[]
\boxed{A_1}&=[\alpha]_1+[A(\tau)]_1+[r*\delta]_1 \\
B_1&=[\beta]_1+[B(\tau)]_1+[s*\delta]_1 \\
\boxed{B_2}&=[\beta]_2+[B(\tau)]_2+[s*\delta]_2 \\
\boxed{C_1}&=[K_{private}(\tau)/\delta]_1+[H(\tau)*Z(\tau)/\delta]_1+s*A_1+r*B_1-[r*s*\delta]_1 \\
&=[K_{private}(\tau)/\delta]_1+[H(\tau)*Z(\tau)/\delta]_1+[s*\alpha]_1+[s*A(\tau)]_1+[r*s*\delta]_1+[r*\beta]_1+[r*B(\tau)]_1
\end{aligned}$$
$r,s$ are for zero-knowledge to protect $w$
## Verify
$$\begin{aligned}
e(A_1,B_2)&=e([\alpha]_1,[\beta]_2)*e([K_{public}(\tau)/\gamma]_1,[\gamma]_2)*e(C_1,[\delta]_2) \\
A_1*B_2&=[\alpha]_1*[\beta]_2+[K_{public}(\tau)/\gamma]_1*[\gamma]_2+C_1*[\delta]_2
\end{aligned}$$
$$\begin{aligned}
Left&=[\alpha+A(\tau)+r*\delta]*[\beta+B(\tau)+s*\delta] \\
&=\alpha*\beta+\alpha*B(\tau)+s*\alpha*\delta+\beta*A(\tau)+\boxed{A(\tau)*B(\tau)}+s*\delta*A(\tau)+r*\beta*\delta+r*\delta*B(\tau)+r*s*\delta^2 \\
Right&=\alpha*\beta+K_{public}(\tau)+K_{private}(\tau)+H(\tau)*Z(\tau)+s*\alpha*\delta+s*\delta*A(\tau)+r*s*\delta^2+r*\beta*\delta+r*\delta*B(\tau) \\
&=\alpha*\beta+\beta*A(\tau)+\alpha*B(\tau)+\boxed{C(\tau)}+\boxed{H(\tau)*Z(\tau)}+s*\alpha*\delta+s*\delta*A(\tau)+r*s*\delta^2+r*\beta*\delta+r*\delta*B(\tau)
\end{aligned}$$

## Applications
+ Tornado Cash https://docs.tornadoeth.cash/tornado-cash-classic/circuits/core-deposit-circuit https://www.rareskills.io/post/how-does-tornado-cash-work

## References
Groth16 https://www.zeroknowledgeblog.com/index.php/groth16
Groth16 https://www.rareskills.io/post/groth16
Groth16 https://blog.lambdaclass.com/groth16/
Groth16 https://xn--2-umb.com/22/groth16
Malleability attacks https://medium.com/ppio/how-to-generate-a-groth16-proof-for-forgery-9f857b0dcafd
Groth16 paper https://eprint.iacr.org/2016/260.pdf