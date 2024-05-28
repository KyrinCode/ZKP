Inner Product Argument

To prove the evaluation of polynomial $f(x)$ at $x$ is $y$

$$\begin{aligned}
\overrightarrow{b} &= (1,x,x^2,...x^{n-1}) \\
y &= <\overrightarrow{a}, \overrightarrow{b}> \\
\end{aligned}$$

Commitment of $f(x)$ is

$$C_f = <\overrightarrow{a}, \overrightarrow{G}>$$

Before the first reduction step

$$\begin{aligned}
\overrightarrow{a^{k-1}} &= \overrightarrow{a} \\
\overrightarrow{G^{k-1}} &= \overrightarrow{G} \\
\overrightarrow{b^{k-1}} &= \overrightarrow{b}
\end{aligned}$$

Given basis $\overrightarrow{G},Q$, we want to claim that $C^{k-1}$ has Inner Product Property with the form $C^{k-1}=<\overrightarrow{a},\overrightarrow{G}>+[<\overrightarrow{a},\overrightarrow{b}>]Q$

Public

+ $(C_f,x,y)$

Proof

+ $(\overrightarrow{L},\overrightarrow{R},G^0,a^0)$ which is $logn$ size

$$\begin{aligned}
C^{k-1} &= C_f+[y]Q\\
&= <\overrightarrow{a},\overrightarrow{G}>+[<\overrightarrow{a},\overrightarrow{b}>]Q \\
&= <\overrightarrow{a^{k-1}},\overrightarrow{G^{k-1}}>+[<\overrightarrow{a^{k-1}},\overrightarrow{b^{k-1}}>]Q \\
\end{aligned}$$

$L,R$ are accumulating cross terms and challenges

$$\begin{aligned}
L_{k-1} &= <\overrightarrow{a^{k-1}_{low}}, \overrightarrow{G^{k-1}_{high}}>+[<\overrightarrow{a^{k-1}_{low}}, \overrightarrow{b^{k-1}_{high}}>]Q \\
R_{k-1} &= <\overrightarrow{a^{k-1}_{high}}, \overrightarrow{G^{k-1}_{low}}>+[<\overrightarrow{a^{k-1}_{high}}, \overrightarrow{b^{k-1}_{low}}>]Q \\
u_{k-1} &= Hash(L_{k-1},R_{k-1})
\end{aligned}$$

Update $\overrightarrow{a},\overrightarrow{G},\overrightarrow{b}$, the size of them shrink to half in each reduction step

$$\begin{aligned}
\overrightarrow{a^{k-2}} &= u_{k-1}\overrightarrow{a^{k-1}_{low}}+u_{k-1}^{-1}\overrightarrow{a^{k-1}_{high}} \\
\overrightarrow{G^{k-2}} &= u_{k-1}^{-1}\overrightarrow{G^{k-1}_{low}}+u_{k-1}\overrightarrow{G^{k-1}_{high}} \\
\overrightarrow{b^{k-2}} &= u_{k-1}^{-1}\overrightarrow{b^{k-1}_{low}}+u_{k-1}\overrightarrow{b^{k-1}_{high}}
\end{aligned}$$
$$\begin{aligned}

C^{k-2} &= <\overrightarrow{a^{k-2}},\overrightarrow{G^{k-2}}>+[<\overrightarrow{a^{k-2}},\overrightarrow{b^{k-2}}>]Q \\

&= <u_{k-1}\overrightarrow{a^{k-1}_{low}}+u_{k-1}^{-1}\overrightarrow{a^{k-1}_{high}},u_{k-1}^{-1}\overrightarrow{G^{k-1}_{low}}+u_{k-1}\overrightarrow{G^{k-1}_{high}}> + [<u_{k-1}\overrightarrow{a^{k-1}_{low}}+u_{k-1}^{-1}\overrightarrow{a^{k-1}_{high}},u_{k-1}^{-1}\overrightarrow{b^{k-1}_{low}}+u_{k-1}\overrightarrow{b^{k-1}_{high}}>]Q \\

&= <\overrightarrow{a^{k-1}_{low}}, \overrightarrow{G^{k-1}_{low}}>+<\overrightarrow{a^{k-1}_{high}}, \overrightarrow{G^{k-1}_{high}}>+[u_{k-1}^{-2}]<\overrightarrow{a^{k-1}_{high}}, \overrightarrow{G^{k-1}_{low}}>+[u_{k-1}^{2}]<\overrightarrow{a^{k-1}_{low}}, \overrightarrow{G^{k-1}_{high}}> + \\
& \quad \{<\overrightarrow{a^{k-1}_{low}}, \overrightarrow{b^{k-1}_{low}}>+<\overrightarrow{a^{k-1}_{high}}, \overrightarrow{b^{k-1}_{high}}>+[u_{k-1}^{-2}]<\overrightarrow{a^{k-1}_{high}}, \overrightarrow{b^{k-1}_{low}}>+[u_{k-1}^{2}]<\overrightarrow{a^{k-1}_{low}}, \overrightarrow{b^{k-1}_{high}}>\}Q \\

&= <\overrightarrow{a^{k-1}}, \overrightarrow{G^{k-1}}>+[<\overrightarrow{a^{k-1}}, \overrightarrow{b^{k-1}}>]Q + \\
& \quad [u_{k-1}^{2}]\{<\overrightarrow{a^{k-1}_{low}}, \overrightarrow{G^{k-1}_{high}}>+[<\overrightarrow{a^{k-1}_{low}}, \overrightarrow{b^{k-1}_{high}}>]Q\} + [u_{k-1}^{-2}]\{<\overrightarrow{a^{k-1}_{high}}, \overrightarrow{G^{k-1}_{low}}>+[<\overrightarrow{a^{k-1}_{high}}, \overrightarrow{b^{k-1}_{low}}>]Q\} \\

&= C^{k-1}+[u_{k-1}^2]L_{k-1}+[u_{k-1}^{-2}]R_{k-1}
\end{aligned}$$

In each round, $u$ relies on the result of the folding scheme, so the prover can not construct a evaluation $y \neq <\overrightarrow{a},\overrightarrow{b}> = <\overrightarrow{a^{k-1}},\overrightarrow{b^{k-1}}>$ to bypass the verification

Take one step as example, verifier can get $C^{k-2}$ through computing $G^{k-2},b^{k-2}$ with $u=Hash(L_{k-1},R_{k-1})$ and $\overrightarrow{a^{k-2}}$ given by the prover (won't leak $\overrightarrow{a^{k-1}}$), then compare it with $C^{k-1}+[u_{k-1}^2]L_{k-1}+[u_{k-1}^{-2}]R_{k-1}$

Final step, the size of $\overrightarrow{a},\overrightarrow{G},\overrightarrow{b}$ will be just 1

$$\begin{aligned}
C^0 &= \boxed{[a^0]G^0+[a^0b^0]Q} \\
&= C^1+[u_1^2]L_1+[u_1^{-2}R_1] \\
&= \boxed{C^{k-1}+\sum\limits_{i\in\{1,...k-1\}}[u_i^2]L_i+[u_i^{-2}]R_i}
\end{aligned}$$

Verifier checks

$$\begin{aligned}
[a^0]G^0+[a^0b^0]Q &= C^{k-1}+\sum\limits_{i\in\{1,...k-1\}}[u_i^2]L_i+[u_i^{-2}]R_i
\end{aligned}$$

Left for verifier to do

+ compute $b^0$ from $\overrightarrow{b}$ following the folding scheme (succinct check $O(logn)$ )

+ check $G^0$ was correctly constructed (expensive check $O(n)$ )

## References

IPA https://dankradfeist.de/ethereum/2021/07/27/inner-product-arguments.html

IPA in Halo2 https://www.youtube.com/watch?v=RaEs5mnXIhY