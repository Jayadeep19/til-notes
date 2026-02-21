---
layout: post
title: "Vector calculus"
author: "Jayadeep"
categories: journal
tags: [documentation,vector calculus,January26]
image:
---

Todays topics:
* TOC
{:toc}

## Chain rule:
- Composite functions: They are functions that are composed of another functions, for example: $F(x) = f(g(x))$. Chain rule can be used to derivate these types of functions
-  The above is a simple case, we can just say that the internal function be like y = $f(x)$ then $x= g(t)$. So finally we get $\frac{dy}{dt} = \frac{dy}{dx} \cdot \frac{dx}{dt}$
- There are other cases depending on the number of variables in the function.
1. Case 1: $z = f(x,y), x = g(t), y = h(t)$, we need to find the ratio $\frac{dz}{dt}$
    - Since the connection between independent and dependent variable is direct, we can use the form similar to above
    -  $\frac{dz}{dt}=\frac{\partial{f}}{\partial{x}} \cdot\frac{dx}{dt} + \frac{\partial{f}}{\partial{y}} \cdot \frac{dy}{dt}$
2. Case 2: $z=f(x,y);x=g(s,t)and y=h(s,t)$ Here the internal function has two variables in them, so the goal here becomes finding two ratios $\frac{\partial{z}}{\partial{s}} \& \frac{\partial{z}}{\partial{t}}$
    - We can use a tree diagram to find all the components of differential function.
    - ![chainrule]({{"/assets/img/calculus/chain_rule.png" | relative_url}})
    - The first two branches becomes the immediat variables inside the main function. Then in the second layer the internal functions can be further divided into their own independent variables.
    - At the end we add allthe partial derivatives together which gives the final ratio.

## Vector fields and Gradients:
- A **gradient** is fancy word for a derivative or rate of change of function. A vector that points in the direction of greatest(steepest) change of a function
- A **vector field** is also called as 'gradient vector field. So , a vector field in 2D or 3D is a function $\overrightarrow{F}$. It assigns a 2D or 3D vector to each point in the space. 
- Scalar functions and Vector functions!! When the function taht outputs scalars then it is a scalar function. When the output is  vector, then it is a vector function.
- Now For a scalar function $f(x,y,z)$ the vector function becomes $\nabla{f} = <f_x,f_y,f_z>$. The terms inside the vector function are the **partial derivatives of the function f wrt (x,y,z)**.
    - $$\nabla{f}= \begin{bmatrix} f_x \\ f_y \\ f_z \end{bmatrix} = f_x\overrightarrow{i}+f_y\overrightarrow{j}+f_z\overrightarrow{z}$$
- An example of vector field is the flow of fluid inside a pipe 
![vector field]({{"/assets/img/calculus/vector_field.png" | relative_url}})
- An example take the function $f(x,y) = x^2 sin(5y)$. Then the vector field becomes $\nabla{f} = <2xsin(5y), 5x^2cos(5y)>$.
- We can substitue the x and y values to the vector function to get the directional vector for that point in the space representing the biggest change in the function.
![grad-vecfield]({{"/assets/img/calculus/gradients_vectorfield.png" | relative_url}})

## Directional derivatives:
- A directional derivative is the rate of change of function in a given direction. In another words, given a direction, the quantity of change of functions in given by directional derivative.
- In the previous chapter we saw that the maximum change in the function is given by the gradient, so it would be common sense to think that the **directional derivative would be maximum when the directional derivative points towards the gradient**.
- We can prove this using dot product properties:
    - Given a function f(x,y) and a unit vector $\overrightarrow{u} = <a,b>$ then the directional derivative is represented as $D_\dot{u}f(x,y)$
    - The directinal derivative is given as  $$D_\dot{u}f(x,y) = f_x(x,y)a + f_y(x,y)b \implies <f_x, f_y>.<a,b> \iff \begin{bmatrix} f_x \\ f_y \end{bmatrix} \cdot \begin{bmatrix} a \\ b \end{bmatrix}$$
    - This above expression boils down to the dot product between the gradient vector function and unit vector for given direction.
    - $D_\dot{u}f(x,y) = \nabla{f} \cdot \overrightarrow{u}$
    - Then the max value for the $D_\dot{u}f(x,y)$ occurs when the angle between the two vectors is 0. cause $cos(0)=1$ in other words, when the directional vector points towards unit vector.

## Jacobian and Hessian Matrices:
