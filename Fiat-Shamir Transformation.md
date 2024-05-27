## Interactive Oracle Proofs (IOP)

Combining the Schwartz–Zippel Lemma and Polynomial Commitment:

- If I can provide the correct evaluation of $f(x)$ at a random $x0$​, it proves that I know the full knowledge of $f(x)$

- Polynomial Commitment for $f(x)$ can be used to prove that $(x_0, y_0)$ is a correct evaluation

- The Schwartz–Zippel Lemma transforms the Proof of Knowledge problem into proving the correctness of a random evaluation in a polynomial

- Polynomial Commitment provides a method to prove the correctness of a polynomial's evaluation without exposing the polynomial to the verifier

And we get an **Interactive** Proof of Knowledge based on Polynomial Commitment

1. Alice generates a polynomial $f(x)$ using the knowledge vector as evaluations

2. Alice generates a Commitment $C$ for $f(x)$ and sends to Bob

3. Bob randomly selects $x_0$ and sends to Alice

4. Alice returns $y_0$ and the proof to Bob

5. Bob verifies the correctness using $C$, $(x_0, y_0)$, and the proof

6. If correct, it demonstrates that Alice knows the complete knowledge of $f(x)$

## Fiat-Shamir Transformation

Non-interactive Proof of Knowledge based on the Random Oracle model

- From Bob randomly selects $x_0$​ and gives it to Alice

- To Alice computes $x_0 = hash(C)$ on her own