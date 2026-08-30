Examples of algorithm problems:
- Fibonacci Numbers
- Greatest Common Divisors
- These straight foward algorithms are hard to complete, and take a long time

Fibonacci:
- input: take a non-negative integer
- if n <= 1:
	- return n
- else:
	- return FibRecurse(n-1)+FibRecurs(n-2)
- Takes 56k  years at 1 GHz
	- Why?
		- terms are being recalculated again and again, raising runtime
- Imitate hand computation as an alternative algorithm
	- create an array F[0, ..., n]
	- F[0] <- 0
	- F[1] <- 1
	- for i from 2 to n:
		- F[i] <- F[i - 1] + F[i - 2]
	- return F[n]
Greatest Common Devisors:
- put a fraction in simplest form
- important in Cryptography
- input: integers a, b >= 0
- output: gcd(a,b)
- solution?
	- Key Lemma
		- let a' be the remainder when a is divided by b, then:
			- gcd(a,b) = gcd(a',b) = gcd(b, a')
	- Euclidean Algorithm
		- if b = 0:
			- return a
		- a' <- the remainder when a is divided by b
	- Much faster, takes about log(ab) = log(a) + log(b) steps