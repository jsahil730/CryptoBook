# Coppersmith algorithm

This algorithm solves for small roots of polynomials modulo any integer, meaning given some polynomial $$f(x)\in\mathbb Z[x]$$of degree $$d$$and any integer $$N$$, then if $$f(x_0)=0\pmod{N},|x_0|<N^{\frac1d}$$, this algorithm can find$$x_0$$with time polynomial in $$\log N$$and $$d$$. The key idea behind this algorithm is to construct a polynomial$$g(x)$$such that $$g(x_0)=0$$in $$\mathbb R$$. As roots of polynomials over the reals can be found easily, this gives an easy way to find $$x_0$$. We shall introduce the Coppersmith algorithm in a few iterations, with each iteration approaching the $$N^{\frac1d}$$bound.

## Polynomials in lattices

We first consider a criteria for a root of a polynomial modulo$$N$$to exists over the reals as well. Suppose$$f(x)=\sum_{i=0}^df_ix^i$$is a polynomial of degree$$d$$. Define the $$\ell_2$$norm$$\left\lVert f(x)\right\rVert_2$$ of a polynomial to be $$\sqrt{\sum_{i=0}^df_i^2}$$. Given $$|x_0|<B,f(x_0)=0\pmod N$$, if

$$
\left\lVert f(Bx)\right\rVert_2=\sqrt{\sum_{i=0}^d\left(f_iB^i\right)^2}\leq\frac N{\sqrt{d+1}}
$$

then $$f(x_0)=0$$ in $$\mathbb R$$. The proof is a relatively straightforward chain of inequalities:

$$
\begin{align*}
\frac N{\sqrt{d+1}}&\geq\sqrt{\sum_{i=0}^d\left(f_iB^i\right)^2}\\&\geq\sqrt{\sum_{i=0}^d\left(f_ix_0^i\right)^2}\\
&\geq\frac1{\sqrt{d+1}}\sum_{i=0}^d\left|f_ix_0^i\right|\\
&\geq\frac1{\sqrt{d+1}}\left|\sum_{i=0}^df_ix_0^i\right|\\
\end{align*}
$$

and since $$f(x_0)=0\pmod N$$implies $$f(x_0)=kN$$for some $$k\in\mathbb Z$$, we know that $$k$$must be$$0$$to satisfy the inequality above. 

With this, if we can find some polynomials $$f_i$$such that $$f_i(x_0)=0\pmod N$$, then if we can find some $$c_i$$such that $$\left\lVert\sum_ic_if_i(x)\right\rVert_2\leq\frac N{\sqrt{d+1}}$$, then we can find $$x_0$$easily. This gives a brief idea as to why lattices would be useful in such a problem.

To use lattices, notice that we can encode the polynomial $$f(x)=\sum_{i=0}^df_ix^i$$as the vector with components $$f_i$$. In this way, adding polynomials and multiplying polynomials by numbers still makes sense. Lets suppose that $$f(x_0)=0\pmod N,x_0<B$$and $$f_d=1$$\(otherwise multiply$$f$$by $$f_d^{-1}\pmod N$$. Consider the polynomials $$g_i(x)=Nx^i$$and consider the lattice$$L$$generated by $$f(Bx)$$and $$g_i(Bx)$$, $$0\leq i\leq d-1$$. As a matrix, the basis vectors are

$$
\mathcal B=\begin{pmatrix}
        N&0&0&\dots&0&0\\
        0&NB&0&\dots&0&0\\
        0&0&NB^2&\dots&0&0\\
        \vdots&\vdots&\vdots&\ddots&\vdots&\vdots\\
        0&0&0&\dots&NB^{d-1}&0\\
        f_0&f_1B&f_2B^2&\dots&f_{d-1}B^{d-1}&B^d\\
    \end{pmatrix}
$$

As every element in this lattice is some polynomial $$g(Bx)$$, if $$f(x_0)=0\pmod N$$, then$$g(x_0)=0\pmod N$$. Furthermore, if $$|x_0|<B$$and a short vector$$v(Bx)$$has length less than $$\frac N{\sqrt{d+1}}$$, then we have $$v(x_0)=0$$in $$\mathbb R$$.

The volume of this lattice is $$N^dB^{\frac{d(d+1)}2}$$and the lattice has dimension $$d+1$$. By using the LLL algorithm, we can find a vector $$v(Bx)$$ with length at most

$$
\left\lVert v(Bx)\right\rVert_2=\underbrace{\left(\frac4{4\delta-1}\right)^{\frac d4}}_{c_{\delta,d}}\text{vol}(L)^\frac1{d+1}=c_{\delta,d}N^{\frac d{d+1}}B^{\frac d2}
$$

As long as $$c_{\delta,d}N^{\frac d{d+1}}B^{\frac d2}<\frac N{\sqrt{d+1}}$$, then by the above criteria we know that this vector has $$x_0$$has a root over $$\mathbb R$$. This tells us that 

$$
B<N^{\frac2{d(d+1)}}\left(c_{\delta,d}\sqrt{d+1}\right)^{-\frac 2d}
$$

While this isn't the $$N^{\frac1d}$$bound that we want, this gives us an idea of what we can do to achieve this bound, i.e. add more vectors such that the length of the shortest vector decreases.

## Achieving the $$N^{\frac1d}$$bound

One important observation to make is that any coefficients in front of$$N^x$$does not matter as we can simply brute force the top bits of our small root in $$O(1)$$time. Hence we only need to get$$B=kN^{\frac1d}$$for some fixed constant$$k$$.

In order to achieve this, notice that if $$f(x_0)=0\pmod N$$, then $$f(x_0)^h=0\pmod{N^h}$$. This loosens the inequality required for a polynomial to have $$x_0$$as a small root as our modulus is now larger. With this in mind, consider the polynomials

$$
g_{i,j}(x)=N^{h-j}f(x)^jx^i\quad0\leq i<d,0\leq j<h
$$

where we will determine$$h$$later. Here $$g_{i,j}(x_0)=0\pmod{N^h}$$, hence we shall consider the lattice $$L$$ generated by $$g_{i,j}(Bx)$$. As an example, if we have

$$
f(x)=x^3+2x^2+3x+4\quad h=3
$$

the basis vectors of our lattice would look like

$$
\footnotesize{\left(\begin{array}{rrrrrrrrr}
N^{3} & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & B N^{3} & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & B^{2} N^{3} & 0 & 0 & 0 & 0 & 0 & 0 \\
4 \, N^{2} & 3 \, B N^{2} & 2 \, B^{2} N^{2} & B^{3} N^{2} & 0 & 0 & 0 & 0 & 0 \\
0 & 4 \, B N^{2} & 3 \, B^{2} N^{2} & 2 \, B^{3} N^{2} & B^{4} N^{2} & 0 & 0 & 0 & 0 \\
0 & 0 & 4 \, B^{2} N^{2} & 3 \, B^{3} N^{2} & 2 \, B^{4} N^{2} & B^{5} N^{2} & 0 & 0 & 0 \\
16 \, N & 24 \, B N & 25 \, B^{2} N & 20 \, B^{3} N & 10 \, B^{4} N & 4 \, B^{5} N & B^{6} N & 0 & 0 \\
0 & 16 \, B N & 24 \, B^{2} N & 25 \, B^{3} N & 20 \, B^{4} N & 10 \, B^{5} N & 4 \, B^{6} N & B^{7} N & 0 \\
0 & 0 & 16 \, B^{2} N & 24 \, B^{3} N & 25 \, B^{4} N & 20 \, B^{5} N & 10 \, B^{6} N & 4 \, B^{7} N & B^{8} N
\end{array}\right)}
$$

We have the following immediate computations of $$L$$:

$$
\dim(L)=dh\quad\text{vol}(L)=N^{\frac{dh(h+1)}2}B^{\frac{(dh-1)dh}2}
$$

hence when using the LLL algorithm, the shortest LLL basis vector$$v(Bx)$$has length

$$
\begin{align*}
\left\lVert v(Bx)\right\rVert_2&=\left(\frac4{4\delta-1}\right)^{\frac{\dim(L)-1}4}\text{vol}(L)^{\frac1{\dim(L)}}\\
&=\left(\frac4{4\delta-1}\right)^{\frac{dh-1}4}N^{\frac{h+1}2}B^{\frac{dh-1}2}\\
\end{align*}
$$

and we need $$\left\lVert v(Bx)\right\rVert_2<\frac{N^h}{\sqrt{dh}}$$for $$v(x_0)=0$$. Hence we have

$$
B<\sqrt{\frac{4\delta-1}4}\left(\frac1{dh}\right)^{\frac1{dh-1}}N^{\frac{h-1}{dh-1}}
$$

Since $$\lim_{h\to\infty}\frac{h-1}{dh-1}=\frac1d$$, this will achieve the $$N^{\frac1d}$$bound that we want. However as for big $$h$$, the LLL algorithm would take a very long time, we typically choose a suitably large$$h$$such that the algorithm is still polynomial in $$\log N,d$$and brute force the remaining bits.

## Exercises

1\) We often see $$h=\left\lceil\max\left(\frac{d+d\varepsilon-1}{d^2\epsilon},\frac7d\right)\right\rceil$$in literature. We shall now show where this mysterious$$\frac7d$$comes from. The other term will appear in the next exercise. Typically, one sets $$\delta=\frac34$$to simplify calculations involving the LLL algorithm as $$\frac4{4\delta-1}=2$$. Suppose we want $$B>\frac12N^{\frac{h-1}{dh-1}}$$, show that this gives us $$dh\geq7$$.

2\) We show that we can indeed find small roots less than$$N^{\frac1d}$$in polynomial time. In the worse case, the longest basis vector cannot exceed $$O\left(B^{dh-1}N^h\right)$$. Hence the LLL algorithm will run in at most$$O(d^6h^6(d+h)^2\log^2N)$$time.

Let

$$
\varepsilon=\frac1d-\frac{h-1}{dh-1}\quad h=\frac{d+d\varepsilon-1}{d^2\epsilon}\approx\frac1{d\varepsilon}
$$

and choose $$\varepsilon=\frac1{\log N}$$, then $$N^\varepsilon$$is a constant hence the number of bits needed to be brute forced is a constant. This gives us the approximate run time of $$O((d+\frac1d\log N)^2\log^8N)$$.

3\) We shall show that this is the best bound we can hope for using lattice methods. Suppose there exists some algorithm that finds roots less than $$O\left(N^{\frac1d+\epsilon}\right)$$in polynomial time. Then consider the case when$$N=p^2$$and $$f(x)=x^2+px$$. Show that this forces the lattice to have a size too big for the algorithm to run in polynomial time, assuming the algorithm finds all small roots.


