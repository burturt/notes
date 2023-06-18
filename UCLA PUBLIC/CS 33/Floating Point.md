- FP stored as `s | exp | frac`
- Normalized:
	- "normal" numbers, with exp that is not all 0s or all 1s
	- $(-1)^s * M * 2^{E}$
	- Bias is $2^{k-1} - 1$ with k bits for exp
		- ex. for 8 bit exponent, bias is 127
	- $E = \text{exp} - \text{Bias}$
- Number --> float:
	- Convert number to binary decimal
	- Move decimal place over to left until only a leading `1` is left, and save this many jumps as E
	- drop the 1 on number, and set this to frac/mantissa portion of floating point
	- convert E to exp: $E + Bias = exp$, store exp
- Denormalized
	- exp is all 0s
	- E = 1 - Bias, implicit leading number is a 0 not 1
	- Used when very close to zero
	- If frac=0, then represents signed zero
- Exceptions
	- Exp is all 1s
	- frac all 0s: represents infinity
	- Frac not all 0s: represents NaN
## Converting to binary:
![Screen Shot 2023-06-01 at 2.26.41 PM.png](../../_resources/Screen%20Shot%202023-06-01%20at%202.26.41%20PM.png)
## Floating point operations:


![Screen Shot 2023-04-29 at 2.19.15 PM.png](../../_resources/Screen%20Shot%202023-04-29%20at%202.19.15%20PM.png)

![Screen Shot 2023-04-29 at 2.26.51 PM.png](../../_resources/Screen%20Shot%202023-04-29%20at%202.26.51%20PM.png)

![Screen Shot 2023-04-29 at 2.27.03 PM.png](../../_resources/Screen%20Shot%202023-04-29%20at%202.27.03%20PM.png)


- 1/4/3 for 8bit, 1/8/23 for float, 1/11/52 for double, 1/15/63 or 64 for 80 bit floating point,