# Intro Module
## ASCII
- Encoding standard using 7-bit representation
- Uses integers 0-127
	- For example, a = 97, b = 98, ... etc
```python
chr() # allows decoding ASCII chars

# for example (from the challenge):
my_arr = [99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 52, 98, 108, 51, 125]

flag = ''
for i in range(len(my_arr)):
	flag += ''.join(chr(my_arr[i])) # concatenate letters to one string
```
## Hexadecimal
- A way to represent ASCII strings
	- Even though they do _not_ directly translate to them
- Can be decoded using the `bytes.fromhex()` tool in Python
	- Opposite: `print(your_string.hex())`
- Process:
	1. Each letter is converted to ASCII char
	2. Decimal number is converted to base-16
	3. Boom, you have your string!
```python
# challenge example:
encoded_flag = "63727970746f7b596f755f77696c6c5f62655f776f726b696e675f776974685f6865785f737472696e67735f615f6c6f747d"

flag_bytes = bytes.fromhex(encoded_flag) # hexadecimal -> readable strings
```
## XOR
It's pretty dope, and you know what it does, so nothing new to add here, really.
However, here are some things to keep in mind when you do the challenges:
- Is it in ASCII form? Remember to use `chr()` and `ord()`
	- If in string form, put string in SINGLE quotes: `my_str = 'example'` 
- Use the `^` operator in Python to XOR
- You might have to XOR certain bits to the whole string for certain encrypted keys. If so, you gotta iterate through it and brute force it that way
- ==Remember CyberChef??== It's great for XORs!

`xor_starter.py` was XOR'ing each bit in a string with 13
`xor_props.py` was finding the flag given a bunch of XORed keys
`xor_secret.py` was finding the key used, then decoding the flag based on the key
### Properties
- Commutative = `A ^ B = B ^ A`
- Associative = `A ^ (B ^ C) = (A ^ B) ^ C`
- Identity = `A ^ 0 = A`
- Self-Inverse = `A ^ A = 0`

---
# Modular Arithmetic
## Greatest Common Divisor (GCD)
AKA the highest common factor

We just take the divisors of two numbers and find the greatest (shocking algorithm)
$a = 12, b = 8$
$a: \{1,2,3,4,6,12\}$
$b: \{1,2,4,8\}$
`gcd(a, b) = 4`

Python has this function under the `math` library:
```python
import math
print("The GCD of 12 and 8 is: ", math.gcd(12, 8))
```
### Extended GCD
We do this to find integers $u$ or $v$ -- this will be useful for decrypting RSA ciphertexts.
$a \cdot u + b \cdot v = \text{gcd}(a,b)$

Let $p = 252, q = 198$
**Steps to solving:**
	$252 = 198(1) + x$
	$252 \div 198 = 1.272$
	$252 - 198 = 54$
	$x = 54$
*--> Move 198 and x down:*
	$198 = 54(3) + 36$
	$54 = 36(1) + 18$
	$36 = 18(2) + 0$ 
	$18 = 0(?) +\ ?$
--> $\text{gcd}(252,198) = 18$
	$18 = 54 - 36(1)$       [eqn1]
	$36 = 198 - 54(3)$     [eqn2]
	$54 = 252 - 198(1)$   [eqn3]
	$18 = 54 - 36(1)$
**Substitute:**
	$18 = 54 - (198 - 54(3))$
	$18 = 54(4) - 198$
	$18 = (252 - 198(1))(4) - 198$
	$18 = 252(4) - 198(5)$
This format looks similar, right?
Well, guess what: $u = 4,$ and $v = 5$ !

How to \*roughly\* implement with Python:
```python
# per user fecell's solution:
def egcd(a, b):
	x,y, u,v = 0,1, 1,0
	while a != 0:
		q, r = b//a, b%a
		m, n = x-u*q, y-v*q
		b,a, x,y, u,v = a,r, u,v, m,n
	gcd = b
	return gcd, x, y
```

## Back to Modular Arithmetic
Fermat's Little Theorem: https://en.wikipedia.org/wiki/Fermat%27s_little_theorem

> **FLT.1**: If _p_ is a prime number, then for any integer _a_, the number $a^{p-p}$ is an integer multiple of _p_.
> *in other words*: $a^p \equiv a$ mod $p \iff a^p$ mod $p = a$
> **FLT.2**: If _a_ is NOT divisible by _p_, then $a^{(p-1) - 1}$ is an integer multiple of _p_.
> *in other words*: $a^{p-1} \equiv 1$ mod $p \iff a^{p-1}$ mod $p = 1$

In other words, the challenge showed us this:
Let _p_ = 17.
$n^p$ mod $p$
$3^{17}$ mod $17 = 3$
$5^{17}$ mod $17 = 5$
How about $7^{16}$ mod $17$ ?
--> 6, because...
$$
\begin{align}
7^{17}\ \text{mod}\ 17 &= 7 \\
7^{17-1}\ \text{mod}\ 17 &= 7-1 \\
7^{16}\ \text{mod}\ 17 &= 6
\end{align}
$$
### Inverting
Main equation: $g \cdot d \equiv 1\ \text{mod}\ p$.
Example, $7 \cdot 8 = 56 \equiv 1\ \text{mod}\ 11$

Challenge explained:
$3 \cdot d \equiv 1\ \text{mod}\ 13$
They say that $d = 3^{-1}$ , and we know $g = 3$ and $p = 13$.
$d$ isn't *actually* $3^{-1}$, but we just want to find the inverse.
The solution is using Python's `pow()` function, because it has that extra argument for mod (p).
```python
# pow(base, exp, mod)
pow(g, exp, p)
pow(3, -1, 13) # = 9
```
## Quadratic Residues

> An integer, *x*, is a Quadratic Residue if there exists an *a* such that: $a^2 \equiv x\ \text{mod}\ p$.
> If there is no such solution, then the integer is a *Quadratic Non-Residue*.

$$x^2 \equiv c \text{ mod } p$$

This is for, essentially, the square root of an *a* in the form of our equations. 
Take this example: Let p = 29, a = 11.
Calculate $a^2 = 5\ \text{mod}\ 29$.
They say $a = 11$, $a^2 = 5$, and "we say the square root of 5 is 11."

*Sorry, but what the hell does that actually mean?* Here's the _rough_ idea:
Let _p_ = 7, then...
$$
\begin{align}
\mathbb{F}^*_p = \{1,2,3,4,5,6\}\\
1^2\ \text{mod } 7 = 1\\
2^2\ \text{mod } 7 = 4\\
3^2\ \text{mod } 7 = 2\\
4^2\ \text{mod } 7 = 2\\
5^2\ \text{mod } 7 = 4\\
6^2\ \text{mod } 7 = 1\\
\\
\text{Quadratic Residue } &= \{1,2,4\}\\
\text{Quadratic Non-Residue} &= \{3,5,6\}
\end{align}
$$
How about another example?
Let *p* = 5, then...
$$
\begin{align}
\mathbb{F}^*_p = \{0,1,2,3,4\}\\
0^2\ \text{mod } 5 = 0\\
1^2\ \text{mod } 5 = 1\\
2^2\ \text{mod } 5 = 4\\
3^2\ \text{mod } 5 = 9 \text{ mod } 5 = 4\\
4^2\ \text{mod } 5 = 16 \text{ mod } 5 = 1\\
\\
\text{Quadratic Residue} = \{1,4\}\\
\text{Quadratic Non-Residue} = \{2,3\}
\end{align}
$$
#### Properties:
Let QR = Quadratic Residue, and QNR = Quadratic Non-residue. Then:
$$
\begin{align}
\text{QR} * \text{QR} &= \text{QR}\\
\text{QR} * \text{QNR} &= \text{QNR}\\
\text{QNR} * \text{QNR} &= \text{QR}\\
\end{align}
$$
Or in other words, **this is the same as positive and negative math**. 
Replace QR w/ 1, and QNR = -1. 
### Legendre Symbol
"An efficient way to determine whether an integer is a QR mod an odd prime, *p*."
==This is the main equation==
$$
(a/p) \equiv a^{(p-1)/2} \text{mod}\ p
$$
These are the rules it obeys:
- $(a/p) = 1$ if *a* is a QR and $a \not\equiv 0\ \text{mod}\ p$
- $(a/p) = -1$ if *a* is a QNR $\text{mod}\ p$
- $(a/p) = 0\ \text{if}\ a \equiv 0\ \text{mod}\ p$

**Note that $(a/p)$ is just a notation.**

For the flag, we had to find the QR first, then the square root of the QR after. So we would use the regular script to find the QR:
```python
for i in ints:
    # a = F_p = {1,2,3,...,p} for our large ass p value
    for a in range(1, p):
        # if a^2 % p = ints[i]
        if pow(a, 2, p) == i:
            flag = min(flag, a)
```
But to find the flag, we have to tweak this a bit.
```python
for i in ints:
	# we're using ints[i] as our temp "a" value
	# if a^{(p-1)/2} mod p == 1
    if pow(i, (p-1)//2, p) == 1:
        a = i # we set "a" to our found QR
        break

# then this is the square root of that QR
flag = pow(a, (p+1)//4, p)
```
[Check out this webpage for a more detailed explanation](https://mathcenter.oxford.emory.edu/site/math125/legendresSymbol/)

### Modular Square Root
Takes the Legendre symbol a bit further with Tonelli-Shanks algorithm. 

> In a congruence of the form $r^2 \equiv a \text{ mod } p$, where T-S calculates $r$.

Now T-S doesn't work for composite moduli. Keep that in mind when doing these challenges. 

This is mainly used for finding elliptic curve coordinates (which you _might_ have to use for the Elliptic Curve section of this crash course). Let's worry about the details later. 

**The challenge is to find the square root $a$ modulo 2048-bit prime $p$ (giving the smaller number, via the output txt file given).**
```python
# this was the first solution after solving.
# this is better than what I did, since it goes more in depth!
from output import *

def square_root(a, p):
    #Tonelli–Shanks algorithm
    if legendre_symbol(a, p) != 1:
        return 0
    elif a == 0:
        return 0
    elif p == 2:
        return 0
    elif p % 4 == 3:
        return pow(a, (p + 1) / 4, p)

    s = p - 1
    e = 0
    while s % 2 == 0:
        s //= 2
        e += 1

    n = 2
    while legendre_symbol(n, p) != -1:
        n += 1

    x = pow(a, (s + 1) // 2, p)
    b = pow(a, s, p)
    g = pow(n, s, p)
    r = e

    while True:
        t = b
        m = 0
        for m in range(r):
            if t == 1:
                break
            t = pow(t, 2, p)

        if m == 0:
            return x

        gs = pow(g, 2 ** (r - m - 1), p)
        g = (gs * gs) % p
        x = (x * gs) % p
        b = (b * g) % p
        r = m


def legendre_symbol(a, p):
	ls = pow(a, (p - 1) // 2, p)
    return -1 if ls == p - 1 else ls

print (square_root(a, p))
```

### Chinese Remainder Theorem

