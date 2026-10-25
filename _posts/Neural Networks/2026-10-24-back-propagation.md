---
layout: post
title: "Back propagation and Neural Networks"
author: "Jayadeep"
categories: [DL, NN]
tags: [documentation,back propagation]
image:
---

I am working on documenting my [pytorch skills](https://github.com/Jayadeep19/pytorch){:target="_blank"} from basics to some small projects ultimately working with transformers and LLM's. In the process, an important functionality of pytorch is **Autograd**. In order to explain this functionality we have to discuss how neural networks learn. So, I thought it would be a nice idea to write a blog on how Back Propagation works in Neural Networks

Todays topics:
* TOC
{:toc}

## What is a Neural Network?
- Neural Networks are inspired from how our brains work. When we see a number written on a piece of paper, we instantly know that it is certain number , How?
- Our brains are trained from a small age, how to recognise number. In the same way a neural network, trains on some data and learns how to solve variety of tasks.
- what is a neuron and what is it learning?
    - Neuron can be thought of as something that holds a value between 0 and 1. 
    - Example, when working with handwritten digits, each input images pixel is connected to a neuron and the pixels gray scale values is the neurons **'activation'**.
- These neurons are structured in a layers format and each neuron in a layer is connected to every other neuron in other layers.
- The connections between different neurons are regulated by a number called **weights**. These weights determine how strong the connection between the neurons is. This is called a Neural Network
- But why does the structured pattern work? How does it learn to identify an array of pixels?
    - The structured pattern works because, the layers in the Neural Network learn to observe features and patterns in the inputs that we provide.
    - Example, when we see '9' written on a paper, we see that there is a 'o' and a straight line '\|' beside it. The network learns to extract these features and learn the patterns in the inputs.
- In a perfect world, when the input is sent to the network, the neurons that are responsible for the particular features of the input fire together and the model outputs teh correct number. But this is extremely difficult task to achieve.

### Multi layer perceptron:
- A multi layer perceptron has multiple neurons and multiple layers in its structure.
- when we take a 28*28 pixel image, we would have a total of 784 pixels. We pass these 784 pixels to 784 neurons in the input layer.
- There can be arbitrary number of neurons and layers inside the structure and 10 neuros in the output layer since we need to determine the input as one of the number from 0-9.
- The input of the neurons is a **weighted sum** of all the neurons in the layer before it.
    - $w_1a_1 + w_2a_1 + .... + w_na_n$
    - where w_1 is the weight of the first neuron to the current neuron 
    - a_1 is the activation of the first neuron.
- when there are multiple neurons we don't want the values to explode so, we apply a function to make the values fall between 0 and 1, we call this as squashing function. Ex: sigmoid, this function can map any input to a number between 0 and 1.
    - $sigmoid (\sigma(z)) = \frac{1}{1+e^{-z}} $
    - These functions also serve another important functionality, introducing non linearity. Helping the model to learn complex non linear patterns in the input.
- Along with these, a neuron also has something called a **bias**. It acts like a threshold that determines how strong the signal has to be for this particular neuron to be fired.
- So our final __weighted sum__ becomes $\sigma(w_1a_1 + w_2a_1 + .... + w_na_n+b)$
- This is for one neuron in one layer, but we have multiple neurons with multiple layers. we can already see it is going to be messy calculations ahead. Fortunately calculus offers us a clean and nice way to calculate these computations. **Matrices**
- Matrices are loved by GPU's since they can be parallelly computed so the modern GPU's are very fast in calculating these.
- we denote **$a_0^{(1)}$** as the activation of the $1^{st}$ neuron in the $1^{st}$ layer. as shown in the Image
- ![back_prop1]({{"/assets/img/NN/back_prop1.png" | relative_url}})
- As shown in the image, the weights and activations of the layer can be neatly placed inside a matrix nd perform matrix multiplication.
- The first row of the weights matrix gives the connection of the 1st neuron in the 1st hidden layer to all the neurons in the input layer. and so on..
- The equation for this layer becomes $a^{(1)} = \sigma(Wa^{(0)} + b)$. vector 'b' is not shown in the image but we add the vector b and apply sigmoid over the whole equation.

- OK, but how does the model learn?

---

## Gradient Descent:
- Now Imagine we are the model, as we are now, we will just randomly guess the output for a given input. 
- Now, we want to train ourself to predict the correct value for that we need to train ourself with huge amounts of data. we get better bit by bit until we predict correct values reliably.
- For this to achieve, the model needs a function that it can look at and decide how bad or good the predictions it is making. It is called as **cost function**.
- A cost function could be just the squared difference between the predicted value and the actual value. $cost function = \|\| \hat{(y)} - y\|\|^2$
- Now how do we use this cost function to make the model learn. we can try to adjust the weights and biases that are responsible for the models prediction. These are called **models parameters**. 
- **By altering model parameters in a way that we reduce the cost function.**
- We have thousands of weights and biases how to effectively find the right set of combinations to mimimize the cost function? One answer is to sit down and try all the combinations manually (Horrifying).
- The other one is to use **gradient descent**
- Gradient meaning the difference between two points, and descent is climbing down. 
    > (Also see my other blog on [vector fields and gradients]({% post_url /math/2026-06-23-vector-calculus %}){:target="_blank"})
- as we have discussed, cost of a model is a function. Every function has minima for the given parameters. Our goal here is to find the minima of the cost function (reducing the error).
- Aka, we follow the direction of the greatest steep (greatest downward slope)
- Now we have a function and a goal (minima), how do we achieve it? from differentiation, we know that when we differentiate a function and equate it to zero to find the minima
    > check blog [geometric meaning]({% post_url /math/2026-06-20-calculus1 %}/#geometric-meaning){:target="_blank"}
- <div style="display: flex; gap: 10px;">
  <img src="{{'/assets/img/NN/gd1.PNG' | relative_url}}" alt="2D function" style="width: 50%;">
  <img src="{{'/assets/img/NN/gd2.PNG' | relative_url}}" alt="Higher dimension function" style="width: 50%;">
  </div>
- In the images above, the orange function is line and a 3D surface. The first function has two parameters in function and the second one has multiple parameters. Our models cost function has 1000's of parameters.
- we first start with random weights and compute our cost, then we find the gradients of this cost function us our gradient vector.
- This gradient vector for example the model has 13,000 parameters, our gradient vector also has 13k dimensions (elements in gradient vector).
- This gradient vector gives us the information to how much each weight and bias should change in order to reduce our cost.
- ![gd3]({{"/assets/img/NN/gd3.jpg" | relative_url}})
- The changes to the weights take place in small steps since we have other inputs to account for, and we define it with the parameter '$\eta$' called **'learning rate'**. This parameter is important as it controls how fast the model converges to the minima.
- If learning rate is too low, we might take very long to reach the minima and on the other hand, if the learning rate is too high we might miss the minima and bounce back and forth around the minima.
- so, the weight updates with : <mark>$η▿C$</mark>.
- All fine, but we have 1000's of parameters and 1000's of inputs. If we want to calculate the gradient for each input and update each parameter, it is going to take large computation resources and need more time. 
- One better way to compute the gradient vector is by using a technique called **'Back Propagation'**.

----

## Back Propagation

- Back propagation is the brain of the neural network. It is algorithm to find the gradients of the cost function efficiently.
- It uses something called a chain rule to calculate the gradients.
- Let us understand how we calculate the gradients using a simple example.
- ![bp2]({{"/assets/img/NN/bp_2.PNG" | relative_url}})
- Let us zoom in to our last two layers for understanding back propagation.
- For a <mark>single input instance</mark> the cost function we get is
- ![bp3]({{"/assets/img/NN/bp3.PNG" | relative_url}})
- If we want to see the chain rule in action for this two layers, we first have to draw the compute graph
- ![bp4]({{"/assets/img/NN/bp4.PNG" | relative_url}})
- As explained in the neural networks, the term $a^{(L-1)}$ is the activation of the neuron in the layer $L-1$.
- We will get this term from the similar graph drawn from its previous layer:
- ![bp5]({{"/assets/img/NN/bp5.PNG" | relative_url}})
- Coming to our last layer, we want to know that changes occur in the cost function when we tweak the parameters $(w^L, a^{L-1} and b^L)$
- $\frac{\partial{C_0}}{\partial{w^L}}$
- ![bp6]({{"/assets/img/NN/bp6.PNG" | relative_url}})
- Similarly we can find the partial derivatives for $[a^{(L-1)} and b^L]$
- But our simple NN has 4 layers, meaning we have to do this partial derivation for all the parameters for all layers. $(a^1 ...a^L), (b^1,....b^L)$. Now if we calculate all of them and put in a vector gives us the Gradient Vector .
- If we have multiple neurons in our layer then we just have to take the sum of the cost function for all the output layer neurons and calculate the gradient vector.
  
### Simplifying the gradient calculation:
- Now let us see how we can simplify the calculation of these gradients. Instead of calculating the finite differences (partial derivatives), we can simply calculate them by multiplying their activations.
 - Take an example: 3 layers and two neurons in each layer
- ![bp7]({{"/assets/img/NN/bp7.PNG" | relative_url}})
- ![bp8]({{"/assets/img/NN/bp8.PNG" | relative_url}})
- In the above equation, we can see that except the derivative of sigmoid, every other term is based on the activation of this layers and previous layers neurons.
- But the sigmoid can also be turned into activation multiplications:
- ![bp9]({{"/assets/img/NN/bp9.PNG" | relative_url}})
- We also have another neuron in the final layer so, we can find the partial derivatives of that neuron in a similar fashion.
- ![bp10]({{"/assets/img/NN/bp10.PNG" | relative_url}})
- Finally, we can calculate the gradients of that cost function wrt to the parameters of the neuron for that layer just by multiplying the activations with the $"\delta s"$.
- We can generalise these formulas as:
- ![bp11]({{"/assets/img/NN/bp11.PNG" | relative_url}})
- And as long as we have the current layers $"\delta"$ we can find the previous layers $"\delta"$ and propagate it back to the first layer.
- ![bp12]({{"/assets/img/NN/bp12.PNG" | relative_url}})
- The gradient vector is $"\triangledown C"$
- ![bp13]({{"/assets/img/NN/bp13.PNG" | relative_url}})


## Links
- [3blue1brown](https://www.3blue1brown.com/lessons/backpropagation/#what-is-backpropagation-really-doing){:target="_blank"}