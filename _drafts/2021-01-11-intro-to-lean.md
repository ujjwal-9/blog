---
toc: true
layout: post
use_math: true
description: Proving theorems the lean way.
categories: [maths, lang]
title:  Introduction to Lean
image: images/intro-lean/cover.png
---

Lean is an open source proof assistant developed by Microsoft Research. 

[Github](https://github.com/leanprover/lean)



# Introduction


# Example

Here we prove that **prime numbers are more than any assigned multitude of prime numbers**.

> This proposition states that there are more than any finite number of prime numbers, that is to say, there are infinitely many primes.

## Outline of proof

[Source](https://mathcs.clarku.edu/~djoyce/java/elements/bookIX/propIX20.html)

- Suppose that there are n primes, a1, a2, ..., an. Euclid, as usual, takes an specific small number, n = 3, of primes to illustrate the general case. Let m be the least common multiple of all of them. 
> The least common multiple was also considered in proposition [IX.14](https://mathcs.clarku.edu/~djoyce/java/elements/bookIX/propIX14.html). It wasn’t noted in the proof of that proposition that the least common multiple of primes is their product, and it isn't noted in this proof, either.

- Consider the number m + 1. If it’s prime, then there are at least n + 1 primes.

- So suppose m + 1 is not prime. Then according to [VII.31](https://mathcs.clarku.edu/~djoyce/java/elements/bookVII/propVII31.html), some prime g divides it. But g cannot be any of the primes a1, a2, ..., an, since they all divide m and do not divide m + 1. Therefore, there are at least n + 1 primes. Q.E.D.

- This proposition is not used in the rest of the Elements.


## Lean Proof

```lean
-- Definitions about natural numbers and primes
import data.nat.prime

-- Library on linear arithmatic
import tactic.linarith

-- Define namespace, which is natural numbers in this case
open nat 


-- Define theorem or goal to prove
theorem infinitude_of_primes: ∀ N, ∃ p >= N, prime p :=
-- between begin-end block we write tactics
begin
  -- define N to be a natural number as a part of our local hypothesis
  intro N,

  -- Continue with proof as mentioned in link provided in header
  -- let M to be N! + 1
  let M := factorial N + 1,
  
  -- let p be smallest prime factor of M which is not 1
  let p := min_fac M,


  -- define supporting hypothesis pp, p is prime
  have pp : prime p := 
  -- begin proof for supporting p being prime
  begin
    -- minimum factor of a number is prime, but what about if M = 1
    refine min_fac_prime _,
    -- so here we prove M != 1 or M > 1
    have : factorial N > 0 := factorial_pos N,
    -- this just automatically takes care of linear arithmatic required for proof
    linarith,
  end,

  -- before this we had existenial statement but now we have condition in p
  use p,

  -- split our goal in  2 subgoals
  split,

  -- proof by contradiction so it should output False
  {by_contradiction,
   
   /- hypothesis h1, p divides N! + 1 proved by  
   min_fac_dvd : ∀ (n : ℕ), n.min_fac ∣ n
   -/
   have h₁ : p ∣ factorial N + 1 := min_fac_dvd M, 
   
   -- hypothesis h2, p divides N!
   have h₂ : p ∣  factorial N := 
   begin
     refine pp.dvd_factorial.mpr _,
     -- proved p <= N, using hypothsis h
     exact le_of_not_ge h,
   end,
   /-
   proved using dvd_add_right with support from local hypothesis h₂ and h₁
   -/
   have h : p ∣ 1 := (nat.dvd_add_right h₂).mp h₁,
   -- prime not dividing one using local hypothesis pp and h
   exact prime.not_dvd_one pp h, },
   -- second part of proof is just our hypothesis pp that we already proved
  {exact pp, },
end
```