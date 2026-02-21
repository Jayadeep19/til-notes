---
layout: post
title: "Linear algebra basics"
author: "Jayadeep"
categories: journal
tags: [documentation,linear algebra, January26]
image: algebra/vectorbasis.PNG
---

In the past couple of weeks, I decided to give my math skills a vist. Namely, Linear Algebra and calculus. These branches of Math are crucial for several fields of Engineering such as Computer vision, Image processing, Ml, AI to name some. Although I will be diving deep into statistics from tommorrow (I got a very nice book called "I'll tell you in the next blogs"). I would like to write a few blogs about the basic and imortant concepts that I studied(again!!) in the past 10-12 days. Starting with the good old Linear Algebra.

So Linear Algebra to simply put is dealing with geometric shapes. How we represent them mathematically and find their solutions. Example, when there are two lines, we can investigate where is they were intersectig, how do we find if they are really intersecting or they are just parallel lines? One might have heard about terms like Vectors, Scalars, Matrices. These can be considered as the core for Linear algebra. So, why is this important for Ml or AI? Well, These matrices provide a nice way to store the huge data for training the models. More over, neural networks use matrix multiplication to improve their accuracies.

In this post:
* TOC
{:toc}

## Vectors
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
- An examlple of vector addition:
- ![vecaddition]({{"/assets/img/algebra/vecaddition.PNG" | relative_url}})

## Dot Product (Scaler Product)
- Dot product is very important concept in linear algebra, it helps determining the length and angle between the vectors.

1. dot product: \\
The dot product b/w teo vectors is denoted by $\vec{u} \cdot \vec{v}$. The product is always a *scalar value*(which is why it is called as a scalar product).
- $$
u \cdot v = 
\begin{cases} 
\|u\| \|v\| \cos[u, v], & \text{if } u \neq 0 \text{ and } v \neq 0, \\ 
0, & \text{if } u = 0 \text{ or } v = 0. 
\end{cases} $$
- $\|\|\mathbf{u}\|\|$ is the length of the vector **u**. And it is given by for ex in 3D: $\|\|\mathbf{u}\|\| = \sqrt{u_x^2 + u_y^2 + u_z^2}$
- $[u,v]$ is the angle between the two vectors and this has a specific implocation in the outcome of the dot product.
    - The dot product is +ve ($u \cdot v$) $\iff 0<[u,v]<\pi/2$
    - The dot product is -ve ($u \cdot v$) $\iff \pi/2<[u,v]<\pi$
    - The dot product is **0** ($u \cdot v$) $\iff [u,v]=\pi/2$ or u=0 or v = 0 **(This says that when the dot product is 0, the two vectors are orthogonal)**

2. Normalization: \\
A unit vector can be produced from a non zero vector. This process is called as normalization and the produced vector is called as normalized vector. 
- $n = \frac{\mathbf{u}}{\|\|u\|\|}$. Simply divide the vector with its length.
- This is an useful concept to just preserve the information on the direction of the vector. \\

3. One more area where the dot product is very useful is when we try to project a vector u onto another vector v. Also called as **Orthogonal projection**.
 ![orthonormal]({{"/assets/img/algebra/projection.PNG" | relative_url}})

4. Dot product in **ORTHONORMAL BASIS**:
- The dot product for an **orthonormal basis** is such that 
$$
e_i \cdot e_j = 
\begin{cases}
0, & \text{if} & i \ne j, \\
1, & \text{if} & i = j.
\end{cases}
$$
- Every vector in the set must be orthogonal (perpendicular) to each other and each vector must be a **unit vector** ($\|\|v\|\| = 1$). 
- From above sentence, We can now say that standard basis is an orthonormal basis
- This presents an interesting formula to find the dot product b/w two vectors
    - Say $\vec{u} = u_1e_1+ u_2e_2 + u_3e_3$ and $\vec{v}= v_1e_1+ v_2e_2 + v_3e_3$
    - Then $u \cdot v$ is given by:
    - ![orthonormal]({{"/assets/img/algebra/dotpro_orthonormal.PNG" | relative_url}})
    - The orthonormal basis is very useful in coordinate systems. The orthogonality and unit vectors make calculations easier compared to a regular basis coordinate system. 
    - When this basis is stacked into a matrix representation we get an Identity matrix(I). For which the inverse is equal to the transpose of the matrix. 

## Vector Product (Cross Product)
- A vetor product's output is another vector. This new vecotr is perpendicular to the two vectors.
- The vector product between two vectors (u,v) can be given by ($\vec{u} \times \vec{u}$):
    1. $\vec{u} \times \vec{u}$ is orthogonal to both $\vec{u}$,$\vec{v}$,
    2. $\|\|\vec{u}\times\vec{v} = \|\|\vec{u}\|\| \cdot \|\|\vec{v}\|\| \cdot \sin[u,v]$ (**Magnitude**)
    3. All the three vectors are positively oriented

- Orientation:
    - The term orientation means how the vectors u,v are oriented. Most common way of representing the orientation is the right hand rule.
    - With the right hand, point to the vectors with thumb and index finger, the middle finger gives the direction of the resulting vector
    - In a 3D space the two vectors u ,v represent the two normals of a 2D plane then:
        - $u \times v = w$ the vector w is pointing upward
        - $v \times u = -w$ the vector w is pointing downward
- Magnitude:
    - The magnitude of the resulting vector is dependent on the sin of the angle between the two vectors.
    - From this we can say that when the an angle [u,v] becomes 0. then $u \times v$ also becomes 0.
- For calculating the cross product between u,v in orthonormal basis:
    - We can use Determinant Form or sarrus rule:
    - $$\vec{u} = \begin{bmatrix} u_x \\\ u_y \\\ u_z\end{bmatrix}$$, $$\vec{v} = \begin{bmatrix} v_x \\\ v_y \\\ v_z\end{bmatrix}$$
    - $$\vec{u} \times \vec{v} = 
        \begin{vmatrix}
        \vec{i} & \vec{j} & \vec{k} \\
        u_1 & u_2 & u_3 \\
        v_1 & v_2 & v_3
        \end{vmatrix}$$ (determinant form)
    - $$\mathbf{u} \times \mathbf{v} = 
        \mathbf{i} \begin{vmatrix} u_2 & u_3 \\ v_2 & v_3 \end{vmatrix} -
        \mathbf{j} \begin{vmatrix} u_1 & u_3 \\ v_1 & v_3 \end{vmatrix} +
        \mathbf{k} \begin{vmatrix} u_1 & u_2 \\ v_1 & v_2 \end{vmatrix}$$ 
    