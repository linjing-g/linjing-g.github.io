# Zhuangzi's Stick


## Story Time Twist:
Hey friends, remember our last treasure hunting story?

Leo had to move in infinite steps within a limited time, but since he was walking at a steady speed, he eventually reached the treasure. Cool, right?

But… what if we change the rules a little bit, like the daily speed is decreasing?

<!--more-->

> Imagine this:
>
> the first day, he walks half of the distance, that is $1/2$. 
>
> The second day, he walks half of what's left, which is $1/4$. 
>
> The third day, he goes half of the remainder, which is $1/8$.
>
> And it keeps going, and going, and going...”
>
> So, will Leo ever reach the treasure?

Well, here's the twist: the answer is… NOPE!

<img src="/img/zeno2.png" align="right" width="100%" alt="caption">

Why? Let's dive in and find out!”

## Thoughts：
### Fun Explanation for Kids
Leo seems to be under a slowing-down magic spell! On the first day, he walks half of the journey. On the second day, only a quarter. On the third day, even less—just one-eighth… 

Each day he moves slower and slower,
until he's crawling like a tiny snail, almost not moving at all.
Even though he looks so close to the treasure, the time he needs, boom boom, stretches out to endless—so he can never really reach it!”

### Intuitive Explanation
Leo keeps moving forward, but each day he covers less and less distance.
In other words, his speed keeps slowing down,
so the total time he needs becomes longer and longer. 

But in real life, nobody walks forever!
That's why, within a finite amount of time,
he can't actually reach the destination.

### Math Explanation
- we know the infinite series: $$ \frac{1}{2} + \frac{1}{4} + \frac{1}{8} + \dots = \frac{ \frac{1}{2}}{1- \frac{1}{2}}=1 ,$$ convergent.
- For any (finite) natural number $n$, the total distance he walked over $n$ days is:
$$ \frac{1}{2}+\frac{1}{4}+\frac{1}{8}+\cdots+\frac{1}{2^n} < 1.$$

- Conclusion: destination is never reached within a finite amount of time.

## Easter Egg
And guess what? This isn't just a math puzzle — it's an ancient idea too!

Over 2,000 years ago, the Chinese philosopher Zhuangzi wrote:

> 一尺之棰， 日取其半，万世不竭。--《庄子·杂篇·天下》

That says:

> If you cut a stick in half every day, it will never be used up, even after ten thousand generations.

So still infinitely many actions happen within a finite period at a decreasing speed, then, AHA, the same story!

Cheers!

