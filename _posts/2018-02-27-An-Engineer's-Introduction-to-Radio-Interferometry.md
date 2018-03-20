![Image of FT Pair](https://raw.githubusercontent.com/devincody/Blog/master/_images/FTwCap.png)

  As some of you may know, I’m currently a graduate student at the California Institute of Technology studying towards my Master of Science in Electrical Engineering. While I’m here, I’m working with Gregg Hallinan and Sandy Weinreb – two phenomenal instructors on a project called the Long Wavelength Array (LWA). The LWA is a 256-element interferometer in Owens Valley, CA which take radio images of the entire sky every 10 seconds. This project is particularly exciting for me because I’ve always been interested in how many distinct dishes can synthesize coherent images, sometimes with resolutions that cannot be achieved with single dish telescopes!

  Radio interferometry, often times comes off as a difficult concept, but it needn’t be that way. In this blog post, I will attempt to give a more intuitive introduction to radio interferometry. Some background in the Fourier transform will be helpful, but not strictly necessary. I will also assume some basic knowledge of astronomy. 
	
  I will start with the big punchline of radio interferometry and then work backwards to then justify everything else. So, are you ready for it? The big reveal? Ok, here it is: the most mind-blowing fact about radio astronomy is that instead of directly observing the sky, radio interferometers measure the Fourier transform of the sky [^1]. Slow down, don’t leave just yet, let’s take a minute to break this down. The Fourier transform is a method of taking data which is represented in one way (i.e. a series of measurements over time, or a series of pixels across a spatial grid) and representing it in a different, but equally-valid way. When you take the Fourier transform of some data, no information is lost, the data has simply been rearranged. Simultaneously, there exists a method by which we can take some data which has been Fourier transformed and undo the Fourier transform to recover the original data. This procedure is called the inverse Fourier transform. The important thing to take away from this, is that if we measure the Fourier transform of the sky, then we can reconstruct an map (image) of the sky brightness by taking the inverse Fourier transform of that data. But enough words, let’s look at some pictures.

  Fig. 2 shows two entirely equivalent, equally-valid representations of a galaxy. The image on the left is an image you might find in an Astronomy 101 textbook and the image on the right is simply the Fourier transform of the image on the left. However, it is just as true, to say that the image on the left is the inverse Fourier transform of the image on the right. 
	
  Radio interferometry, then, is a method of collecting information about the sky in the “Fourier” domain (i.e. information similar to that shown on the right in Fig. 2). Once we have collected this information, we can then use a computer to compute the inverse Fourier transform of the data to reconstruct a representation of the sky brightness. 
	
  But how, then, does an interferometer measure the Fourier transform of the sky? To answer this question, we will start by examining how the one-dimensional Fourier transform works. At its core, the one-dimensional (Discrite [^2]) Fourier transform states that any signal, that is, any sequence of N data points (e.g. measurements of position, a stock’s value over time, or pixel intensities in a 1-D image) with regular spacing can be represented by (decomposed into) the sum of N/2+1 sines and N/2+1 cosines. Strictly speaking, these are the properties of the discrete Fourier Transform (DFT) however, the Fourier Transform, which exists in the domain of continuous functions is equally valid here.

![Image of Decomposition Synthesis Relationship](https://raw.githubusercontent.com/devincody/Blog/master/_images/DecompSynthwCap.png)

![Image of Decomposition Synthesis Relationship](https://raw.githubusercontent.com/devincody/Blog/master/_images/FreqSpecwCap.png)

  Fig 3. (Reproduced from dspguide.com) shows an example of this relationship with a signal of length 16. On the left of Fig 3. we show a 16-point signal which is decomposed into the (16/2+1 =) 9 sine and 9 cosine waves shown on the right. Equivalently, we can say that the 18 signals on the right can be synthesized (summed) to form the signal on the left. These two representations are exactly equivalent in the information that they contain. As Steven Smith, author of “The Scientist and Engineer’s Guide to Digital Signal Processing”, points out, “There is no difference between the signal in (a) and the sum of the signals in (b), just as there is no difference between 7 and 3+4”.

  Without the Fourier transform, the decomposition shown in Fig 3. might seem impossible to compute, however, the magic of the Fourier transform is that it tells you precisely what sinusoid amplitudes are needed to completely decompose your signal. 
  
  The Fourier transform determines how much of each sinusoid is needed by using a technique called “correlation”. At its core, correlation is a method which allows us to determine how much “sameness” is contained in the two signals (of the same length). The Fourier Transform uses correlation to tell us what amplitudes are needed for the N+2 sinusoids in the decomposition. 
Mathematically, correlation is about as simple an operation as they come, and in-fact, you probably already know about it by its other name: the dot product. Simply multiply each element in the two sequences (signals) and add all the products together. If you are comfortable with linear algebra, correlation can also be thought of as the projection of one signal onto another. The Fourier transform finds the amplitudes of all of the necessary sines and cosines by finding the “sameness” between the input signal and test sinusoids -- signals with predetermined frequencies and unit amplitudes.

Let’s see what this looks like mathematically. The equation for correlation between two signals, x[n] and y[n] is the following:

<img align="center" src="https://latex.codecogs.com/gif.latex?\large&space;x\cdot&space;y&space;=&space;\sum_{n=1}^{N-1}x[n]y[n]" />

All this equation is doing is multiplying each term in the two sequences and adding up the result. The Fourier Transform is simply an extension of this equation. Lets look at the equation for the Fourier Transform.

<img src="https://latex.codecogs.com/gif.latex?\large&space;\mathfrak{Re}\{X[k]\}&space;=&space;\frac{2}{N}\sum_{n=1}^{N-1}x[n]\cos(2\pi&space;kn/N)" />

<img src="https://latex.codecogs.com/gif.latex?\large&space;\mathfrak{Im}\{X[k]\}&space;=&space;\frac{2}{N}\sum_{n=1}^{N-1}x[n]\sin(2\pi&space;kn/N)" />

These equations might look scary, but let’s work through them together. Here x[n] (note that there’s a difference between little x and big X) is the signal that we are analyzing, and y[n] (from the correlation equation) has been replaced by cos(2piki/N). This is simply our test sinusoid. The frequency of each test sinusoid is set by the parameter k. X[k] is simply the variable that will hold the amplitudes of our decomposed signal. Evidently, the real part of X will hold the amplitudes of the cosines and the imaginary part of X will hold the amplitudes of the sines. Using exact notation, X[k] is the representation of x[n] in the Fourier domain. Finally, the Fourier transform scales each of these correlations by 2/N to set the appropriate scale when things are added together again.

  Here we’ve discussed the Discrete Fourier transform (Discrete time and frequency), however the same analysis is valid for the continuous-time Fourier transform.

![Image of Fourier Correlation Example](https://raw.githubusercontent.com/devincody/Blog/master/_images/FourierCorrelationwCap.png)

  To see what this looks like, lets look at the figure above. Here we show two test sinusoids, one cosine and one sine. Additionally, the sinusoids have 2 different frequencies, one for k=1 and one for k = 2. First we correlate against the test sine wave, y_{k=1}[n], by multiplying all of the values in our sequence x[n] with all of the values in the test sinusoid. The result of this multiplication is shown in the bottom left figure. Afterwards, we sum all of the products together which gives us a single scalar value. This completes the correlation. Identically, the input signal can be correlated against a test cosine (such as the one on the left) by multiplying each value of x[n] by each value in y[n]. When we have done the above operation for a sine and cosine for each frequency, we will have successfully computed the Fourier transform of the signal.
  And that’s all there is to the one-dimensional Fourier transform! We now have the tool we need to calculate the amplitudes of the sinusoids which exactly decompose our original signal. To recap, we simply correlate our original signal with a toolbox of test sinusoids and then we can represent our signal as a sum of those same test sinusoids with a new amplitude. 
  For the two-dimensional Fourier transform, we do the exact same thing, except this time, our test sinusoids look like the one shown in Fig. 4 and we have to sum over two-dimensions one for the x direction and one for the y direction. For the two-dimensional Fourier transform, the “direction” of the sinusoid varies by 360 degrees, in Fig. 4 we show only two frequencies and two directions.

 Now, let’s return to radio astronomy and attempt to answer the question that spurred this adventure into the Fourier transform in the first place: how does an interferometer measure the Fourier transform of the sky? The answer, of course, is with test sinusoids! Supposing we had an “instrument” with an antenna pattern that looked like the sinusoid shown in Fig. 4, then by observing the sky with this instrument, the signal coming from this instrument would be proportional to the Fourier amplitude needed for the Fourier decomposition. Remember that an antenna pattern (also known as a beam pattern) tells us the directional dependence of the antenna’s sensitivity. It tells us how much power is picked up from every direction around the antenna. (see http://www.antenna-theory.com/basics/radpattern.php for review)

  Furthermore, if we had many instruments each with a different beam pattern, then we’d be able to calculate the Fourier amplitudes for all of these sinusoids and exactly determine how the sky is represented in the Fourier domain.
  
![Image of Decomposition Synthesis Relationship](https://raw.githubusercontent.com/devincody/Blog/master/_images/AntGeometrywCap.png)


  Finally, we will now turn to Fig. 1, which is commonly the first image shown during lectures on Interferometry. Fig. 1 is fundamentally a blue-print for the design of an instrument which has “beam pattern” that approximates a test sinusoid. Suppose we have two antennas, separated by some distance b looking at some object in the sky in the direction given by the unit vector, s. Assuming that the object in the direction of s is far enough away that the radiation from it can be assumed to be a plane wave, then time delay between when the radio telescope number 2 receives the signal and when the first telescope receives the signal is given by tau where tau is calculated by the equation 

<img src="https://latex.codecogs.com/gif.latex?\large&space;c\tau&space;=&space;\vec{b}&space;\cdot&space;\vec{s}&space;=&space;|\vec{b}|\cos(\theta)"/>

Thus, if antenna 2 produces some waveform due to the radiation from s, given by:

<img src="https://latex.codecogs.com/gif.latex?\large&space;v_2(t)&space;=&space;V\cos(\omega&space;t)"/>

Then antenna 1 produces some time-shifted version of the same waveform:

<img src="https://latex.codecogs.com/gif.latex?\large&space;v_1(t)&space;=&space;V\cos(\omega&space;(t-\tau))"/>

Finally, if we multiply these signals together and average them, we see that the resulting signal, R, is a function of tau:

<img src="https://latex.codecogs.com/gif.latex?\large&space;R&space;=&space;\frac{V^2}{2}\cos(\omega&space;\tau)"/>

Re-writing this in terms of theta, we find that:

<img src="https://latex.codecogs.com/gif.latex?\large&space;R&space;=&space;\frac{V^2}{2}\cos(\omega&space;|\vec{b}|\cos(\theta))"/>

![Image of Decomposition Synthesis Relationship](https://raw.githubusercontent.com/devincody/Blog/master/_images/FringePatternwCap.png)





[^1]: This can also be done in optical astronomy.
[^2]: Here we deal with the discrite fourier transform. This should not be confused with the discrite time Fourier transform, the continuous time Fourier transform, the laplace transform, or the z-transform.



