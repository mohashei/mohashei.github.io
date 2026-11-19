---
layout: post
comments: false
title:  "Renormalization of Functions"
excerpt: "Part 2: the MSR action, and why zooming out on a pinned rubber band means following a whole function instead of a few numbers."
date:   2026-11-15 00:00:00
mathjax: true
---

<style>
.post-content table { border-collapse: collapse; margin: 20px 0; }
.post-content th, .post-content td { padding: 6px 14px 6px 0; border-bottom: 1px solid #e8e8e8; text-align: left; vertical-align: top; }
.post-content th { font-weight: 400; }
.post-content .imgcap img { border: none; }
</style>

### Renormalization

Before we can tackle the full temperature case, we have to look at how to even analyze systems like this at zero temperature. I think there are many fascinating approaches to talk about, including simulation, replica symmetry, and cellular automata [1, 2, 3]. Here I want to talk about doing this using renormalization flow. I am going to assume folks are familiar with regular renormalization in this post, see especially reviews by Wilson [4] and Kadanoff [5]. But to cover it slightly, the renormalization flow is a hallmark of physics, one that is particularly important when talking about how microscopic laws can become the physics we see around us. Incidentally it is also useful when dealing with infinite values in QFT, but we won't concern ourselves with that here.

At a high level, renormalization consists of a three step process of progressively zooming out. In step 1, we are going to coarse grain the system that we're studying. The canonical example here is a Ising spin model, in which we coarse grain the spins into blocks of spins (Figure 1) [6]. In step 2, we rescale. Since we did a zoom out, our new system has a larger scale. We will then relabel the scales, such as the position between the centers of the block spins, as the new unit scale. In step 3, we renormalize, which means that we have to find out how the block spins and interactions between them changed, and try to make them look like our original starting point. In the process of doing this, clearly we will have to define new coupling constants, new spins, etc. These will then be the new system, and we can repeat the process.

<div class="imgcap">
<img src="/assets/renormalization-of-functions/fig01_block_spins.png"
     alt="A grid of up and down spins grouped into three-by-three blocks, each block replaced by one spin, and the result shrunk back to the original spacing"
     width="700"
     height="auto">
<div class="thecap">
  Figure 1. One round of zooming out for Ising spins (blue is up, grey is down). Group the spins into 3 × 3 blocks, replace each block by one spin pointing the way most of the block does, and shrink the picture so the spins are as far apart as they were before.
</div>
</div>

What we want to extract is the flow of our coupling constants, spins, magnetic fields, etc, as we keep zooming out. This flow is important, because it delineates how the system behaves at large, every-day scales. If some constant goes to $$0$$, it probably isn't important at large scales, although as we will see, it sometimes is. If some constant blows up, it probably is important. And some constants don't change quickly as we scale up. In general, we call variables that shrink irrelevant, those that grow relevant, and those that stay roughly the same or grow slowly are known as marginal.

The flow also has other important properties. Maybe the most famous is that the flow can have fixed points. A fixed point of the flow, by definition, is a place where scale changes don't change after a renormalization step. Special fixed points that are unstable in at least one direction are important because that means the flows away from that point push the system away from the fixed point. Therefore, the collective behavior of that fixed point is a transition between two phases (Figure 2). For physicists, these scale invariant points are examples of continuous phase transitions. Unlike boiling, evaporation, or freezing, these phase transitions have fluctuations at all length scales (Figure 3). Unfortunately I wished I could spend more time talking about this, but really it requires a full textbook to understand fully.

<div class="imgcap">
<img src="/assets/renormalization-of-functions/fig02_flow.png"
     alt="Flow lines near a fixed point: they come in along one direction and leave along the other, towards one of two phases"
     width="620"
     height="auto">
<div class="thecap">
  Figure 2. The flow near a fixed point that is unstable in one direction. A system that starts exactly on the orange line ends up at the fixed point. Start a little to either side and the flow carries you off to one of two phases.
</div>
</div>

<div class="imgcap">
<img src="/assets/renormalization-of-functions/fig03_ising.png"
     alt="Three snapshots of an Ising magnet: nearly uniform below the critical temperature, patches of all sizes at the critical temperature, fine-grained noise above it"
     width="700"
     height="auto">
<div class="thecap">
  Figure 3. Snapshots of a small simulated Ising magnet below, at and above its critical temperature \(T_c\). Below, one colour has won. Above, the spins are a fine-grained mess. Exactly at \(T_c\) there are patches of every size.
</div>
</div>

### MSR Action

Here is the model from [Part 1](/2026/11/12/avalanches-and-depinning/) again, with the temperature switched off:

$$
\eta\,\partial_tu(x,t)=c\nabla^2u+F(x,u)+f .
\tag{1}
$$

What we want out of (1) are averages over the rough surface, things like the roughness or the velocity. The trouble is that $$u$$ is whatever solves (1), and the solution depends on the pinning force $$F$$ in a horribly complicated way. We can't solve (1) for one rough surface, never mind average the solution over all of them.

The trick is to not solve the equation at all. It goes back to Martin, Siggia and Rose [7], and the version I'll use is due to Janssen [8] and De Dominicis [9]. We sum over every possible history $$u(x,t)$$ of the rubber band, and we put in a delta function that throws away all of them except the one that solves (1).

#### Warm-up with one variable

Say $$u^*$$ is the solution of some equation $$g(u)=0$$, and we want to know some function of it, $$O(u^*)$$. We can write

$$
O(u^*)=\int du\;\delta\big(g(u)\big)\,\lvert g'(u)\rvert\;O(u).
\tag{2}
$$

The factor $$\lvert g'\rvert$$ is there because $$\delta(g(u))$$ is $$\delta(u-u^*)$$ divided by $$\lvert g'(u^*)\rvert$$. Then we write the delta function as its Fourier transform:

$$
\delta(g)=\int\frac{d\hat u}{2\pi}\;e^{\,i\hat u\,g}.
\tag{3}
$$

So we got rid of the equation, and the price is a second variable $$\hat u$$.

#### One delta function for every point and every time

Equation (1) is really one equation for each point $$x$$ and each time $$t$$. (Chop space and time into small steps if that makes you more comfortable.) So we need one delta function, and one $$\hat u$$, for each $$x$$ and $$t$$. Writing $$\int_{xt}$$ for $$\int d^dx\,dt$$:

$$
O[u_{\rm solution}]=\int\mathcal{D}u\,\mathcal{D}\hat u\;\mathcal{J}\;O[u]\;\exp\Big\{i\int_{xt}\hat u(x,t)\,\big[\eta\,\partial_tu-c\nabla^2u-F(x,u)-f\big]\Big\}.
\tag{4}
$$

Here $$\mathcal{D}u$$ means an integral over the value of $$u$$ at every point and time, and $$\mathcal{J}$$ is the generalization of $$\lvert g'\rvert$$ in (2): the determinant of the equation $$E = 0$$ we are trying to model with respect to $$u$$. Let's look at that matrix:

$$
\frac{\delta E(x, t)}{\delta u(x', t')} = [\eta \partial_t - c\nabla^2 - F'(x, u)]\delta(x-x')\delta(t-t')
$$

Here $$f$$ drops out as a constant and $$\delta(x-x')\delta(t-t')$$ comes from taking the functional derivative of $$u(x, t)$$ at $$x', t'$$. Clearly this is super complicated to evaluate in general, but we take the Ito convention. This means that $$u$$ *only* depends on times before. So then it turns out that:

$$
E_t = \frac{\eta}{dt}(u(x, t+dt)-u(x, t)) - c\nabla^2u(x, t) - F(x, u(x, t)) - f
$$

Now consider the sequence of all $$E_t$$, starting at $$0$$ and ending at $$T$$. This sequence is going to start at $$E_0, ..., E_{T-dt}$$, where the equation ends before the final output. Practically, if we're solving for $$T$$ time, we don't need an equation for $$T$$ because the $$T - dt$$ step gives us the final output. So $$\partial E_t/\partial u(x, t + dt)$$ is the correct quantity to look at, and this is constant, it is just $$\eta/dt$$! It is badly singular, but we don't need to worry about that, and we can safely ignore the Jacobian.

There is one more thing that comes for free. Put $$O=1$$ in (4). The answer is $$1$$, whatever $$F$$ is, because (1) has exactly one solution once you say where the band starts. This sounds trivial, but it is the reason this method works so well for rough surfaces. In equilibrium problems the normalization (the partition function) depends on the rough surface, you have to average its logarithm, and that is what forces people into the replica trick I mentioned above. Here there is nothing to normalize, so we can average (4) over the rough surface directly.

#### Averaging over the rough surface

The pinning force $$F$$ appears in (4) in only one place, the factor $$\exp\{-i\int_{xt}\hat u\,F(x,u)\}$$. And $$F$$ is Gaussian with zero mean. For any Gaussian variable $$\phi$$ with zero mean, $$\overline{e^{-i\phi}}=e^{-\overline{\phi^2}/2}$$. Here $$\phi=\int_{xt}\hat u(x,t)\,F(x,u(x,t))$$, and with the correlator of $$F$$ from Part 1,

$$
\overline{\phi^2}=\int_x\int_{t,t'}\hat u(x,t)\,\hat u(x,t')\;\Delta\big(u(x,t)-u(x,t')\big).
\tag{5}
$$

There is one $$x$$, because the rough surface is uncorrelated between different points of the band. But there are two times.

Last, a change of notation to get rid of the $$i$$'s: rename $$i\hat u\to-\hat u$$. (This means $$\hat u$$ is integrated up and down the imaginary axis. Nothing else changes.) The result is

$$
\overline{O[u]}=\int\mathcal{D}u\,\mathcal{D}\hat u\;O[u]\;e^{-S},
$$

$$
S=\int_{xt}\hat u\,\big(\eta\,\partial_t-c\nabla^2\big)u\;-\;\frac12\int_x\int_{t,t'}\hat u(x,t)\,\hat u(x,t')\;\Delta\big(u(x,t)-u(x,t')\big)\;-\;f\int_{xt}\hat u .
\tag{6}
$$

This is the MSR action. The rough surface is gone. All that is left of it is the function $$\Delta$$.

#### What the action is telling us

Three things are worth pointing out before we calculate anything.

**$$\hat u$$ is how you kick the system.** Add a small extra force $$h(x,t)$$ to (1). In (6) it shows up next to $$f$$, as $$-\int_{xt}\hat u\,h$$. So asking how the band responds to a kick is the same as asking how $$u$$ is correlated with $$\hat u$$:

$$
\frac{\delta\,\overline{u(x,t)}}{\delta h(x',t')}=\overline{u(x,t)\,\hat u(x',t')}\equiv R(x-x',t-t').
\tag{7}
$$

For this reason $$\hat u$$ is called the response field. Without the rough surface we can read $$R$$ straight off (1), which is then a linear equation. For a wave with wavevector $$q$$,

$$
R_q(t)=\frac1\eta\,e^{-cq^2t/\eta}\quad(t>0),\qquad\int_0^\infty R_q(t)\,dt=\frac{1}{cq^2}.
\tag{8}
$$

A kick makes a bulge, and the bulge relaxes. The total displacement you get per unit of force is $$1/cq^2$$. This is the estimate from Part 1 that bending a piece of size $$R$$ by $$\delta u$$ takes a force $$c\,\delta u/R^2$$, turned upside down.

**The rough surface has a memory.** The second term in (6) has two times in it. The rough surface doesn't change with time, so if a piece of the band is at the same place at time $$t$$ and at time $$t'$$, it feels the same force both times. The action remembers everywhere the band has been. Thermal noise has no memory. If we had kept the noise $$\xi$$ and averaged over it the same way, we would have gotten a term $$-\eta T\int_{xt}\hat u^2$$, with a single time. The two terms look alike and behave very differently, and a lot of the difficulty of the finite-temperature problem comes from that.

**The rough surface enters as a whole function.** Not as a number or two. This is what the title of the post is about, and it is the next thing to look at.

### Zooming out at zero temperature

Now we do to (6) what the first section did to the Ising model.

#### Rescaling: why we need a function

I'll start with step 2, the rescaling, because it's easy. Zoom out by a factor $$b$$, so that lengths shrink: $$x=b\,x_{\rm new}$$. To keep the picture looking the same we also have to shrink the displacements and the times, $$u=b^{\zeta}u_{\rm new}$$, which is the roughness exponent $$\zeta$$ of Part 1. Time also rescales with $$t=b^{z}t_{\rm new}$$, in other words big pieces move more slowly than small ones, and $$z$$ is called the dynamic exponent. Note both $$z$$ and $$\zeta$$ are positive. We also have to rescale $$\hat u$$, and I'll choose to do it so that the elastic term in (6) keeps its coefficient $$c$$. (We are allowed to insist on this. As I mentioned in Part 1, the elastic term can't be changed by the disorder [10]. This is a Ward identity.) That means $$\hat u=b^{\,2-\zeta-d-z}\,\hat u_{\rm new}$$. Now count the powers of $$b$$ in each of the other terms of (6):

$$
\eta\to b^{\,2-z}\,\eta,\qquad f\to b^{\,2-\zeta}f,\qquad \Delta(u)\to b^{\,\epsilon-2\zeta}\,\Delta\big(b^{\zeta}u\big),\qquad\epsilon=4-d.
\tag{9}
$$

The middle one says that a small force grows as we zoom out. (Strictly this is the force measured from the threshold $$f_c$$. The threshold itself will show up below.) It becomes of order one when $$b\sim(f-f_c)^{-1/(2-\zeta)}$$, and that is the relation $$\nu=1/(2-\zeta)$$ from Part 1.

The last one is the important one. Before the rough surface has done anything, the band is flat, $$\zeta=0$$, and (9) says $$\Delta\to b^{\epsilon}\Delta$$. Below four dimensions the disorder grows as we zoom out. It's the same 4 as in Larkin's argument in Part 1.

But it is the whole function that is marginal (.e.g of order $$\epsilon$$). In the usual RG story, the Ising model for example, counting powers tells you that all but one or two of the couplings are irrelevant, and you only have to follow those one or two numbers. Here, write $$\Delta(u)=\Delta_0+\Delta_1u^2+\Delta_2u^4+\dots$$ ($$\Delta$$ is even) and every one of the $$\Delta_n$$ grows as $$b^{\epsilon}$$. There are infinitely many relevant couplings, and no excuse for keeping some and dropping the others. We have to follow the entire function as we zoom out. This is called the functional renormalization group, although not the one that was invented by Polchinsky. It was first done for a relaxed interface by Daniel Fisher [11], and for an interface that is being dragged by Nattermann, Stepanow, Tang and Leschhorn [12] and by Narayan and Fisher [10].

#### One step of coarse-graining

Now the hard step. We want to integrate out all the microscopic fluctuations from the action. This means integrating out the short wavelengths (fast part) from the long wavelengths (slow part).

In the language of the action, we split $$u$$ into a slow part and a fast part and do the integral over the fast part, expanding in powers of $$\Delta$$. We should do this calculation using equation (1), but we should also be mindful here of what we are expanding from. It is, in fact, *two* separate quantities. We are expanding over the long wavelength solution in $$\epsilon = 4 - d$$ dimensions. Why are we doing this? Well, above 4 dimensions, we know that the long wavelengths dominate completely, the elastic force overwhelms the disorder. So $$\epsilon$$ is our small quantity. We are also expanding around the long wavelength solution. What solution, you might ask? Well, we are specifically expanding around the $$v \rightarrow 0^+$$ solution, in other words, when the interface is just barely moving above $$f_c$$. This is because at finite velocity, we can't integrate short wavelengths while ignoring the motion of the longer wavelengths. On the other hand, below the threshold, there are a huge number of metastable states, and then the question of which state we expand around again becomes a problem.

Now we denote $$u$$ the slow part, and $$\delta u(x)$$ the fast part. The fast part is made of waves in a thin shell of wavevectors just below the largest one we still keep, which I'll call $$\Lambda$$. So the shell is $$\Lambda/b<q<\Lambda$$.

**The response function.** An important point of almost all perturbative field theory calculations is that we are dealing with Gaussian integrals in some form or another. Here the Gaussian term is the quadratic part of the action

$$
R_q(t)=\frac1\eta\,e^{-cq^2t/\eta}\quad(t>0)
$$

The response from the the fast part

$$
\delta u(x)=\int_yG(x-y)\,F(y,u),\qquad G(x)=\int_{\rm shell}\frac{d^dq}{(2\pi)^d}\,\frac{e^{iqx}}{cq^2}.
\tag{10}
$$

(In the action, $$G$$ is the response function (7), a pairing of one $$\hat u$$ with one $$u$$.) I evaluated $$F$$ at $$u$$ and not at $$u+\delta u$$, which assumes the wiggles are small. How small? Average the square of (10):

$$
\overline{\delta u^2}=\Delta(0)\,I,\qquad I\equiv\int_yG(y)^2=\int_{\rm shell}\frac{d^dq}{(2\pi)^d}\,\frac{1}{(cq^2)^2}.
\tag{11}
$$

This is Larkin's estimate, equation (3) of Part 1, for a piece of size $$1/\Lambda$$.

**The slow part now feels a slightly different force.** The point at $$x$$ is really at $$u+\delta u(x)$$, so the pinning force on it is

$$
F_{\rm new}(x,u)=F(x,u)+F'(x,u)\,\delta u(x)+\tfrac12F''(x,u)\,\delta u(x)^2+\dots
\tag{12}
$$

where the primes are derivatives with respect to $$u$$. Everything on the right is built out of $$F$$. So $$F_{\rm new}$$ is another random force, and step 3 of renormalization, finding the new couplings, means finding its correlator.

**The new correlator.** Compare the slow part at two positions, $$u$$ and $$u'$$. (If you like, these are the positions of the same patch at two different times, which is what the two times in (6) were for.) Multiply (12) at $$(x,u)$$ by (12) at $$(x',u')$$, average, and keep the terms with four $$F$$'s. Because $$F$$ is Gaussian, the average of four $$F$$'s is a sum of products of pairs, and each pair is a $$\Delta$$ or a derivative of one. Three pieces survive. Writing $$w=u-u'$$:

- from $$F'\delta u\times F'\delta u$$, pairing the two $$F'$$ with each other and the two wiggles with each other: $$-\Delta''(w)\,\Delta(w)\,I$$;
- from the same product, pairing each $$F'$$ with the $$F$$ inside the other one's wiggle: $$-\Delta'(w)^2\,I$$;
- from $$F\times\tfrac12F''\delta u^2$$, which appears twice: $$+\Delta''(w)\,\Delta(0)\,I$$.

(The minus signs come from $$\partial_u\partial_{u'}\Delta(u-u')=-\Delta''$$.) The other ways of pairing give nothing. Either they contain $$\Delta'(0)$$, which is zero for a smooth $$\Delta$$ (remember this, it is about to matter), or they average to zero over the patch because the wiggles have no long-wavelength part. Adding up,

$$
\Delta_{\rm new}(w)=\Delta(w)-I\,\Big[\Delta'(w)^2+\Delta''(w)\big(\Delta(w)-\Delta(0)\big)\Big]
=\Delta(w)-\frac{I}{2}\,\frac{d^2}{dw^2}\big[\Delta(w)-\Delta(0)\big]^2 .
\tag{13}
$$

#### The flow equation

Make the shell thin, $$b=e^{\delta l}$$ with $$\delta l$$ small. Then $$I$$ is $$\delta l$$ times a constant ($$\Lambda^{-\epsilon}/c^2$$ times a number), and I'll measure $$\Delta$$ in units that make that constant one. Putting the coarse-graining (13) together with the rescaling (9):

$$
\partial_l\Delta(u)=(\epsilon-2\zeta)\,\Delta(u)+\zeta\,u\,\Delta'(u)-\frac12\,\frac{d^2}{du^2}\big[\Delta(u)-\Delta(0)\big]^2 .
\tag{14}
$$

Here $$l$$ is the logarithm of how far we have zoomed out. The first two terms are the rescaling and the last is the coarse-graining. It is an equation for how a *function* changes with scale.

How far can we trust it? It is the first term of an expansion in the strength of the disorder. We'll see below that $$\Delta$$ settles down at a size of order $$\epsilon$$, so strictly the equation is good when $$\epsilon$$ is small, which means just below four dimensions. We are going to plug in $$\epsilon=3$$ anyway and see how wrong it is.

#### The cusp

Start with a smooth $$\Delta$$ and watch the curvature at its top, $$\kappa\equiv-\Delta''(0)$$, which is positive. Take two derivatives of (14), set $$u=0$$, and use $$\Delta'(0)=0$$:

$$
\partial_l\kappa=\epsilon\,\kappa+3\kappa^2 .
\tag{15}
$$

The first term by itself would be plain exponential growth. The second makes $$\kappa$$ blow up at a finite zoom:

$$
\kappa(l)=\frac{\kappa_0\,e^{\epsilon l}}{1-\frac{3\kappa_0}{\epsilon}\,\big(e^{\epsilon l}-1\big)}\;\to\;\infty\qquad\text{when}\qquad e^{\epsilon l_c}=1+\frac{\epsilon}{3\kappa_0}.
\tag{16}
$$

Put the units back. $$\kappa_0$$ is about $$\Delta(0)/r_f^2$$ times the constant $$\Lambda^{-\epsilon}/c^2$$ from above, where $$r_f$$ is the size of one bump. For weak disorder the length $$e^{l_c}/\Lambda$$ at which the blow-up happens is then, up to a number,

$$
\Big(\frac{c^2r_f^2}{\Delta(0)}\Big)^{\frac{1}{4-d}} .
$$

This is the Larkin length $$R_c$$, equation (4) of Part 1. We didn't put it in. The flow found it.

What happens to a function when the curvature at its top becomes infinite? It gets a kink (Figure 4):

$$
\Delta(u)=\Delta(0)-\chi\,\lvert u\rvert+\tfrac12\,\Delta''(0^+)\,u^2+\dots
\tag{17}
$$

Beyond the Larkin length $$\Delta$$ has a cusp at $$u=0$$, with some slope $$\chi$$. Equation (14) carries on working, as long as wherever it says $$u=0$$ we read it as $$u$$ slightly bigger than zero, $$u=0^+$$ [15].

<div class="imgcap">
<img src="/assets/renormalization-of-functions/fig04_cusp.png"
     alt="Two curves with the same height and area: a smooth bell shape, and a curve with a sharp cusp at the origin"
     width="600"
     height="auto">
<div class="thecap">
  Figure 4. The correlator \(\Delta(u)\) of the pinning force. Blue: the kind of smooth function we start with. Orange: what it has turned into after zooming out past the Larkin length, with a cusp at \(u=0\). The orange curve is the fixed point (22) below, and the two curves have the same area.
</div>
</div>

There was no way to avoid this. If $$\Delta$$ stayed smooth, zooming out would give Larkin's roughness, equation (3) of Part 1, at every scale, and we know that is wrong beyond $$R_c$$ [11]. The cusp is how the theory gets out of it.

#### What the cusp means

A kink in a correlation function sounds like a technicality. It is actually most of the physics of Part 1 in one symbol.

From the definition of $$\Delta$$, the difference $$\Delta(0)-\Delta(u)$$ is half the mean square of how much the force on a piece changes when the piece moves by $$u$$. If the force is a smooth function of position, it changes in proportion to $$u$$, and $$\Delta(0)-\Delta(u)$$ goes like $$u^2$$. That is a rounded top. A cusp says the mean square change goes like $$\lvert u\rvert$$. No smooth force does that. A force that *jumps* does. Suppose that as the piece moves forward, the force on it every so often drops suddenly by some amount. Over a short distance $$u$$, the chance of having passed one of these drops is proportional to $$\lvert u\rvert$$, and if you passed one, the force changed by a finite amount. So the mean square change is proportional to $$\lvert u\rvert$$.

So the cusp is telling us that beyond the Larkin length, the force on a piece of the band, as a function of where the piece is, has jumps in it. Figure 5 shows how a perfectly smooth force can turn into one with jumps. It is the smallest version of the problem I can think of: one point of the band, pulled by a spring (which stands in for the rest of the band) across a smooth random force. Move the far end of the spring steadily. The point sits in a resting place while the spring stretches, until the resting place disappears, and then it jumps ahead to the next one and the spring relaxes. Those jumps are avalanches.

<div class="imgcap">
<img src="/assets/renormalization-of-functions/fig05_sawtooth.png"
     alt="Top: a smooth, randomly wiggling force. Bottom: a sawtooth, ramping up steadily and dropping suddenly at irregular intervals"
     width="700"
     height="auto">
<div class="thecap">
  Figure 5. One point of the band, pulled by a spring across a smooth random pinning force (top). Bottom: the force you need on the far end of the spring as you move it steadily along. It builds up while the point is stuck and drops when the point jumps ahead.
</div>
</div>

The cusp also gives us the threshold force. Go back to (12) and take its average, the mean pinning force on the slow part. The first term averages to zero and the second gives $$\Delta'(0)$$ times a constant. With a smooth $$\Delta$$ that is zero: the bumps ahead push back exactly as much as the bumps behind push forward. A smooth $$\Delta$$ can't pin anything. With a cusp, $$\Delta'(0)$$ isn't defined. It is $$-\chi$$ just to the right of zero and $$+\chi$$ just to the left, and we have to ask which one we mean.

The answer comes from remembering that the wiggles respond a little late, by (8). The wiggle at $$x$$ now was set by the force a time $$\tau$$ ago, and if the band is moving forward at speed $$v$$ it was then a distance $$v\tau$$ behind where it is now. So the mean force is

$$
\overline{F_{\rm new}}=\int_0^\infty d\tau\;R_{\rm shell}(\tau)\;\Delta'(v\tau),\qquad R_{\rm shell}(\tau)=\int_{\rm shell}\frac{d^dq}{(2\pi)^d}\,R_q(\tau).
\tag{18}
$$

The argument of $$\Delta'$$ is positive. For a slow band $$v\tau$$ is small, so $$\Delta'(v\tau)\approx-\chi+\Delta''(0^+)\,v\tau$$, and doing the two time integrals with (8) gives

$$
\overline{F_{\rm new}}=-\chi\int_{\rm shell}\frac{d^dq}{(2\pi)^d}\,\frac{1}{cq^2}\;+\;\Delta''(0^+)\,I\;\eta v .
\tag{19}
$$

The first term is a force that pushes backwards however slowly the band moves. Every shell beyond the Larkin length contributes one, and their sum is the threshold force $$f_c$$. (The band only ever moves forward when it is pushed forward [13], which is why $$0^+$$ is the right choice throughout.) The second term is proportional to the velocity, so it is a correction to the friction:

$$
\eta\to\eta\,\big[1-\Delta''(0^+)\,I\big].
\tag{20}
$$

So having many places to rest, moving in avalanches, and having a threshold force are all the same fact, and the fact is the cusp.

#### The fixed point and the exponents

Now look for a $$\Delta$$ that doesn't change as we zoom out, $$\partial_l\Delta=0$$ in (14).

First the roughness. Integrate (14) over $$u$$ from $$0$$ to $$\infty$$. The last term is a total derivative, and it vanishes at both ends (at $$u=0$$ because $$\Delta-\Delta(0)$$ is zero there, at infinity because $$\Delta$$ dies off). The first two terms, after integrating by parts, give

$$
\partial_l\int_0^\infty\Delta\,du=(\epsilon-3\zeta)\int_0^\infty\Delta\,du .
$$

Coarse-graining moves $$\Delta$$ around, but it can't change its area. So the area stays put only if

$$
\zeta=\frac{\epsilon}{3}.
\tag{21}
$$

With that value of $$\zeta$$, equation (14) with $$\partial_l\Delta=0$$ can be integrated twice by hand. The first integration gives $$\tfrac{\epsilon}{3}\,u\,\Delta=(\Delta-\Delta(0))\,\Delta'$$, and the second gives

$$
y-\ln y=1+\frac{\epsilon\,u^2}{6\,\Delta(0)},\qquad y\equiv\frac{\Delta(u)}{\Delta(0)}.
\tag{22}
$$

This is the orange curve in Figure 4. ($$\Delta(0)$$ itself is not fixed by the equation. It just sets the unit of $$u$$.) Expanding (22) near $$u=0$$ gives the cusp (17) with

$$
\chi=\sqrt{\frac{\epsilon\,\Delta(0)}{3}},\qquad\Delta''(0^+)=\frac{2\epsilon}{9}.
\tag{23}
$$

Notice that $$\Delta$$ is of order $$\epsilon$$, which is what I promised when I said how far (14) can be trusted.

Next the friction. Combine the coarse-graining (20) with the rescaling (9): $$\partial_l\ln\eta=2-z-\Delta''(0^+)$$. For the friction to stay put,

$$
z=2-\frac{2\epsilon}{9}.
\tag{24}
$$

Finally the velocity. It is a displacement over a time, so it scales as $$b^{\zeta-z}$$, while the force measured from threshold scales as $$b^{-(2-\zeta)}$$ by (9). So $$v\sim(f-f_c)^{\beta}$$ with $$\beta=\nu\,(z-\zeta)$$.

For the rubber band, $$\epsilon=3$$:

| Exponent | What it is | This calculation | Measured [16] |
|---|---|---|---|
| $$\zeta$$ | roughness | $$1$$ | $$1.25$$ |
| $$\nu=1/(2-\zeta)$$ | largest avalanche, $$\ell_{\rm dep}\sim(f-f_c)^{-\nu}$$ | $$1$$ | $$1.33$$ |
| $$z$$ | how much slower big pieces are | $$4/3$$ | $$1.43$$ |
| $$\beta=\nu(z-\zeta)$$ | velocity, $$v\sim(f-f_c)^{\beta}$$ | $$1/3$$ | $$0.25$$ |

Not great, not terrible. $$\epsilon=3$$ is not a small number, and we kept only the first term of an expansion in it. The next term has been worked out [14, 15]: $$\zeta=\tfrac{\epsilon}{3}(1+0.143\,\epsilon)$$, which is $$1.43$$ for the rubber band. It overshoots in the other direction, and the truth is in between. What I'd take away from the calculation is not the numbers but the structure: there is a fixed function, it has a cusp, and the cusp is the avalanches and the threshold. A review that covers all of this in much more detail is [17].

#### Why not 2/3?

Part 1 said that a rubber band that has had time to relax has a different roughness, $$\zeta_{\rm eq}=2/3$$. Where is that in (14)?

The answer is a little surprising. To this order, (14) is the same equation for a relaxed band and a dragged one [15]. What differs is the function you start from. If the rough surface is made of random bumps of *energy*, each of finite size, then the force is the derivative of something that dies off, and its $$\Delta$$ has zero area. In that case the area argument above says nothing, and (14) has another fixed point, with $$\zeta\approx0.208\,\epsilon$$ [14]. That is $$0.62$$ for the rubber band, close to $$2/3$$.

For a band that is being dragged, the next order of the expansion shows that zero area doesn't survive. Dragging generates area, and you end up at the fixed point (22) whatever kind of rough surface you started with [14, 15]. That is why Part 1 had two roughnesses but only one kind of depinning.

### What's next

Here is this post in one place:

| Quantity | Meaning | Result |
|---|---|---|
| $$S$$ | the MSR action | equation (6) |
| $$\hat u$$ | response field: how you kick the system | $$\overline{u\,\hat u}=R$$ |
| $$\Delta(u)$$ | correlator of the pinning force | flows by (14) |
| $$R_c$$ | Larkin length | where $$-\Delta''(0)$$ blows up, (16) |
| $$\chi=-\Delta'(0^+)$$ | slope of the cusp | sets the threshold force, (19) |
| $$\Delta''(0^+)$$ | curvature next to the cusp | sets the friction, (20) |

Everything here was at zero temperature. Temperature adds the term $$-\eta T\int_{xt}\hat u^2$$ to the action, and in the flow equation (14) it adds a term proportional to $$T\,\Delta''(u)$$. That is a diffusion term, and what diffusion does to a kink is round it off. But the temperature also shrinks as we zoom out, because the barriers grow with scale (equation (7) of Part 1), so the rounding is squeezed into a thinner and thinner layer around $$u=0$$. Since the cusp is the avalanches and the threshold force, how it gets rounded is how the band creeps. That is the next part.

### References

1. A. Rosso, A. K. Hartmann, W. Krauth, "Depinning of elastic manifolds," *Phys. Rev. E* **67**, 021602 (2003). [doi:10.1103/PhysRevE.67.021602](https://doi.org/10.1103/PhysRevE.67.021602)
2. M. Mézard, G. Parisi, "Replica field theory for random manifolds," *J. Phys. I France* **1**, 809 (1991). [doi:10.1051/jp1:1991171](https://doi.org/10.1051/jp1:1991171)
3. H. Leschhorn, "Interface depinning in a disordered medium — numerical results," *Physica A* **195**, 324 (1993). [doi:10.1016/0378-4371(93)90161-V](https://doi.org/10.1016/0378-4371(93)90161-V)
4. K. G. Wilson, J. Kogut, "The renormalization group and the ε expansion," *Phys. Rep.* **12**, 75 (1974). [doi:10.1016/0370-1573(74)90023-4](https://doi.org/10.1016/0370-1573(74)90023-4)
5. L. P. Kadanoff, W. Götze, D. Hamblen, R. Hecht, E. A. S. Lewis, V. V. Palciauskas, M. Rayl, J. Swift, D. Aspnes, J. Kane, "Static phenomena near critical points: theory and experiment," *Rev. Mod. Phys.* **39**, 395 (1967). [doi:10.1103/RevModPhys.39.395](https://doi.org/10.1103/RevModPhys.39.395)
6. L. P. Kadanoff, "Scaling laws for Ising models near $$T_c$$," *Physics* **2**, 263 (1966). [doi:10.1103/PhysicsPhysiqueFizika.2.263](https://doi.org/10.1103/PhysicsPhysiqueFizika.2.263)
7. P. C. Martin, E. D. Siggia, H. A. Rose, "Statistical dynamics of classical systems," *Phys. Rev. A* **8**, 423 (1973). [doi:10.1103/PhysRevA.8.423](https://doi.org/10.1103/PhysRevA.8.423)
8. H.-K. Janssen, "On a Lagrangean for classical field dynamics and renormalization group calculations of dynamical critical properties," *Z. Phys. B* **23**, 377 (1976). [doi:10.1007/BF01316547](https://doi.org/10.1007/BF01316547)
9. C. De Dominicis, "Techniques de renormalisation de la théorie des champs et dynamique des phénomènes critiques," *J. Phys. Colloques* **37**, C1-247 (1976). [doi:10.1051/jphyscol:1976138](https://doi.org/10.1051/jphyscol:1976138)
10. O. Narayan, D. S. Fisher, "Threshold critical dynamics of driven interfaces in random media," *Phys. Rev. B* **48**, 7030 (1993). [doi:10.1103/PhysRevB.48.7030](https://doi.org/10.1103/PhysRevB.48.7030)
11. D. S. Fisher, "Interface fluctuations in disordered systems: $$5-\epsilon$$ expansion and failure of dimensional reduction," *Phys. Rev. Lett.* **56**, 1964 (1986). [doi:10.1103/PhysRevLett.56.1964](https://doi.org/10.1103/PhysRevLett.56.1964)
12. T. Nattermann, S. Stepanow, L.-H. Tang, H. Leschhorn, "Dynamics of interface depinning in a disordered medium," *J. Phys. II France* **2**, 1483 (1992). [doi:10.1051/jp2:1992214](https://doi.org/10.1051/jp2:1992214)
13. A. A. Middleton, "Asymptotic uniqueness of the sliding state for charge-density waves," *Phys. Rev. Lett.* **68**, 670 (1992). [doi:10.1103/PhysRevLett.68.670](https://doi.org/10.1103/PhysRevLett.68.670)
14. P. Chauve, P. Le Doussal, K. J. Wiese, "Renormalization of pinned elastic systems: how does it work beyond one loop?," *Phys. Rev. Lett.* **86**, 1785 (2001). [arXiv:cond-mat/0006056](https://arxiv.org/abs/cond-mat/0006056)
15. P. Le Doussal, K. J. Wiese, P. Chauve, "Two-loop functional renormalization group theory of the depinning transition," *Phys. Rev. B* **66**, 174201 (2002). [arXiv:cond-mat/0205108](https://arxiv.org/abs/cond-mat/0205108)
16. E. E. Ferrero, S. Bustingorry, A. B. Kolton, "Nonsteady relaxation and critical exponents at the depinning transition," *Phys. Rev. E* **87**, 032122 (2013). [arXiv:1211.7275](https://arxiv.org/abs/1211.7275)
17. K. J. Wiese, "Theory and experiments for disordered elastic manifolds, depinning, avalanches, and sandpiles," *Rep. Prog. Phys.* **85**, 086502 (2022). [arXiv:2102.01215](https://arxiv.org/abs/2102.01215)
