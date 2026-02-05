---
layout: post
title: "Linear algebra basics"
author: "Jayadeep"
categories: journal
tags: [documentation,linear algebra, January26]
image: 
---

In the past couple of weeks, I decided to give my math skills a vist. Namely, Linear Algebra and calculus. These branches of Math are crucial for several fields of Engineering such as Computer vision, Image processing, Ml, AI to name some. Although I will be diving deep into statistics from tommorrow (I got a very nice book called "I'll tell you in the next blogs"). I would like to write a few blogs about the basic and imortant concepts that I studied(again!!) in the past 10-12 days. Starting with the good old Linear Algebra.

So Linear Algebra to simply put is dealing with geometric shapes. How we represent them mathematically and find their solutions. Example, when there are two lines, we can investigate where is they were intersectig, how do we find if they are really intersecting or they are just parallel lines? One might have heard about terms like Vectors, Scalars, Matrices. These can be considered as the core for Linear algebra. So, why is this important for Ml or AI? Well, These matrices provide a nice way to store the huge data for training the models. More over, neural networks use matrix multiplication to improve their accuracies.

1. Vectors: \\
A vector is an imaginary line that has length and direction. We can also think of a vector as a line that is directed from one point in space to another. For example when a ball is thrown in a staright line, we can say that the velocity of the ball as a vector and has a direction and a certain magnitude. It is represented as $\vec{v}$ 

    - The length of the vector is then given by $\lvert \lvert v \rvert\rvert$. It is a scalar value
    - A vector with zero length is a Zero vector, so it becomes a *point*.

2. Vector operations: \\
Two vectors can be added, subtracted or multiplied.

    - Multiplication: A vector can be multiplied by a scalar to scale the vector it is called vector scaling. \
    Two vectors can also be multiplied which we come across later.
    $$
    k*\vec{v} = k\vec{v}
    $$
    
    - Addition and subtraction: Two vectors **u and v** can be added to form a resutling vector **u+v**. However, subtraction of two vectors can be performed by addition and multiplication of '-1' to another vector 
    $$
    \vec{u}+(-1)*\vec{v} = \vec{u}-\vec{v}
    $$

3. Vector Bases and coordinates: \\
Now that ve know what a vecotr is, now how do we represent this vector in space. For this we take the help of coordinates. we define the coordinates as follows
    - In 1D: \
    When we two vectors of sifferent size, we can always represent the bigger vector as a scaled version of the smaller vector. We use this same principle to represent $\vec{v}$ in 1D space. We consider **'e'** as a known length and represent **v** as $v = xe$. Meaning, 'e' scaled by 'x' in 1D gives us the vector 'v'. 
    - In 2D and 3D: \
    In the similar way as 1D space, now we have two axes or **bases vectors (e)**. And the vector 'v' can be represented as a liner combination of the both scaled bases vectors. 
    $$
    v = x_1*e_1+x_2*e_2 \\
    v = x_1*e_1+x_2*e_2+x_3*e_3
    $$
    - The scalars $(x_1,x_2,x_3)$ are nothing but the coordinates of the vector v along the axis xyz respectively. They can be represented as $(v_x, v_y, v_z)$ respectively.
    - These vectors can be written in another form called vector notation:
        - 1D: $$\textbf{v} = \begin{bmatrix} v_x \end{bmatrix}$$
        - 2D: $$\textbf{v} = \begin{bmatrix} v_x \\\ v_y \end{bmatrix}$$
        - 3D: $$\textbf{v} = \begin{bmatrix} v_x \\\ v_y \\\ v_z\end{bmatrix}$$
    - The next set of terms in our equation are $(e_1, e_2, e_3)$ in our equation. These are (simply putting) are the vectors representing the individual axis of the coordinate space.
        - say we have a 3D space: the $e_1$ also called as 1st basis vector for the coordinate space would become $e_1 = \begin{bmatrix} 1\\\0\\\0 \end{bmatrix}$
        - similarly, $e_2 = \begin{bmatrix} 0\\\1\\\0 \end{bmatrix}$ and $e_3 = \begin{bmatrix} 0\\\0\\\1 \end{bmatrix}$
        - These are also called as **standard basis**
4. Vector spaces:
What happens when we need to represent a vector in more than 3D (as discussed above)? We use something called Vector Space. Vector space is a very common term in linear algebra. We can think of it as a space defined be objects like arrows(vectors), where one can add objects together or scale them. And both these operations follow a well defined commands. They are also called as linear spaces
- A vector space in an n-D is represented as $R^n$ and a vector in this space can be represented as 
$$
\vec{u} = \sum_{i = 1}^n u_i*e_i
$$
- where $u_i$ are the coordinates of $\vec{u}$ and $e_i$ are the bases vectors for $R^n$ vector space.
- Now we can summarise the basis vectors for $R^n$ as (**canonical of vector space**):
$$
e_1 = (1,0...0), \\
e_2 = (0,1...0), \\
. \\
. \\
e_n = (0,0...1),
$$
- The set of scalars (Real or complex numbers) used to scale the vectors in this vector space are called as **Field** for the vector space. The properties(eg: dimensions) of the vector space depends on the Field


     







