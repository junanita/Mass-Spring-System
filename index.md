## Introduction 

Mass Spring System is an interesting model in Computer graphics, usually used to simulate cloth. This model includes several mass points which connected to each other by springs, and springs will expand, stretch and exterting forces on the mass points. To make it clear, I will break down the system into following categories: 
1. Equation of Motion
2. Integrators
3. TBC

## Equation of Motion 

### Newton's laws

### Hook’s law (Spring Force)

### Gravitational Force
### Linear Damping Force
### Spring Damping Force

## Integrators
### Forward Euler
The Forward Euler, also called Explicit Euler, is a first-order numerical procedure for solving ODEs with a given initial value. It is called explicit because of the specific order it evaluates the position and velocity updates: the values for the next time step are all computed by directly evaluating expressions using the values of the last time step.

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n}" title="q^{n+1} = q^n + h \dot{q}^{n}" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n},&space;\dot{q}^{n})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n},&space;\dot{q}^{n})" title="\dot{q}^{n+1} = \dot{q}^n + h M^{-1}F(q^{n}, \dot{q}^{n})" /></a>

where `q` is the configuration/position of the mass. 

Though simple, Euler’s method is not accurate. Consider the case of a 2D function f whose integral curves are concentric circles. A point p governed by f is supposed to orbit forever on whichever circle it started on. Instead, with each Euler step, p will move on a straight line to a circle of larger radius, so that its path will follow an outward spiral. Shrinking the stepsize will slow the rate of this outward drift, but never eliminate it[3]. 


### Backward Euler
Same as Forward Euler, the Backward Euler method, also called Implicit Euler, is an another commonly used integrator for solving ODEs. However, the problem with this method is that the unknowns (the values for time step `n + 1`) are present on both sides of the formula and it becomes an equation that needs to be solved, rather than a direct evaluation[1].

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n&plus;1}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n&plus;1}" title="q^{n+1} = q^n + h \dot{q}^{n+1}" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n&plus;1},&space;\dot{q}^{n&plus;1})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n&plus;1},&space;\dot{q}^{n&plus;1})" title="\dot{q}^{n+1} = \dot{q}^n + h M^{-1}F(q^{n+1}, \dot{q}^{n+1})" /></a>

### Symplectic Euler
The Symplectic Euler integrator is also called semi-implicit Euler or forward-backward Euler. It's a combination of Farward Euler and Backward Euler by updating velocity with the position and velocity at the previous timestep, while updating position  depends on the velocity at the current timestep.

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n&plus;1}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;q^{n&plus;1}&space;=&space;q^n&space;&plus;&space;h&space;\dot{q}^{n&plus;1}" title="q^{n+1} = q^n + h \dot{q}^{n+1}" /></a>

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n},&space;\dot{q}^{n})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_phv&space;\dot{q}^{n&plus;1}&space;=&space;\dot{q}^n&space;&plus;&space;h&space;M^{-1}F(q^{n},&space;\dot{q}^{n})" title="\dot{q}^{n+1} = \dot{q}^n + h M^{-1}F(q^{n}, \dot{q}^{n})" /></a>

Unlike Forward Euler that produces an unstable orbit and ‘spiral outward,’ Symplectic Euler could produce a stable orbit.


## Examples
### Multiple springs
Multiple springs with increasing damping coefficients oriented along the horizontal and slightly stretched from their rest lengths. Intended to test the behavior of increasing the damping coefficient.

### Cantilever
A cantilever integrated with Symplectic Euler (forward-backward-euler). Intended to test multiple springs acting on the same particle.


### References:
[1]. [edX: Animation and CGI Motion](https://www.edx.org/course/animation-cgi-motion-columbiax-csmm-104x)

[2]. [Jernej Barbic: Helper slides of `Computer Animation and Simulation Spring 2015`](http://run.usc.edu/cs520-s15/assign1/jellocube_csci520.pdf)

[3]. [Pixar: Physically Based Modeling](https://www.pixar.com/assets/pbm2001/index.html)
