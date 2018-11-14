<p align="center"> 
<img src="https://media.giphy.com/media/3gWyfB5RlOK1hnsqQj/giphy.gif">
</p>

## Introduction 

Mass Spring System is an interesting model in Computer graphics, usually used to simulate cloth. This model includes several mass points which connected to each other by springs, and springs will expand, stretch and exterting forces on the mass points. To make it clear, I will break down the system into following categories: 
1. Equation of Motion
2. Integrators
3. Examples

## Equation of Motion 
###  Unary forces
#### Gravitational Force
The gravatitionl force on each mass is `F = mG`, where g is a gravatitional constant.

#### Gravitational Attraction Force
The potential energy of two particles interacting via gravity is given by:

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;U(x_i,&space;x_j)&space;=&space;\frac{Gm_im_j}{l}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;U(x_i,&space;x_j)&space;=&space;\frac{Gm_im_j}{l}" title="U(x_i, x_j) = \frac{Gm_im_j}{l}" /></a>

where `l` is the distance between the two particles: <a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;l&space;=&space;\sqrt{(x_i&space;-&space;x_j)^2}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;l&space;=&space;\sqrt{(x_i&space;-&space;x_j)^2}" title="l = \sqrt{(x_i - x_j)^2}" /></a> and `n` is a unit-length vector pointing from vertex `xj` to vertex `xi`
<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;\widehat{n}&space;=&space;\frac{(xi&space;-&space;xj&space;)}{\sqrt{(xi&space;-&space;xj)^2}}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;\widehat{n}&space;=&space;\frac{(xi&space;-&space;xj&space;)}{\sqrt{(xi&space;-&space;xj)^2}}" title="\widehat{n} = \frac{(xi - xj )}{\sqrt{(xi - xj)^2}}" /></a>
And we also have
<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;\bigtriangledown&space;_{xi}&space;l&space;=&space;\widehat{n}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;\bigtriangledown&space;_{xi}&space;l&space;=&space;\widehat{n}" title="\bigtriangledown _{xi} l = \widehat{n}" /></a>, 
<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;\bigtriangledown&space;_{xj}&space;l&space;=&space;-&space;\widehat{n}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;\bigtriangledown&space;_{xj}&space;l&space;=&space;-&space;\widehat{n}" title="\bigtriangledown _{xj} l = - \widehat{n}" /></a>

Therefore we got gravatitional force for vertex `xi` and `xj` to be:

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;f_{xi}=&space;-\bigtriangledown&space;_{xi}&space;U&space;=&space;-&space;\frac{Gm_im_j}{l^2}&space;\widehat{n}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;f_{xi}=&space;-\bigtriangledown&space;_{xi}&space;U&space;=&space;-&space;\frac{Gm_im_j}{l^2}&space;\widehat{n}" title="f_{xi}= -\bigtriangledown _{xi} U = - \frac{Gm_im_j}{l^2} \widehat{n}" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;f_{xj}=&space;-\bigtriangledown&space;_{xj}&space;U&space;=&space;\frac{Gm_im_j}{l^2}&space;\widehat{n}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;f_{xj}=&space;-\bigtriangledown&space;_{xj}&space;U&space;=&space;\frac{Gm_im_j}{l^2}&space;\widehat{n}" title="f_{xj}= -\bigtriangledown _{xj} U = \frac{Gm_im_j}{l^2} \widehat{n}" /></a>

### Viscous Drag
#### Linear Damping Force
The ideal viscous drag is the linear damping force that linearly resists a particle’s motion:

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;F_i&space;=&space;-\beta&space;v_i" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;F_i&space;=&space;-\beta&space;v_i" title="F_i = -\beta v_i" /></a>

where <a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;\beta" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;\beta" title="\beta" /></a> is a scalar damping constant.
Note that a damping force, by its very (dissipative) nature, is not conservative, i.e., it does not act to preserve total energy[1].

### N-nary forces

#### Spring Force (Hook’s law)
In a basic mass-spring system, the springs are the structural elements that hold everything together. 

The potential energy of a spring between a pair of particles at positions `xi` and `xj` is

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;U(xi,&space;xj)&space;=&space;\frac{1}{2}k(l(x_i,&space;x_j)&space;-&space;l_0)^2" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;U(xi,&space;xj)&space;=&space;\frac{1}{2}k(l(x_i,&space;x_j)&space;-&space;l_0)^2" title="U(xi, xj) = \frac{1}{2}k(l(x_i, x_j) - l_0)^2" /></a>

where `l` would be the same from section **Gravitational Attraction Force**, `l0` denotes the spring’s rest length, and `k` denotes the spring’s stiffness.

Therefore, the spring force on `xi` and `xj`:

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;F_{xi}&space;=&space;-&space;\bigtriangledown_{xi}U&space;=&space;-&space;k&space;(l&space;-&space;l_0)&space;\widehat{n}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;F_{xi}&space;=&space;-&space;\bigtriangledown_{xi}U&space;=&space;-&space;k&space;(l&space;-&space;l_0)&space;\widehat{n}" title="F_{xi} = - \bigtriangledown_{xi}U = - k (l - l_0) \widehat{n}" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;F_{xj}&space;=&space;-&space;\bigtriangledown_{xj}U&space;=&space;k&space;(l&space;-&space;l_0)&space;\widehat{n}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;F_{xj}&space;=&space;-&space;\bigtriangledown_{xj}U&space;=&space;k&space;(l&space;-&space;l_0)&space;\widehat{n}" title="F_{xj} = - \bigtriangledown_{xj}U = k (l - l_0) \widehat{n}" /></a>

where `n` is the unit-length vector defined from section **Gravitational Attraction Force**.

Example below shows the behavior of the decreasing rest length `l0` integrated with symplectic Euler (scene xml from [1]).

<p align="center"> 
<img src="https://media.giphy.com/media/piKaMfpEpFaQ3F8Try/giphy.gif">
</p>

#### Spring Damping Force

Example below shows the behavior of increasing the damping coefficient. Multiple springs with increasing damping coefficients oriented along the horizontal (scene xml from [1]).


<p align="center"> 
<img src="https://media.giphy.com/media/EBodbd4F9r0B5rteTr/giphy.gif">
</p>


## Integrators
### Forward Euler
The Forward Euler, also called Explicit Euler, is a first-order numerical procedure for solving ODEs with a given initial value. It is called explicit because of the specific order it evaluates the position and velocity updates: the values for the next time step are all computed by directly evaluating expressions using the values of the last time step.

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n}" title="q^{n+1} = q^n + h \dot{q}^{n}" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n},&space;\dot{q}^{n})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n},&space;\dot{q}^{n})" title="\dot{q}^{n+1} = \dot{q}^n + h M^{-1}F(q^{n}, \dot{q}^{n})" /></a>

where `q` is the configuration/position of the mass.

Though simple, Euler’s method is not accurate. Consider the case of a 2D function f whose integral curves are concentric circles. A point p governed by f is supposed to orbit forever on whichever circle it started on. Instead, with each Euler step, p will move on a straight line to a circle of larger radius, so that its path will follow an outward spiral. Shrinking the stepsize will slow the rate of this outward drift, but never eliminate it[3] (scene xml from [1]).

<p align="center"> 
<img src="https://media.giphy.com/media/14SFyXJ9AnDxmdMX7X/giphy.gif">
</p>

### Backward Euler
Same as Forward Euler, the Backward Euler method, also called Implicit Euler, is an another commonly used integrator for solving ODEs. However, the problem with this method is that the unknowns (the values for time step `n + 1`) are present on both sides of the formula and it becomes an equation that needs to be solved, rather than a direct evaluation[1].

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n&plus;1}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n&plus;1}" title="q^{n+1} = q^n + h \dot{q}^{n+1}" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n&plus;1},&space;\dot{q}^{n&plus;1})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n&plus;1},&space;\dot{q}^{n&plus;1})" title="\dot{q}^{n+1} = \dot{q}^n + h M^{-1}F(q^{n+1}, \dot{q}^{n+1})" /></a>

### Symplectic Euler
The Symplectic Euler integrator is also called semi-implicit Euler or forward-backward Euler. It's a combination of Farward Euler and Backward Euler by updating velocity with the position and velocity at the previous timestep, while updating position  depends on the velocity at the current timestep.

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n&plus;1}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n&plus;1}" title="q^{n+1} = q^n + h \dot{q}^{n+1}" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n},&space;\dot{q}^{n})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n},&space;\dot{q}^{n})" title="\dot{q}^{n+1} = \dot{q}^n + h M^{-1}F(q^{n}, \dot{q}^{n})" /></a>

Unlike Forward Euler that produces an unstable orbit and ‘spiral outward,’ Symplectic Euler could produce a stable orbit (scene xml from [1]).


<p align="center"> 
<img src="https://media.giphy.com/media/9A3sYUICIxe8y3F25D/giphy.gif">
</p>

## Examples

### Cantilever
Example below shows multiple springs acting on the same particle. A cantilever integrated with Symplectic Euler (scene xml from [1]).

<p align="center"> 
<img src="https://media.giphy.com/media/dlbGP1EKJTcV1va4An/giphy.gif">
</p>


### References:
[1]. [edX: Animation and CGI Motion](https://www.edx.org/course/animation-cgi-motion-columbiax-csmm-104x)

[2]. [Jernej Barbic: Helper slides of `Computer Animation and Simulation Spring 2015`](http://run.usc.edu/cs520-s15/assign1/jellocube_csci520.pdf)

[3]. [Pixar: Physically Based Modeling](https://www.pixar.com/assets/pbm2001/index.html)
