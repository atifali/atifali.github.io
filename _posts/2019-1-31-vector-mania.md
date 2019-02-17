---
layout: post
title_prefix: <span>:boom::tada::confetti_ball:</span>
title: Vector mania 
title_suffix: <span>:confetti_ball::tada::boom:</span>
use_mathjax: true
---

A couple days back I had one of those moments where you intuitively have a feeling (aka dem gut feelz) about something being right or wrong in terms of making mathematical sense, and then you go out on a limb to see if you can come up with a generalized model for the situation and see if it can be formed into a framework for the future, or<!-- more --> maybe there already is a framework that supports that generalization that you have somehow managed to learn before. In the former case, you simply come up with a brand new framework of thinking (that's where mostly theories come from), while in the latter case you simply reinforce the framework that already exists, by adding in one more scenario to strengthen the case. This time was the case of the latter, as I realized pretty early down my curiosity path. But I continued on and eventually reconciled my 'framework' with my 'intuition'.

At the end, I thought that the whirlwind of ideas that I generated were quite amusing, so I am going to share some of them here. Following was the issue at hand:

I had a very simple case of a static equilibrium problem at hand with a mechanical linkage system as shown below in Figure 1.

![_config.yml]({{ site.baseurl }}/images/vector-mania1.jpg "Figure 1: ")
<figcaption>Figure 1: Simple Mechanical Linkage</figcaption>

In order to solve the equilibrium problem in this scenario, one would need to make sure that the force and moments about any point in the body are zero. Here I choose that point to be $$ 'O' $$. Now when doing the moment calculation for the force $$ 'F' $$ about point $$ 'O' $$, there are actually a couple straight-forward ways (based on our knowledge of points in space) of doing it. You could take the cross product of force vector $$ \vec{F} $$ with either $$ \vec{OA} $$ or $$ \vec{OB} $$. Now this does make sense, since the force $$ \vec{F} $$ is always along $$ \vec{AB} $$, and we are essentially taking the cross product of the force with the 'distance' to the line of action of the force ($$ \vec{M} = \vec{r} \times \vec{F} $$). I knew this was right, but I never really proved it with some vector math. So it was time to do some math and prove this general rule. I came up with the following:     

$$ \vec{M_O} = \vec{OA} \times \vec{F} = \vec{OB} \times \vec{F} $$

We know: $$ \vec{F} = \frac{\left \| F \right \|}{\left \| \vec{AB} \right \|}\vec{AB} $$

So:

$$ \vec{OA} \times \frac{\left \| F \right \|}{\left \| \vec{AB} \right \|}\vec{AB} = \vec{OB} \times \frac{\left \| F \right \|}{\left \| \vec{AB} \right \|}\vec{AB} $$

That entails:

$$ \vec{OA} \times \vec{AB} = \vec{OB} \times \vec{AB} $$

Now to prove the above, we know that point $$ 'O' $$, $$ 'A' $$ and $$ 'B' $$ form a triangle in all possible configurations. And we know that a cross product is basically the area of the parallelogram formed by the two vectors being crossed (this can be proved by basic trigonometry, maybe some other day!). This is shown in Figure 2 below.

![_config.yml]({{ site.baseurl }}/images/vector-mania2.jpg "Figure 2: ")
<figcaption><i>Figure 2: Cross Products in a Triangle</i></figcaption>

And so we have (Note here: $$ \left \| xyz \right \| = area\:of\:triangle\:xyz $$ and; $$ \left \| oxyz \right \|  = area\:of\:parallelogram\:oxyz $$):

$$ \vec{OA} \times \vec{AB} = \left \| OABA' \right \| = 2 \times \left \| OAB \right \| $$

$$ \vec{OB} \times \vec{AB} = \left \| OBAB' \right \| = 2 \times \left \| OBA \right \| $$

And since $$ OAB $$ and $$ OBA $$ are the same triangle $$ \left \| OAB \right \| = \left \| OBA \right \| $$. And hence this proves our postulate that in turn proves our general rule about calculating moments caused by a force about any point in space. Indeed this can be generalized to say that you can calculate the moment of a force about any arbitrary point in space, by taking the cross product of the vector from our reference point to any point along the force vector to infinity, and the force vector itself. 

Now I must admit for some of the readers out there, this might be too simple to even prove. And yes, there is another simpler way of proving this, by arguing that the $$ sine $$ component of $$ \vec{r} $$ is always the same regardless of what point it connects to, as long as that point lies on the line of action of the force. However, this does give a general idea of how proofs in mathematics work, and this same sort of framework can be applied in a variety of other cases.

#### TL;DR

To calculate the moment of a force about any arbitrary point in space, you simply need to take the cross product of the vector from the reference point to __any point along the line of action of force__, and the force vector itself. 