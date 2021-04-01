---
layout: post
title: The Price is Relatively Right
date: 2020-12-31 23:00:00
description: multiplicative weight update method
published: true
---

---

<i>
Note: All thereoms mentioned in this post are found in the references at the
bottom of the page.
I am intending to showcase some results in the field of algorithms.
</i>

#### Problem Setup

<img
  alt="The Price is Right"
  height="200px"
  src="https://offthewire.com/wp-content/uploads/2019/10/Onebid2018.jpg"
  style="float: right; margin-left: 10px;"
  title="The Price is Right">

[_The Price is Right_ ](https://en.wikipedia.org/wiki/The_Price_Is_Right)
is an American game show wherein contestants bid on various prizes.
Roughly speaking, in each round, the contestant whose bid is closest to the
actual cost of the prize wins the prize.
Though there is one caveat: if a contestant overvalues the prize, he will be
guaranteed not to win the prize.
To make the game more fun, [the contestants often get advice from the
audience](https://youtu.be/3U7xtCBmieQ?t=106).

Consider the following variation of the game:
  * There is only one contestant.
    Before starting the game, the contestant chooses $$n$$ audience members to
    help advise him throughout the game.
    The advice must be in a form so that it directly answers the question given
    by the host.
  * In round $$t$$, the game show host presents the prize along with a suggested
    price $$q^{(t)}$$ and width $$k^{(t)}$$; the actual price $$p^{(t)}$$ is kept secret.
    The contestant must determine whether $$|p^{(t)}-q^{(t)}|\leq k^{(t)}$$.
    In other words, the contestant needs to determine if the price of the prize
    is close to what the price the host suggested.
  * Before making his decision, the contestant can get advice from the $$n$$
    pre-selected audience members.
  * After the contestant makes the decision, the host tells him whether his
    answer was correct or mistaken.

Here is an example round:
> Host: [Shows a 2020 Toyota Prius].

> Host: "Does this car cost $22,000 &#177; 4,000?"
>
> Contestant: [Consults audience members and receives mixed yes/no answers].
>
> "Yes."
>
> Host: "You are correct! The price is $24,525."

In this example, $$q^{(t)} = 22000$$, $$k^{(t)}=4000$$, and $$p^{(t)}=24525$$.

What strategy should the contestant employ to minimize the number of rounds he
loses?

---

From here on out, I will refer to the $$n$$ audience members as _experts_ since
they are giving advice.
If the contestant loses a round, we say that this is a _mistake_.
Similarly, if an expert gives incorrect advice for a round, we say that this is
a _mistake_ (the context will be clear).
Also, _we_ are going to play as the contestant (to make the wording more clear).

---

#### Scenarios

##### A Perfect Expert
Suppose that out of the $$n$$ experts we choose, there is at least one expert
never makes a mistake (but we don't know who it is yet).

**Theorem 1.** <i>
There is a strategy such that at most $$\log_2 n$$ mistakes are made.
</i>

**Proof:** We decide using majority vote.
If an expert gives incorrect advice, we do not consider their vote in future
rounds.
When our strategy makes a mistake, that meant that a _majority_ of the remaining
experts made a mistake, thus the remaining number of experts halves.
Thus after $$\log_2 n$$ mistakes, at most
$$n/2^{\log_2 n} = 1$$ expert is left: a perfect expert.
<span style="float:right;">
  &#x25FB;
</span>


##### Arbitrary Experts
Now we think of the setting where the experts are arbitrary.

After $$t$$ rounds, let $$m_i^{(t)}$$ be the number of mistakes that expert
$$i$$ has made and $$m^{(t)}$$ be the number of mistakes we have made.
One strategy would be to ignore the experts and randomly (uniformly) make a
decision.
This strategy will yield $$\mathbb E\, m^{(t)} = t/2$$.
Another strategy would be to use a simple majority vote.
This will guarantee $$m^{(t)} \leq \frac2n \sum_i m_i^{(t)}$$.
Yet another strategy would be to pick the answer from the experts uniformly at
random.
This leads to a strategy with $$\mathbb E\, m^{(t)} = \frac1n\sum_i m_i^{(t)}$$.
Surprisingly, the following result holds:

**Theorem 2.** <i>There is a strategy such that
$$m^{(t)} \leq \frac{2\ln n}{\varepsilon} + 2(1+\varepsilon)m_i^{(t)}$$
for every $$\varepsilon \in (0, 1/2)$$, $$i\in [n]$$, and $$t\in \mathbb Z_{\geq0}$$.
</i>

In particular, this is saying that we can _approximately_ perform at
least as well as the best expert.

**Proof:** We generalize the majority vote algorithm:
  1. Each expert $$i$$ has a "reliability" weight $$w_i^{(t)}$$ for each
     round $$t$$ such that $$w_i^{(1)}=1$$.
  2. During each round, we choose the answer whose combined supporting experts
     has the largest total weight (i.e. a _weighted_ majority vote).
  3. If we learn that an expert makes a mistake in round $$t$$, we subtract
     off $$\varepsilon w_i^{(t)}$$ from their weight so that
     $$w_i^{(t+1)} \gets (1-\varepsilon)w_i^{(t)}$$.
     Otherwise, their weight goes untouched.

Let $$\Phi^{(t)} = \sum_i w_i^{(t)}$$.
We are going to bound $$\Phi^{(t+1)}$$ below and above.
  * (below) Notice that $$\Phi^{(t+1)}\geq w_i^{(t+1)}$$.
    By induction, one can observe that
    $$w_i^{(t+1)} = (1-\varepsilon)^{m_i^{(t)}}$$,
    thus $$\Phi^{(t+1)}\geq (1-\varepsilon)^{m_i^{(t)}}$$.
  * (above) Notice that if in round $$t$$ we make a mistake, that means that
    the weighted majority of the experts made a mistake.
    This means that the sum of all of the experts' weights is decreased by at
    least $$\varepsilon\Phi^{(t)}/2$$.
    Thus by induction, $$\Phi^{(t+1)}\leq (1-\varepsilon/2)^{m^{(t)}}n$$.
    We can bound $$1-\varepsilon/2$$ above by $$\exp(-\varepsilon/2)$$ using
    convexity to get $$\Phi^{(t+1)}\leq n\cdot\exp(-\varepsilon m^{(t)}/2)$$.

Now we have that $$(1-\varepsilon)^{m_i^{(t)}} \leq \Phi^{(t+1)} \leq n\cdot\exp(-\varepsilon m^{(t)}/2)$$.
Apply the natural logarithm to the left and right sides to get
$$m^{(t)}\leq 2\varepsilon^{-1}(\ln n - m_i^{(t)}\ln (1-\varepsilon))$$.
Finally, use the fact that $$-\ln(1-\varepsilon)\leq \varepsilon(1+\varepsilon)$$
for $$\varepsilon \in (0, 1/2)$$ (hint: look at Taylor series expansion) to arrive
at the conclusion.
<span style="float:right;">
  &#x25FB;
</span>

One can do better with a randomized strategy, removing the factor of $$2$$ from
the RHS.
 
**Theorem 3.** <i>There is a strategy such that
$$\mathbb E\,m^{(t)} \leq \frac{\ln n}{\varepsilon} + (1+\varepsilon)m_i^{(t)}$$
for every $$\varepsilon \in (0, 1/2)$$, $$i\in [n]$$, and $$t\in\mathbb Z_{\geq0}$$.
</i>

**Proof:**
Let $$\Phi^{(t)} = \sum_i w_i^{(t)}$$ again.
In round $$t$$, we will first ask all of the experts to give advice.
Then we will randomly use the advice of a single expert according to the
distribution where we select expert $$i$$ with probability $$w_i^{(t)}/\Phi^{(t)}$$.
We then decrease the weight of each expert $$i$$ by $$\varepsilon w_i^{(t)}$$ in the
case she made a mistake in round $$t$$.
Let $$\mu_i^{(t)} = 1$$ if expert $$i$$ makes a mistake in round $$t$$ and $$0$$
otherwise.
Let $$\mu^{(t)} = 1$$ if our algorithm makes a mistake in round $$t$$ and $$0$$
otherwise.
Then $$w^{(t+1)}=(1-\varepsilon\mu_i^{(t)})w_i^{(t)}$$.
Thus after some definition chasing and induction:

$$\Phi^{(t+1)} = (1-\varepsilon \mathbb E\, \mu^{(t)})\Phi^{(t)} = n\prod_{r=1}^t(1-\varepsilon \mathbb E\, \mu^{(t)}).$$

Now we use convexity, linearity of expectation, and a similar argument from the
proof of Theorem 2 to see that:

$$(1-\varepsilon)^{m_i^{(t)}}\leq \Phi^{(t+1)} = n\prod_{r=1}^t(1-\varepsilon \mathbb E\, \mu^{(r)})\leq n\cdot \exp(-\varepsilon \mathbb E\, m^{(t)}).$$

Likewise, the rest of the argument is similar to that of Theorem 2.
<span style="float:right;">
  &#x25FB;
</span>

---

#### A More Realistic Setting

Now, let us make the following modifications to the game:

  * In round $$t$$, the game show host presents the prize.
    The actual price $$p^{(t)}$$ of the prize is kept secret.
    The contestant must bid a price $$q^{(t)}$$ for the prize.
  * Before making his bid, the contestant gets suggested bids from the $$n$$
    pre-selected audience members.
  * After the contestant makes his bid, the host tells him the actual price
    of the prize.
  * The contestant is _penalized_ $$\gamma^{(t)}\in[0,1]$$ points in round
    $$t$$.
    This penalty depends on $$q^{(t)}$$, $$p^{(t)}$$, and possibly other variables.
    The contestant is aware of the formula for $$\gamma^{(t)}$$.

A mistake is no longer binary.
We, the contestant, impose a penalty $$\gamma_i^{(t)}$$ on expert $$i$$ when she
advises a bid of $$q_i^{(t)}$$ in round $$t$$.

**Theorem 4.** <i>There is a strategy such that
$$\mathbb E\,\gamma^{(t)} \leq \frac{\ln n}{\varepsilon} + (1+\varepsilon)\sum_{r=1}^t \gamma_i^{(r)}$$
for every $$\varepsilon \in (0, 1/2)$$, $$i\in [n]$$, and $$t\in\mathbb Z_{\geq0}$$.
</i>

For example, if we have
$$\gamma^{(t)}=1-(q^{(t)}/p^{(t)})\cdot \mathbf 1_{[0, p^{(t)}]}(q^{(t)})$$,
we could impose a penalty
$$\gamma_i^{(t)} = 1-(q_i^{(t)}/p^{(t)})\cdot \mathbf 1_{[0, p^{(t)}]}(q_i^{(t)})$$
for each expert.
Thus Theorem 3 is a special case of Theorem 4 when $$\gamma^{(t)}=\mu^{(t)}$$
and $$\gamma_i^{(t)}=\mu_i^{(t)}$$.
I'll omit the proof since it is similar to the ones above,
but we modify the previous randomized algorithm with the expert weight update rule:

$$w_i^{(t+1)}\gets (1-\varepsilon\gamma_i^{(t)})w_i^{(t)}.$$

---

#### Conclusion
It is great to see error bounds which depend on the performance of the best expert.
One should hope they select
[Ted Slauson](https://en.wikipedia.org/wiki/Perfect_Bid:_The_Contestant_Who_Knew_Too_Much)
as one of their $$n$$ experts.

The strategies presented above are known as "multiplicative weight update"
methods and have a wide range of applications, especially in
optimization/approximation settings.

---

#### References
1. [Arora, Hazan, and Kale: Multiplicative Weights Update Survey](https://www.cs.princeton.edu/~arora/pubs/MW-survey.pdf)
2. [Har-Peled: Expert Selection Notes](https://sarielhp.org/teach/19/01_fall/lecs/l19_10_29.pdf)
