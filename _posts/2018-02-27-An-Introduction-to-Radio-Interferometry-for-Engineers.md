
  As some of you may know, I’m currently a graduate student at the California Institute of Technology studying towards my Master of Science in Electrical Engineering. While I’m here, I’m working with Gregg Hallinan and Sandy Weinreb – two phenomenal instructors on a project called the Long Wavelength Array (LWA). The LWA is a 256-element interferometer in Owens Valley, CA which takes radio images of the entire sky every 10 seconds. This project is particularly exciting for me because I’ve always been interested in how many distinct dishes can synthesize coherent images, sometimes with resolutions that cannot be achieved with single dish telescopes!

  Radio interferometry, often times comes off as a difficult concept, but it needn’t be that way. In this blog post, I will attempt to give a more intuitive introduction to radio interferometry. Some background in the Fourier transform will be helpful, but not strictly necessary. I will also assume some basic knowledge of astronomy. 
	
### Starting with the Punchline
	
  In this blog post, I will start with the big punchline of radio interferometry and then work backwards to then justify everything else. So, are you ready for it? The big reveal? Ok, here it is: the most mind-blowing fact about radio astronomy is that instead of directly observing the sky, radio interferometers measure the Fourier transform of the sky[^1]. Slow down, don’t leave just yet, let’s take a minute to break this down. The Fourier transform is a method of taking a sequence of data which is represented in one way (i.e. a series of measurements over time, a series of pixels across a spatial grid, or a signal coming from a sensor) and representing it in a different, but equally-valid way. When you take the Fourier transform of some data, no information is lost. The data has simply been rearranged. Simultaneously, there exists a method by which we can take some data which has been Fourier transformed and undo the Fourier transform to recover the original data. This procedure is called the inverse Fourier transform. The important thing to take away from this, is that if we measure the Fourier transform of the sky, then we can reconstruct an map (image) of the sky brightness by taking the inverse Fourier transform of that data. But enough words, let’s look at some pictures.

![Image of FT Pair](https://raw.githubusercontent.com/devincody/Blog/master/_images/FTwCap.png)

  Fig. 1 shows two entirely equivalent, equally-valid representations of a galaxy. The image on the left is an image you might find in an Astronomy 101 textbook and the image on the right is simply the Fourier transform of the image on the left. However, it is just as true to say that the image on the left is the inverse Fourier transform of the image on the right. 
	
  Radio interferometry, then, is a method of collecting information about the sky in the “Fourier” domain (i.e. information similar to that shown on the right in Fig. 1). Once we have collected this information, we can then use a computer to compute the inverse Fourier transform of the data and to reconstruct a picture of the sky. 
	
  But how, then, does an interferometer measure the Fourier transform of the sky? To answer this question, we will start by examining how the one-dimensional Fourier transform works. 
  
### The (Discrite) Fourier Transform
  
  At its core, the one-dimensional (Discrite[^2]) Fourier transform states that any signal, that is, any sequence of N data points (e.g. measurements of position, a stock’s value over time, or pixel intensities in a 1-D image) with regular spacing can be represented by (decomposed into) a sum of N/2+1 sines and N/2+1 cosines. 
![Decomposition Synthesis Relationship](https://raw.githubusercontent.com/devincody/Blog/master/_images/DecompSynthwCap.png)

![Frequency Spectrum](https://raw.githubusercontent.com/devincody/Blog/master/_images/FreqSpecwCap.png)

  Fig 2. (inspired by a similar figure from dspguide.com) shows an example of this relationship with a signal[^3] of length 16. On the left side of Fig 2. we show a 16-point signal which is decomposed into (16/2+1 =) 9 sine and 9 cosine waves as shown on the right. Equivalently, we can say that the 18 signals on the right can be synthesized (summed) to form the signal on the left. These two representations are *exactly* equivalent in the information that they contain. As Steven Smith, author of “The Scientist and Engineer’s Guide to Digital Signal Processing”, points out, “There is no difference between the [original signal] and the sum of the signals in [the decomposition], just as there is no difference between 7 and 3+4”.

  Often times, it's more useful to represent the information about the fourier decomposition as a frequency spectrum. Fig. 3 shows the frequency spectrum of the signal in Fig. 2. In this plot, the amplitudes of the sinusoids are plotted as functions of their frequencies. Notice that all the important information is contained within this plot. As you will remember from trigonometry, a sinusoid is completely determined by it's frequency (X-axis), amplitude (Y-axis), and phase (sine corresponds to the real part, while cosine corresponds to the imaginary part). The actual values which comprise each sinusoid are omitted since they can be determined from the frequency spectrum. Typically, when someone asks for the Fourier Transform of a signal, this is the information they are looking for.

  #The Fourier transform describes precisely what sinusoid amplitudes are needed to completely decompose the signal. 
  
  The Fourier transform determines how much of each sinusoid is needed by using a technique called “correlation”. At its core, correlation is a method which allows us to determine how much “sameness” is contained in the two signals.
  
Mathematically, correlation is about as simple an operation as they come, and in-fact, you probably already know about it by its other name: the dot product. Simply multiply each element in the two sequences (signals) and add all the products together. If you are comfortable with linear algebra, correlation can also be thought of as the projection of one signal onto the other. The Fourier transform finds the amplitudes of all of the necessary sines and cosines by finding the “sameness” between the input signal and test sinusoids -- signals with predetermined frequencies and unit amplitudes.

Let’s see what this looks like mathematically. The equation for correlation between two signals, x[n] and y[n] is shown here

<img align="center" src="https://latex.codecogs.com/gif.latex?\large&space;x\cdot&space;y&space;=&space;\sum_{n=1}^{N-1}x[n]y[n]." />

All this equation is doing is multiplying each term in the two sequences and adding up the result. The Fourier Transform is simply an extension of this equation. Lets look at the equation for the Fourier transform

<img src="https://latex.codecogs.com/gif.latex?\large&space;\mathfrak{Re}\{X[k]\}&space;=&space;\frac{2}{N}\sum_{n=1}^{N-1}x[n]\cos(2\pi&space;kn/N)" />

<img src="https://latex.codecogs.com/gif.latex?\large&space;\mathfrak{Im}\{X[k]\}&space;=&space;\frac{2}{N}\sum_{n=1}^{N-1}x[n]\sin(2\pi&space;kn/N)" /> [^4].

These equations might look scary, but let’s work through them together. Here x[n] (note that little x and big X are different even though the represent the same signal) is the signal that we are analyzing, and y[n] has been replaced by cos(2pikn/N). This is simply our test sinusoid. The frequency of each test sinusoid is set by the parameter k. X[k] is the variable that will hold the amplitudes of our decomposed signal. Evidently, the real part of X will hold the amplitudes of the cosines and the imaginary part of X will hold the amplitudes of the sines. Using exact nomenclature, X[k] is the representation of x[n] in the Fourier domain. Finally, the Fourier transform scales each of these correlations by 2/N to set the appropriate scale when things are added together again. In a similar manner, given X[k], we can synthesize x[n] using the inverse Fourier transform

<img src="https://latex.codecogs.com/gif.latex?\large&space;x[n]&space;=&space;\sum_{k=0}^{N/2}\mathfrak{Re}\{X[k]\}\cos(2\pi&space;kn/N) + \mathfrak{Im}\{X[k]\}\sin(2\pi&space;kn/N)." />

Here, we are using the amplitudes stored in X[k] to scale the 18 sinusoids and sum them together. 

Let's now take a deeper look at the mechanics of correlation as used in the Fourier transform. Fig. 4 shows what this might look like graphically.

![Image of Fourier Correlation Example](https://raw.githubusercontent.com/devincody/Blog/master/_images/FourierCorrelationwCap.png)

   Fig. 4 shows the correlation of one input signal shown on the top in blue (x[n]) with two different test sinusoids shown in the middle in green. The test sinusoid on the left is a sine wave with frequency k = 1 and amplitude 1 while the one on the right is a cosine with frequency k = 2 and amplitude 1. As described in the correlation equation, we simply multiply all of the values in our sequence x[n] with all of the values in the test sinusoid. The result of this multiplication is shown on the bottom for both of these test sinusoids. To complete the correlation, we sum all of the products together which gives us a single scalar value. Once we have done the above operation for a sine and cosine at each frequency, we will have all the information to write down the Fourier transform of the signal.
   
### Correlation on the Sky: Putting it All Together

  Now that we've gotten a better understanding of the Fourier transform, We now have the tool we need to calculate the amplitudes of the sinusoids which exactly decompose our original signal. To recap, we simply correlate our original signal with a toolbox of test sinusoids (one sine and one cosine at each of the N/2 frequencies) and then we can represent our signal as a sum of those same test sinusoids with a new amplitude. 
  
  For the two-dimensional Fourier transform, we do the exact same thing, except this time, our test sinusoids look like the one shown in Fig. 5 and we have to sum over two-dimensions (one for the x direction and one for the y direction). For the two-dimensional Fourier transform, the “direction” of the sinusoid (i.e. direction of maximal variation) varies by 360 degrees, in Fig. 4 we show two frequencies and two directions.

 Now, let’s return to radio astronomy and attempt to answer the question that spurred this adventure into the Fourier transform in the first place: how does an interferometer measure the Fourier transform of the sky? The answer, of course, is with test sinusoids! Supposing we had an “instrument” with an antenna pattern that looked like the sinusoid shown in Fig. 4, then by observing the sky with this instrument, the signal coming from this instrument would be proportional to the Fourier amplitude needed for the Fourier decomposition. Remember that an antenna pattern (also known as a beam pattern) tells us the directional dependence of the antenna’s sensitivity. It tells us how much power is picked up from every direction around the antenna. (see http://www.antenna-theory.com/basics/radpattern.php for review)

![Two-dimensional Test Sinusoids](https://raw.githubusercontent.com/devincody/Blog/master/_images/TwoTestSinusoidswCap.png)


  Furthermore, if we had many instruments each with a different beam pattern, then we’d be able to calculate the Fourier amplitudes for all of these sinusoids and exactly determine how the sky is represented in the Fourier domain.
  
![Geometric origin of fringe pattern](https://raw.githubusercontent.com/devincody/Blog/master/_images/AntGeometrywCap.png)

### The Fringe Pattern

  Finally, we will now turn to Fig. 6, which is commonly the first image shown during lectures on Interferometry. Fig. 6 is fundamentally a blue-print for the design of an instrument which has “beam pattern” that approximates a test sinusoid. 
  
  The reasoning goes as follows: suppose we have two antennas, separated by some distance b looking at some object in the sky in the direction given by the unit vector, s. Assuming that the object in the direction of s is far enough away that the radiation from it can be assumed to be a plane wave, then the time delay between when the radio telescope number 2 receives the signal and when the first telescope receives the signal is given by tau_g where tau_g is calculated by the equation 

<img src="https://latex.codecogs.com/gif.latex?\large&space;c\tau_g&space;=&space;\vec{b}&space;\cdot&space;\vec{s}&space;=&space;|\vec{b}|\cos(\theta)"/>

Thus, if antenna 2 produces some waveform due to the radiation from s, given by:

<img src="https://latex.codecogs.com/gif.latex?\large&space;v_2(t)&space;=&space;V\cos(\omega&space;t)"/>

Then antenna 1 produces some time-shifted version of the same waveform:

<img src="https://latex.codecogs.com/gif.latex?\large&space;v_1(t)&space;=&space;V\cos(\omega&space;(t-\tau_g))"/>

Finally, if we multiply these signals together and average them (such that the high frequency components are removed), we see that the resulting signal, R, is a function of tau_g:

<img src="https://latex.codecogs.com/gif.latex?\large&space;R&space;=&space;\frac{V^2}{2}\cos(\omega&space;\tau_g)"/>

Re-writing this in terms of theta, we find that:

<img src="https://latex.codecogs.com/gif.latex?\large&space;R&space;=&space;\frac{V^2}{2}\cos(\omega&space;|\vec{b}|\cos(\theta)/c)"/>

  This, at long-last, is our test sinusoid (traditionally, this is known as a fringe pattern). As we sweep theta from one side of the sky to the other, the magnitude of the signal will vary in a sinusoidal manner. The instrument is most sensitive in the directions of the sinusoidal maxima and has “negative” sensitivity at the locations of the dips. Put another way, this instrument has a sinusoidal beam pattern. Now, while this pattern is not perfectly sinusoidal, with some clever manipulations, it can be made to work perfectly well for radio interferometry.
 

  But, wait, you might say, this doesn’t look like the two-dimensional pattern shown previously! And you’d be right, they do not look the same. However, in our analysis of Fig. 6, we assumed that the direction of the source, s, was directly overhead of the two antennas. This need not be the case. If we are careful about our analysis, we will see that if the source is not directly overhead, but rotated out of the plane (defined by the antennas and the original source position) by some angle, phi, we will still obtain the same time delay, tau, and therefore have the same fringe pattern for any phi.
  
  
  Lastly, you may wonder how we can generate all the other test sinusoides. Notice that the spatial frequency (that is, how rapidly the signal peaks and dips as a function of s) of our test sinusoid, is dependent on b, the length and direction of the baseline between the antennas. Therefore, although any given pair of antennas is only able to produce one baseline (assuming they don’t move), we can add additional antennas to produce additional baselines and more test sinusoids!

  This completes my (hopefully) intuitive approach to interferometry. I hope you enjoy reading this as much as I enjoyed writing it. To maximize simplicity, I inevitably had to cut out many interesting and exciting (some might even say critical) aspects of interferometry. However, those details are recorded in greater depth and eloquence than I could ever hope to achieve in such texts as “Interferometry and Synthesis” by Moran and “Essential Radio Astronomy” by James Justin Condon and Scott M. Ransom. I would also highly recommend “The Scientist and Engineer’s Guide to Digital Signal Processing” by Steven Smith, a book that was highly influential on my approach to this subject. 


![Image of Decomposition Synthesis Relationship](https://raw.githubusercontent.com/devincody/Blog/master/_images/FringePatternwCap.png)





[^1]: This can also be done in optical astronomy.
[^2]: Strictly speaking, these are the properties of the discrete Fourier Transform (DFT) however, the Fourier Transform, which exists in the domain of continuous functions can be though of as a generalization of these ideas.
[^3]: Because my background is in Electrical Engineering, I will often talk about "signals", "sequences", or "series", which can be defined as any sequence of datapoints in the "time" domain. Conversely, I will say "frequency spectrum" when talking about a set of datapoints which represent information in the "frequency" domain.
[^4]: Most equations for the discrite fourier transform include the factor of 2/N as part of the inverse fourier transform rather than the forward transform.



