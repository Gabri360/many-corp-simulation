# Many Corp Simulation
## Contents
- [Introduction](#introduction)
- [Dependencies and Project Usage](#dependencies-and-project-usage)
- [Configuration](#configuration)
- [Project Composition](#project-composition)
- [Integration Methods](#integration-methods)
  - [Euler Method](#euler-method)
  - [Verlet Velocity](#verlet-velocity)
  - [Runge Kutta 4](#runge-kutta-4)
  - [Runge Kutta 45](#runge-kutta-45)


## Introduction
I wrote this project to simulate the motion of *N* bodies (where *N* is an arbitrary number) following classical laws of motion in an electromagnetic field. I'm attaching a couple of screenshots:

![](doc/sim1.jpeg)
---
![](doc/sim2.jpeg)

## Dependencies and Project Usage
The project is written entirely in C and requires the `make` and `gcc` software. It also uses the OpenGL library `<GLFW/glfw3.h>`. This can be downloaded either from the official repositories (on Linux recommended) or from the website: https://www.glfw.org/download.html.

Alternatively, you can download the `glfw` library into the `vendor/glfw` folder using the command:
```bash
git submodule update --init --recursive
```
Then you need to compile the library using the commands, in `many-corp-simulation/`:
```bash
cd vendor/glfw
cmake -B build
cmake --build build
```
---
Once the files are been downloaded, you need to enter the folder `many-corp-simulation/` and start the compilation with the command:

```bash
make
```

This will create a `sim` executable file (as well as object files in the `build/` folder). Then you have to run it from terminal with the command (on linux):
```bash
./sim (parameter)
```
This will create two windows in which you can view the simulation.
The parameter you can include in the command represents the solution method used for the equations of motion:
- 1 => **Euler method**-------`./sim 1`
- 2 => **Verlet Velocity**-----`./sim 2`
- 3 => **Runge Kutta 4**------`./sim 3`
- 4 => **Runge Kutta 45**-----`./sim 4`

Another available command is:
```bash
make clean
```
which deletes all object files from `build/`
## Configuration
The `config.txt` file is used to configure the simulation parameters. By default, it contains the following data:
```text
e=100           # Number of electron
pr=100          # Number of proton
A=10            # How many angstroms long is half the simulation?
C=-11.1         # Collision potential effectiveness
ut=5e-7         # Simulation time unit
```
The first two values ​​(number of electrons and protons) can be modified at will (within a limit otherwise, it explodes). I recommend leaving the other parameters as they are, but obviously, you can do whatever you like. (Pay attention to the effectiveness parameter, which is negative by default; to increase it, you need to change -11.1 -> -10.1)

Once you have modified the `config.txt` file, you must save it and recompile the entire project.

Another variable that can be modified is the **number of dimensions**; to change it, you must modify the 5th line of the `head/simlib_struct.h` file (It is set to `DIM=2` by default):
```c
#define DIM 2
```
The values ​​represented graphically ​​are `DIM=[2,3,4]`, the third dimension is represented by the size of the particles, while the fourth is represented by the opacity of their color. However, the program is generalized for any integer number of dimensions.

## Project Composition
The project is divided into the following folders:
- `prg/` ----| The main program is located here:
    - `sim.c`
- `lib/` ----| The libraries are located here:
    - `simlib.c` --------------*initialization of variables and calculation of recurring quantities*
    - `simlib_metodi.c` -----*the four methods of resolution*
    - `simlib_base.c` -------*useful mathematical functions*
- `head/` ---| The header files are located here:
    - `simlib_struct.h` ----*structures used*
    - `simlib.h`
    - `simlib_metodi.h`
    - `simlib_base.h`
- `build/` --| The Makefile is located here, and the compilation results will end up here.
- `doc/` -----| Here are the files useful for the README.md.
- `vendor/`--| The `glfw` library is located here if downloaded manually.

## Integration Methods
In this section, I want to briefly explain the integration methods used. The goal is to solve the following differential equation (Newton's second law).
```math
F(\vec{x_j})=m\frac{d^2\vec{x_j}}{dt^2}
```
Where $F(x_j)$ is the force acting on the particle $j$ with position $\vec{x_j}$, which follows the law:
```math
F(\vec{x_j})=\sum_{i \neq j}^N\left[G\,q_j\,q_i \dfrac{1}{(\vec{x_j}-\vec{x_i})^2}\;+\;\frac{2.4\cdot 10^C}{G}\left(\frac{5}{\vec{x_j}-\vec{x_i}}\right)^{13}\right]
```
Where $N$ is the total number of particle and $C$ is a parameter that can be passed via the *config*. The first term is the **Coulomb force** and the second is a **repulsive potential** between all the particles, that becoming rilevant only when the distance becomes very small, to prevent the bodies from overlapping (which is the short-range term of the **Lennard-Jones potential**).

### Euler Method

```math
\begin{align*}
&\vec{v}(t+dt)=\vec{v}(t) + dt\;\frac{F(\vec{x})}{m} +O(dt^2)\\[4mm]
&\vec{x}(t+dt)=\vec{x}(t) + dt\;\vec{v}(t+dt) + O(dt^2)
\end{align*}
```

### Verlet Velocity

```math
\begin{align*}
&\vec{x}(t+dt)=\vec{x}(t) + dt\;\vec{v}(t) + \frac{1}{2}dt^2\;\frac{F(\vec{x})}{m} + O(dt^3)\\[4mm]
&\vec{v}(t+dt)=\vec{v}(t) + \frac{1}{2}dt\;\frac{F(\vec{x}(t))+F(\vec{x}(t+dt))}{m} +O(dt^2)
\end{align*}
```

### Runge Kutta 4
- step 1
```math
\begin{align*}
&\vec{k}_{x,1}=\vec{v}(t)\\[4mm]
&\vec{k}_{v,1}=\frac{F\left(\vec{x}(t)\right)}{m}
\end{align*}
```
- step 2
```math
\begin{align*}
&\vec{k}_{x,2}=\vec{v}(t)+\frac{dt}{2}\vec{k}_{v,1}\\[4mm]
&\vec{k}_{v,2}=\frac{F\left(\vec{x}(t)+\frac{dt}{2}\vec{k}_{x,1}\right)}{m}
\end{align*}
```
- step 3
```math
\begin{align*}
&\vec{k}_{x,3}=\vec{v}(t)+\frac{dt}{2}\vec{k}_{v,2}\\[4mm]
&\vec{k}_{v,3}=\frac{F\left(\vec{x}(t)+\frac{dt}{2}\vec{k}_{x,2}\right)}{m}
\end{align*}
```
- step 4
```math
\begin{align*}
&\vec{k}_{x,4}=\vec{v}(t)+dt\;\vec{k}_{v,3}\\[4mm]
&\vec{k}_{v,4}=\frac{F\left(\vec{x}(t)+dt\;\vec{k}_{x,3}\right)}{m}
\end{align*}
```
- final update
```math
\begin{align*}
&\vec{x}(t+dt)=\vec{x}(t)+\frac{dt}{6}\left(\vec{k}_{x,1}+2\vec{k}_{x,2}+2\vec{k}_{x,3}+\vec{k}_{x,4}\right)+O(dt^5)\\[4mm]
&\vec{v}(t+dt)=\vec{v}(t)+\frac{dt}{6}\left(\vec{k}_{v,1}+2\vec{k}_{v,2}+2\vec{k}_{v,3}+\vec{k}_{v,4}\right)+O(dt^5)
\end{align*}
```
### Runge Kutta 45
This method implements both **Runge Kutta 4** and **Runge Kutta 5** and updates the integration step $dt$ based on the difference between the two results.
the two results are:
- $y^{(5)}$ solution with an error $O(dt^6)$
- $y^{(4)}$ solution with an error $O(dt^5)$

Actual local error:
```math
E=\left|\left|y^{(5)}-y^{(4)}\right|\right|
```
If the local error exceeds a tolerance parameter $tol$ (by default it is set to $10^{-5}$), the integration step is updated.
```math
dt_{new}=dt\cdot\left(\dfrac{tol}{E}\right)^{1/5}
```
And the entire process is recalculated until $E < tol$, and the accepted solution will be $y^{(5)}$.
