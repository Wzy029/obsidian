![[Pasted image 20250415204214.png|350]]

(1) T，空集是任何集合的子集
(2) F，如果T⊆S，M⊆S，则S∪T = S∪M恒成立，T和M的关系无法确定
(3) F，如果S⊆T，S-T=∅恒成立，S不一定等于T
(4) T，设全集中一个元素x∈S，则x∉~S，又x∈~S∪T，所以x∈T，即∀x∈S，x∈T，S⊆T
(5) F，S≠∅时，S-S=∅≠S


![[Pasted image 20250415205406.png|450]]
(1) 
	(a) n=1, 1\*(1+1) = 2 = 1/3 \* 1 \*(1+1) \*(1+2)
	(b) 设n=k时, $\sum^{k}_{i = 1}{i(i+1)} ={\frac{1}{3}}k(k+1)(k+2)$
	(c) 则n=k+1时，$\sum^{k+1}_{i = 1}{i(i+1)} ={\frac{1}{3}}k(k+1)(k+2) + (k+1)(k+2) = {\frac{1}{3}}(k+1)(k+2)(k+3)$
	(d) 又n=1时成立，因此对于$n∈N_+$，式子成立

(2)
	(a) n=1, $\frac{1}{\sqrt{1}}=1 < 2\sqrt{1}=2$
	(b)设n=k时式子成立，$\frac{1}{\sqrt{1}}+\frac{1}{\sqrt{2}}+\frac{1}{\sqrt{3}}+...+\frac{1}{\sqrt{k}} < 2\sqrt{k}$
	(c) n=k+1时，$\frac{1}{\sqrt{1}}+\frac{1}{\sqrt{2}}+\frac{1}{\sqrt{3}}+...+\frac{1}{\sqrt{k+1}} < 2\sqrt{k}+\frac{1}{\sqrt{k+1}}$
			$4k^2+4k < 4k^2+4k+1$
			$2\sqrt{k(k+1)} < 2k+1$
			$2\sqrt{k(k+1)} +1 < 2k+2$
			$2\sqrt{k} + \frac{1}{\sqrt{k+1}}< 2\sqrt{k+1}$
			∴ $\frac{1}{\sqrt{1}}+\frac{1}{\sqrt{2}}+\frac{1}{\sqrt{3}}+...+\frac{1}{\sqrt{k+1}} < 2\sqrt{k}+\frac{1}{\sqrt{k+1}} < 2\sqrt{k+1}$ 
	(d)又n=1时成立，因此对于$n∈N_+$，式子成立

![[Pasted image 20250415211523.png|500]]
(1)![[Pasted image 20250415212033.png|225]]
(2)
$$
\begin{bmatrix}
1 & 1 & 0&1 \\
1&1&0&0\\
0&0&1&0\\
1&0&0&1
\end{bmatrix}
$$
![[Pasted image 20250415212246.png|650]]
(1)是。
	单射：不是，f(3)=f(5)=9
	满射：不是，A中不存在x使f(x)=7
	双射：不是，不是单射也不是满射
(2)不是。A中存在一些元素，在B中没有对应的值
(3)是。
	单射：不是，f(0)=f(1)=0
	满射：不是，B中小于-0.25的值没有对应的x
	双射：不是，因为既不是单射也不是满射
(4)是。
	单射：是，f单调，x对应唯一f(x)
	满射：不是，A中不存在x使f(x)=1
	双射：不是，因为不是满射
