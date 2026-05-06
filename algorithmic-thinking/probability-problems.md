# A Few Counterintuitive Probability Problems


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet readers' needs, the site now offers a [Quick-Start Curriculum](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for your support! It is also recommended that you read articles on my [website](https://labuladong.online/algo/) for a better experience.**


**-----------**


The earlier article [Random Algorithms in Games](https://labuladong.online/algo/frequency-interview/random-algorithm/) discussed Monte Carlo methods for verifying probability algorithms. Today, something lighter: a few interesting probability puzzles.

Two simple principles for computing probabilities:

Principle 1: there must be a frame of reference, called the "sample space" — all possible outcomes. P(A) = (number of sample points in A) / (total sample points).

Principle 2: probability is a continuous whole; you can't arbitrarily slice continuous probability — that's the meaning of conditional probability.

These were taught in high school, yet we still mess them up — and the mistakes follow a familiar pattern: we ignore Principle 2, miscount the sample space, and then misapply Principle 1.

Below: the boy/girl problem, the birthday paradox, and the Monty Hall problem. The Monty Hall problem is the most famous, so we'll spend more time on it.


### 1. The Boy/Girl Problem

A family has two children. You're told that one of them is a boy. What is the probability that the other is also a boy?

Many people, myself included, instinctively answer 1/2 because the other child is either a boy or a girl with equal probability. But the answer is 1/3.

Why is the intuition wrong? Because the sample space is wrong, so Principle 1 is misapplied. With two children, the sample space has 4 outcomes: older-bro/younger-sis, older-bro/younger-bro, older-sis/younger-sis, older-sis/younger-bro. Knowing one of them is a boy excludes "older-sis/younger-sis", leaving 3 outcomes. Both being boys is just "older-bro/younger-bro" — 1 out of 3.

Why is the sample space miscalculated? We ignored conditional probability and conflated:

This family has only one child; what's the probability the child is a boy?

This family has two children, one of whom is a boy; what's the probability the other is a boy?

By Principle 2, probability is continuous; the two questions are different. The second is conditional: given one is a boy, what's the chance the other is too. The conditional-probability formula handles it; we'll skip the calculation.

The relationship between the two principles should be clearer now. The most misleading factor is overlooking conditional probability. The simplest defense: enumerate all outcomes.

I've seen one funny objection: what if the children are twins, with no age difference? It almost sounds reasonable! But age was just a way to express the children's independence — even same-gender twins still represent two outcomes. So no twin loopholes.


### 2. The Birthday Paradox

The birthday paradox: how many people are needed in a room for the probability that at least two share a birthday to reach 50%?

Answer: 23 people. So with 23 people in a room, there's already a 50% chance two share a birthday. It seems unbelievable — hence the name. Intuition says you'd need 183 (half of 365). Two intuitive errors:

**Error 1: misinterpreting "exists".**

You might think: with 23 people the probability hits 50%, so:

If 22 people are in the room and I walk in, there's a 50% chance someone shares my birthday. But that can't be right!

Yes — your thinking is self-centered. The puzzle's probability is about the group as a whole. "Exists" refers to any pair among the 23 — combinatorics — and likely doesn't involve you specifically.

If you really want to know the probability that someone shares your birthday:

1 - P(22 people all have a birthday different from mine) = 1 - (364/365)^22 ≈ 0.06

Now it sounds more reasonable. The birthday paradox is about the group, not any one person, and the combinations vastly inflate the probability.

**Error 2: thinking probability is linear.**

You might think: with 23 people the probability is 50%, so 46 people would be 100%?

No. Just like a 50%-win game — playing twice doesn't give 100%; it gives 75%:

`P(win) = P(win on first try) + P(miss first, win second) = 1/2 + 1/2 × 1/2 = 75%`

Same here — probabilities don't simply add; we have a continuous process. So the conclusion isn't unreasonable.

Why is 23 enough for >50%? Compute the probability that 23 people all have unique birthdays. With 1 person it's `365/365`; with 2, `365/365 × 364/365`; etc. With 23:

![](https://labuladong.online/algo/images/probability/p.png)

That's about 0.493, so the probability of a shared birthday is 0.507 — close to 50%. By 70 people, the probability rises to 99.9%, basically 100%. So statistically, it's no surprise to find shared birthdays in a group of a few dozen.


### 3. The Monty Hall Problem

A classic: a contestant faces three doors. Behind two are goats; behind one is a sports car. The contestant picks a door and gets what's behind it (the car is more valuable, of course). The host helps: after the choice, instead of opening it immediately, the host opens one of the remaining two doors (the host knows what's behind each) and reveals a goat. Then the host gives the contestant a chance to switch. Should the contestant switch?

In case it's not clear: you're the contestant, doors 1, 2, 3. You picked door 1; the host opens door 3 to reveal a goat. Stick with door 1, or switch to door 2?

![](https://labuladong.online/algo/images/probability/sanmen.png)

Answer: switch. Switching wins the car with probability 2/3; staying with 1/3. Counterintuitive again — surely two doors mean 1/2 either way?

As before, the simplest method is to enumerate:

![](https://labuladong.online/algo/images/probability/tree.png)

Easy to see: switching = 2/3, staying = 1/3.

A simpler explanation: the host's action concentrates probability. Initially, your door has 1/3 chance of being the car; the other two together have 2/3. Once the host reveals a goat behind one of the other two, that 2/3 collapses entirely onto the remaining unopened door. So would you stick with the 1/3 door or switch to the now-2/3 door?

To make it even more vivid: imagine 100 doors. You pick one. The host reveals 98 goats among the other 99. Switching is obviously better — your original door has 1% odds; the other has 99%. (Equivalently, staying = picking 1 door; switching = picking the other 99 doors.)

Now consider this: while you're deciding to switch, Mr. X bursts in and demands to choose for you. He knows nothing of what just happened — he only sees two closed doors, one with the car. What's his probability of getting the car?

Of course 1/2 — and that's why people get the Monty Hall problem wrong. Like the birthday paradox, people self-center using Mr. X's perspective.

Analogy: two boxes — Box 1 has 4 black balls and 2 red, Box 2 has 2 black and 4 red. You pick a box at random and a ball at random. P(red) = ?

For Mr. X (no info): P = 1/2 × 2/6 + 1/2 × 4/6 = 1/2.

For you (in the know): you'd always pick Box 2: P = 0 × 2/6 + 1 × 4/6 = 2/3.


**＿＿＿＿＿＿＿＿＿＿＿＿＿**


![](https://labuladong.online/algo/images/souyisou2.png)
