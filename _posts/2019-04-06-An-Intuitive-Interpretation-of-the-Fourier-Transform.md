### Introduction

I despised the Fourier transform.

I didn’t understand why the mathematics worked the way they did. And I couldn’t fathom how anybody could have just sat down and wrote down the equations. To me, the synthesis and decomposition equations appeared almost magically. No physical interpretation. no explanation of why the mathematics should work the way they did.

Now, the Fourier transform is one of tools that I use most often. It took me a long time to gain a physical intuition for the Fourier transform, but now that I have it, I’ve decided that its too exciting not to share.

My objective with this blog post is not to belabor the mechanics of the Fourier transform or attempt to explain every intricate property of the Fourier transform. Rather, my objective is to show you a way of understanding the Fourier transform from a linear algebraic perspective. Once one gains an appreciation for this connection, many of the subtleties of the Fourier transform become almost obvious when observed through this lens (as it turns out, this blog might have the unintended consequence of elucidating certain subtleties of linear algebra as well).

A word of warning before we start: If you’ve never seen the Fourier transform before, this may not be the best place to start your journey. Fear not, however, you can find a good explanation of the (discrete) Fourier transform in my last blog post although I will probably repeat some of the points that I made there. I also recommend the following learning resources: Steve Smith’s (e)Book http://www.dspguide.com/ and Brian Douglas’ [youtube series](https://www.youtube.com/watch?v=1JnayXHhjlg). I also quite like the explanation given [here](https://betterexplained.com/articles/an-interactive-guide-to-the-fourier-transform/)



### The Punchline
As with my introduction of interferometry, I’m going to start with the punchline and then work backwards to understand what it means and why it’s important. So here it is: the Fourier transform is a (linear) transformation between the standard orthonormal basis and a second orthonormal basis (the Fourier basis).

Now that we know the punchline, let’s take some time to really understand what we mean by orthonormal bases and transformations. 

### Basis vectors

An implicit assumption of linear algebra is that each number in a vector has some physical meaning. For example, the three vector, (1,2,3), the first position might represent a one-unit step in the x-axis, the second position might represent the one-unit step in the y-axis, and the third position might represent the one-unit step in the z-axis. The numbers in the vectors then represent how much of each direction is needed to “build” the vector.

### Change of Basis

Note, however, the x, y, and z axes are not always the most convenient axes for expressing the vector. Consider the following vectors:

Figure 1: Four two-dimensional vectors in the “standard” basis. Notice that the red vector is aligned with the “standard” basis while the other three are not aligned with either axis.

When written numerically, the vectors take the form (counter-clockwise from red to black):

<img src="https://latex.codecogs.com/gif.latex?\large&space;\vec{r}&space;=&space;\begin{bmatrix}1&space;\\&space;0\end{bmatrix},&space;\vec{b}&space;=&space;\frac{1}{\sqrt{2}}\begin{bmatrix}1&space;\\&space;1\end{bmatrix},&space;\vec{g}&space;=&space;\frac{1}{\sqrt{2}}\begin{bmatrix}-1&space;\\&space;1\end{bmatrix},&space;\vec{k}&space;=&space;\frac{1}{\sqrt{2}}\begin{bmatrix}-2&space;\\&space;2\end{bmatrix}" title="\large \vec{r} = \begin{bmatrix}1 \\ 0\end{bmatrix}, \vec{b} = \frac{1}{\sqrt{2}}\begin{bmatrix}1 \\ 1\end{bmatrix}, \vec{g} = \frac{1}{\sqrt{2}}\begin{bmatrix}-1 \\ 1\end{bmatrix}, \vec{k} = \frac{1}{\sqrt{2}}\begin{bmatrix}-2 \\ 2\end{bmatrix}" />

The first thing to notice here is that the red vector’s representation appears simpler than the other vectors’ representations. Although some people may disagree, mathematicians generally prefer simplicity over complexity. When given the option between vectors with many zeros and only a few zeros, it’s preferable to pick the vector with many zeros. In linear algebra, this means that the vector is aligned with the underlying axes. To build the red vector, we simply take one step in the x-direction and zero steps in the y direction.

With this in mind, we might ask: “can we rotate these vectors such that we can align more of the vectors with the underlying axes (i.e. get more zeros)?”. Indeed, we can! If we rotate the above vectors clock-wise by 45 degrees, we find that three out of the four vectors have “simpler” representations:

When written out:

<img src="https://latex.codecogs.com/gif.latex?\large&space;\vec{r}_{new}&space;=&space;\frac{1}{\sqrt{2}}\begin{bmatrix}1&space;\\&space;-1\end{bmatrix},&space;\vec{b}_{new}&space;=&space;\begin{bmatrix}1&space;\\&space;0\end{bmatrix},&space;\vec{g}_{new}&space;=&space;\begin{bmatrix}0&space;\\&space;1\end{bmatrix},&space;\vec{k}_{new}&space;=&space;\begin{bmatrix}0&space;\\&space;2\end{bmatrix}" title="\large \vec{r}_{new} = \frac{1}{\sqrt{2}}\begin{bmatrix}1 \\ -1\end{bmatrix}, \vec{b}_{new} = \begin{bmatrix}1 \\ 0\end{bmatrix}, \vec{g}_{new} = \begin{bmatrix}0 \\ 1\end{bmatrix}, \vec{k}_{new} = \begin{bmatrix}0 \\ 2\end{bmatrix}" />

As you can see from the above graph, and the written representations, three of our four new vectors are aligned with the axes!

While you might say this operation seems obvious, this is actually an example of something more subtle: this rotating transformation is our very first example of a change of basis! 

Let’s breakdown how this change of basis happened. We started with some vectors in the original x, y basis (i.e. the standard basis) and applied some linear operation (Yes! Rotations are linear operations) to transform the vectors into a “new” basis. The next question you might ask is: what do these new basis vectors look like? As it turns out, I hid the new basis vectors in Figure 1: \vec{b} and \vec{g} are the basis vectors for this new rotated basis (You can also check the axes in figure 2)! To further convince yourself that this is true, consider the following pedagogical questions: How many copies of the green vector and how many copies of the blue  vector are needed to build the blue vector? Only one blue vector (and no multiples of the green vector), therefore the representation of the blue vector in the new basis is (1, 0). Similarly, how many multiples of the blue and green vectors are needed to build the black vector? We need 0 blue vectors, but 2 green vectors, therefore the representation of the black vector in the new basis is (2, 0).

One key observation (which we will discuss further in the subsequent sections), is that when the basis vectors for the new coordinate system are transformed from the “standard” coordinate system to the “new” coordinate system, their representations in the “new” coordinate system are always aligned with individual axes and will have unit length. This means that their representations in the new coordinate system will be of the form (1, 0, 0, …..) or (0, 1, 0, 0, ….) or (0, 0, 1, 0, ….) and so on. That is to say that they will be entirely zeros except for a single location where they have a one. (At the risk of spoiling a later punchline, the basis transformation matrix is the matrix which takes a matrix whose columns are the basis vectors in the standard basis and transforms them into the identity matrix.)

While this may not seem consequential, this observation is what ultimately allows us to build a change of basis matrix. That is, a matrix which takes any vector in the “standard” basis and returns the vector’s representation in the “new” basis.


### A (More) General Change of Basis

In our previous example, we understood, somewhat intuitively, that if we rotated our coordinate system by 45 degrees, the resulting vectors would have a simpler representation. While it is easy enough to guess the resulting vectors when the rotation is a commonly used angle (i.e. 30, 45, 60, 90), it’s less obvious when the rotation is 10 degrees or 1 radian. Let’s try to find an exact mathematical way of representing any rotation matrix.

Suppose we want to rotate the basis vectors clockwise by $\theta$ degrees. This means that instead of using our original basis vectors

<img src="https://latex.codecogs.com/gif.latex?\large&space;\hat{e_1}&space;=&space;\begin{bmatrix}&space;1&space;\\&space;0\end{bmatrix},&space;\hat{e_2}&space;=&space;\begin{bmatrix}&space;0&space;\\&space;1\end{bmatrix}" title="\large \hat{e_1} = \begin{bmatrix} 1 \\ 0\end{bmatrix}, \hat{e_2} = \begin{bmatrix} 0 \\ 1\end{bmatrix}" />

we want to find a way to express every possible vector as a combination of the new basis vectors:

<img src="https://latex.codecogs.com/gif.latex?\large&space;\hat{e_1}'&space;=&space;\begin{bmatrix}&space;\cos(\theta)&space;\\&space;-\sin(\theta)\end{bmatrix},&space;\hat{e_2}'&space;=&space;\begin{bmatrix}&space;\sin(\theta)&space;\\&space;\cos(\theta)\end{bmatrix}" title="\large \hat{e_1}' = \begin{bmatrix} \cos(\theta) \\ -\sin(\theta)\end{bmatrix}, \hat{e_2}' = \begin{bmatrix} \sin(\theta) \\ \cos(\theta)\end{bmatrix}" />

Visually, the new vectors are given by the blue vectors below:

The key question now is: how do we find the representation of any vector, \vec{z} = (x, y) ^T with the new vectors? Put another way, we need to find how much of each of the new basis vectors are needed to for \vec{z}. Mathematically, we need to solve the following equation for a and b:

<img src="https://latex.codecogs.com/gif.latex?\vec{z}&space;=&space;a&space;\begin{bmatrix}&space;\cos(\theta)&space;\\&space;-\sin(\theta)\end{bmatrix}&space;&plus;&space;b&space;\begin{bmatrix}&space;\sin(\theta)&space;\\&space;\cos(\theta)\end{bmatrix}" title="\vec{z} = a \begin{bmatrix} \cos(\theta) \\ -\sin(\theta)\end{bmatrix} + b \begin{bmatrix} \sin(\theta) \\ \cos(\theta)\end{bmatrix}" />

We can simplify the above expression if we combine the basis vectors, e1’ and e2’ into a single matrix. This results in the suggestive form:










