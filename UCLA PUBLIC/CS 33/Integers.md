## Two's complement:
- To convert, negate and add one
- If signed, subtract the most significant bit value and add the rest
- Unisiged:
	- UMin = $0$ (000000...00)
	- UMax = $2^w - 1$ (111111....00)
- signed:
	- TMin = $0$ (1000....000)
	- TMax = $2^w - 1$ (01111111)
	- $-1 = 11111....111$
- $| TMin | = TMax + 1$
- $UMax = 2 \times TMax + 1$
- Comparisons between signed and unsigned compares both as unsigned numbers
- Sign extension (taking a signed value and placing it in a larger int size): take 
## Operations
- u << k gives $u * 2^k$
- u >> k gives $\lfloor u / 2^k \rfloor$