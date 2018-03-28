Hi and welcome to my blog! As this is my first post, I thought I’d give a quick introduction of myself before we delve into my introduction of interferometry. My name is Devin and I’m currently a graduate student at Caltech studying towards a Master’s degree in Electrical Engineering. My primary interest is instrumentation. My passion is building hardware systems that allow us to better understand the world around us (e.g. radio telescopes, quantum circuits, sounding rockets). However, a lot of the work that I’ve done has been bringing these types of systems to life through software development, so I consider myself a bit of a mixed bag of skills and expertise.  If you’re interested in learning more about what I do or how I do it, please check out my website [here](www.devincody.com).

Currently at Caltech, I’m working with Gregg Hallinan and Sandy Weinreb on a project called the Long Wavelength Array (LWA). The LWA is a 256-element interferometer in Owens Valley, CA which takes radio images of the entire sky every 10 seconds. This project is particularly exciting for me because I’ve always been interested in how many distinct dishes can synthesize coherent images, sometimes with resolutions that cannot be achieved with single dish telescopes!

Radio interferometry often times comes off as a difficult concept, but it doesn’t need to be that way. In this blog post, I will attempt to give a more intuitive introduction to radio interferometry, one that, in particular, might help a budding radio engineer learn more about this fascinating field. I will assume knowledge of calculus, but everything else will be developed from the ground up. 

	
### Starting with the Punchline
	
  In this blog post, I will start with the big punchline of radio interferometry and then work backwards to then justify everything else. So, are you ready for it? The big reveal? Ok, here it is: the most mind-blowing fact about radio astronomy is that instead of directly observing the sky, radio interferometers measure the Fourier transform of the sky. Slow down, don’t leave just yet, let’s take a minute to break this down. The Fourier transform is a method of taking a sequence of data which is represented in one way (i.e. a series of measurements over time, a signal coming from a sensor, or series of pixels across a spatial grid[^1]) and representing it in a different, but equally valid way. When you take the Fourier transform of some data, no information is lost. The data has simply been rearranged. Simultaneously, there exists a method by which we can take some data which has been Fourier transformed and undo the Fourier transform to recover the original data. This procedure is called the inverse Fourier transform. The important thing to take away from this, is that if we measure the Fourier transform of the sky, then we can reconstruct an map (image) of the sky brightness by taking the inverse Fourier transform of that data. But enough words, let’s look at some pictures.

![Image of FT Pair](https://raw.githubusercontent.com/devincody/Blog/master/_images/FTwCap.png)

  Fig. 1 shows two entirely equivalent, equally-valid representations of a galaxy. The image on the left is an image you might find in an Astronomy 101 textbook and the image on the right is simply the Fourier transform of the image on the left[^9]. It is just as true, however, to say that the image on the left is the inverse Fourier transform of the image on the right. 
	
  Radio interferometry, is a method of collecting information about the sky in the “Fourier” domain (i.e. information similar to that shown on the right in Fig. 1). Once we have collected this information, we can then use a computer to compute the inverse Fourier transform of the data and to reconstruct a picture of the sky. 
	
  But how does an interferometer measure the Fourier transform of the sky? To answer this question, we will start by examining how the one-dimensional Fourier transform works. 
  
### The (Discrete) Fourier Transform
  
  At its core, the one-dimensional (Discrete[^2]) Fourier transform states that any signal, that is, any sequence of N data points (e.g. measurements of position, a stock’s value over time, or pixel intensities in a one-dimensional image) with regular spacing can be represented by (decomposed into) a sum of N/2+1 sines and N/2+1 cosines. 
![Decomposition Synthesis Relationship](https://raw.githubusercontent.com/devincody/Blog/master/_images/DecompSynthwCap.png)

  Fig 2. shows an example of this relationship with a signal[^3] of length 16. On the left side of Fig 2. we show a 16-point signal which is decomposed into (16/2+1 =) 9 sine and 9 cosine waves as shown on the right. Equivalently, we can say that the 18 signals on the right can be synthesized (summed) to form the signal on the left. These two representations are *exactly* equivalent in the information that they contain. As Steven Smith, author of “The Scientist and Engineer’s Guide to Digital Signal Processing”, points out, “There is no difference between the [original signal] and the sum of the signals in [the decomposition], just as there is no difference between 7 and 3+4”.

![Frequency Spectrum](https://raw.githubusercontent.com/devincody/Blog/master/_images/FreqSpecwCap.png)

  Often times, it's more useful to represent the information about the Fourier decomposition as a frequency spectrum. Fig. 3 shows the frequency spectrum of the signal in Fig. 2. In this plot, the amplitudes of the sinusoids are plotted as functions of their frequencies. Notice that all the important information is contained within this plot. As you will remember from trigonometry, a sinusoid is completely determined by its frequency (X-axis), amplitude (Y-axis), and phase (sine corresponds to the real part, while cosine corresponds to the imaginary part). The actual values which comprise each sinusoid are omitted since they can be uniquely determined from the amplitude, phase, and frequency. Typically, when someone asks for the Fourier Transform of a signal, this is the information they are looking for.
  
  The Fourier transform determines how much of each sinusoid is needed by using a technique called **correlation**. At its core, correlation is a method which allows us to determine how much “sameness” is contained in the two signals.
  
Mathematically, correlation is about as *fundamental* an operation as they come, and in-fact, you might already know about it by its other name: the dot product. Simply multiply each element in the two sequences (signals) and add all the products together. Correlation can also be thought of as the projection of one signal onto the other. The Fourier transform finds the amplitudes of all of the necessary sines and cosines by finding the “sameness” between the input signal and test sinusoids -- signals with predetermined frequencies and unit amplitudes.

Let’s see what this looks like mathematically. The equation for correlation between two signals, ![xn] and ![yn] is shown here

<img align="center" src="https://latex.codecogs.com/gif.latex?\large&space;x\cdot&space;y&space;=&space;\sum_{n=1}^{N-1}x[n]y[n]." />

All this equation is doing is multiplying each term in the two sequences and adding up the result. The Fourier Transform is simply an extension of this equation. Lets look at the equation for the Fourier transform[^4]

<img src="https://latex.codecogs.com/gif.latex?\large&space;\mathfrak{Re}\{X[k]\}&space;=&space;\frac{2}{N}\sum_{n=1}^{N-1}x[n]\cos(2\pi&space;kn/N)" />

<img src="https://latex.codecogs.com/gif.latex?\large&space;\mathfrak{Im}\{X[k]\}&space;=&space;\frac{2}{N}\sum_{n=1}^{N-1}x[n]\sin(2\pi&space;kn/N)" />

where

![kinZ]

These equations might look scary, but let’s work through them together. Here ![xn] (note that little x and big X are different even though they represent the same signal) is the signal that we are analyzing, and ![yn] has been replaced by <img src="https://latex.codecogs.com/gif.latex?\fn_phv&space;\cos(2\pi&space;kn/N)" title="\cos(2\pi kn/N)" />. This is simply our test sinusoid. The frequency of each test sinusoid is set by the parameter k and the third equation (![kinZ]) tells us that it takes integer values between 0 to N/2 inclusive. ![Xk] is the variable that will hold the amplitudes of our decomposed signal. Evidently, the real part of X will hold the amplitudes of the cosines and the imaginary part of X will hold the amplitudes of the sines. Using exact nomenclature, ![Xk] is the representation of ![xn] in the Fourier domain. Finally, the Fourier transform scales each of these correlations by 2/N to set the appropriate scale when things are added together again. In a similar manner, given ![Xk], we can use the inverse Fourier transform to synthesize x[n]

<img src="https://latex.codecogs.com/gif.latex?\large&space;x[n]&space;=&space;\sum_{k=0}^{N/2}\mathfrak{Re}\{X[k]\}\cos(2\pi&space;kn/N) + \mathfrak{Im}\{X[k]\}\sin(2\pi&space;kn/N)." />

Here, we are using the amplitudes stored in ![Xk] to scale the 18 sinusoids and sum them together. Notice that the two terms in this equation are exactly the data points shown in the decomposed version of Fig. 2.

Let's now take a deeper look at the mechanics of correlation as used in the Fourier transform. Fig. 4 shows what this might look like graphically.

![Image of Fourier Correlation Example](https://raw.githubusercontent.com/devincody/Blog/master/_images/FourierCorrelationwCap.png)

   Fig. 4 shows the correlation of one input signal with two different test sinusoids. The test sinusoid on the left is a sine wave with frequency k = 1 and amplitude 1 while the one on the right is a cosine with frequency k = 2 and amplitude 1. As described in the correlation equation, we multiply all of the values in our sequence ![xn] with all of the values in the test sinusoid. The result of this multiplication is shown on the bottom for both of these test sinusoids. To complete the correlation, we sum all of the products together which gives us a single scalar value. Once we have done the above operation for a sine and cosine at each frequency, we will have all the information to write down the Fourier transform of the signal.
   
### Correlation on the Sky: Putting it All Together

  Now that we've gotten a better understanding of the Fourier transform, We now have the tool we need to calculate the amplitudes of the sinusoids which exactly decompose our original signal. To recap, we simply correlate our original signal with a toolbox of test sinusoids (one sine and one cosine at each of the N/2+1 frequencies since <img src="https://latex.codecogs.com/gif.latex?\fn_phv&space;k&space;\in&space;\mathbb{Z}&space;\cap&space;[0,&space;N/2&space;]" title="k \in \mathbb{Z} \cap [0, N/2 ]" /> and then we can represent our signal as a sum of those same test sinusoids with a new amplitude. 
  
  For the two-dimensional Fourier transform, we do the exact same thing, except this time, our test sinusoids look like the one shown in Fig. 5. Furthermore, we have to sum over two-dimensions (one for the x direction and one for the y direction). For the two-dimensional Fourier transform, the “direction” of the sinusoid (that is, the direction of maximal variation) varies by 180 degrees, in Fig. 5 we show two possible frequencies and directions.

![Two-dimensional Test Sinusoids](https://raw.githubusercontent.com/devincody/Blog/master/_images/TwoTestSinusoidswCap.png)

 Now, let’s return to radio astronomy and attempt to answer the question that spurred this adventure into the Fourier transform in the first place: how does an interferometer measure the Fourier transform of the sky? The answer, of course, is with test sinusoids! Supposing we had an “instrument” with an antenna pattern that looked like the sinusoid shown in Fig. 5, then by observing the sky with this instrument, the signal coming from this instrument would be proportional to the Fourier amplitude needed for the Fourier decomposition. Remember that an antenna pattern (also known as a beam pattern) tells us the directional dependence of the antenna’s sensitivity. Therefore, within the context of the two-dimensional test sinusoids, the white portions of the image correspond to portions of the sky where the antenna is highly sensitive and the black portions denote areas where the antenna is *negatively* sensitive
 (see [here](http://www.antenna-theory.com/basics/radpattern.php "antenna-theory.com") or [here](https://www.cv.nrao.edu/~sransom/web/Ch3.html "NRAO Essential Radio Astronomy ch 3") for review).

Let’s examine what happens visually to images when they are multiplied by a beam pattern as part of a two-dimension correlation. Fig. 6 shows the ultimate result of this multiplication. When multiplied by a gaussian, the center of the image remains intact while the edges fade to black. When multiplied by the test sinusoid, a distinctive zebra pattern arises because the test sinusoid alternates between both positive and negative values. As with the one-dimensional case, we need to add all the values in these two images (matricies) to end up with one final scalar value.

![Modulated Images of Galaxy](https://raw.githubusercontent.com/devincody/Blog/master/_images/ModulatedBWGalaxwCap.png)

  Last, and most importantly, if we had many instruments each with a different beam pattern, then we’d be able to calculate the Fourier amplitudes for all of these sinusoids and exactly determine how the sky is represented in the Fourier domain[^10].

### The Fringe Pattern

![Geometric origin of fringe pattern](https://raw.githubusercontent.com/devincody/Blog/master/_images/AntGeometrywCap.png)

  Finally, we will now turn to Fig. 7, which is commonly the first image shown during lectures on Interferometry. Fig. 7 is fundamentally a blueprint for the design of an instrument which has *beam pattern* (also known as a radiation pattern) that approximates a test sinusoid.
  
  The derivation proceeds as follows: suppose we have two antennas, separated by some vector b looking at some object in the sky in the direction given by the unit vector, ![hats]. Assuming that the object (source) in the direction of ![hats] is far enough away that the radiation from it can be assumed to be a plane wave[^5], then the time delay between when the radio telescope number two receives the signal and when radio telescope number one receives the signal is given by ![taug] where ![taug] is calculated by the equation 

<img src="https://latex.codecogs.com/gif.latex?\large&space;c\tau_g&space;=&space;\vec{b}&space;\cdot&space;\vec{s}&space;=&space;|\vec{b}|\cos(\theta)"/>

Thus, if antenna 2 produces some waveform[^11] due to the radiation from ![hats], given by

<img src="https://latex.codecogs.com/gif.latex?\large&space;V_2(t)&space;=&space;V\cos(\omega&space;t)"/>

Then antenna 1 produces some time-delayed version of the same waveform

<img src="https://latex.codecogs.com/gif.latex?\large&space;V_1(t)&space;=&space;V\cos(\omega&space;(t-\tau_g))"/>

As per the diagram, we next multiply these signals together

<img src="https://latex.codecogs.com/gif.latex?\large&space;V_{12}(t)&space;=&space;V_1(t)V_2(t)&space;=&space;V^2\cos(\omega&space;t)\cos(\omega&space;(t-\tau_g))"/>

applying a handy trigonometric product identity

<img src="https://latex.codecogs.com/gif.latex?\large&space;V_{12}(t)&space;=&space;\frac{V^2}{2}\cos(\omega&space;\tau_g)&space;+&space;\cos(\omega&space;(2t-\tau_g))"/>

and finally average this signal over a long enough period of time such that the temporally varying portion of the signal, <img src="https://latex.codecogs.com/gif.latex?\fn_phv&space;\cos(\omega&space;(2t-\tau_g))"/>, averages to 0 and we are left the following signal, R, which is a function of ![taug]:

<img src="https://latex.codecogs.com/gif.latex?\large&space;R(\tau_g)&space;=&space;<V_{12}>&space;=&space;\frac{V^2}{2}\cos(\omega&space;\tau_g)"/>

Re-writing this in terms of theta, we find that:

<img src="https://latex.codecogs.com/gif.latex?\large&space;R(\theta)&space;=&space;\frac{V^2}{2}\cos(\omega&space;|\vec{b}|\cos(\theta)/c)"/>

  This, at long-last, is our test sinusoid (in proper radio astronomy nomenclature, this pattern is closely related to the interferometer fringe pattern[^7]). The instrument is most sensitive in the directions of the sinusoidal maxima and has “negative” sensitivity at the locations of the dips. Fig. 8 plots the above equation for R as we sweep theta from one side of the sky to the other (left). 
 
![Image of Decomposition Synthesis Relationship](https://raw.githubusercontent.com/devincody/Blog/master/_images/FringePatternwCap.png)
 
  But, wait, you might say, Fig. 8 doesn’t look like the two-dimensional pattern shown in Fig. 5! And you’d be right, they do not look the same; for starters, Fig. 8 is a one-dimensional pattern, and second, Fig. 8 is not a pure cosinusoidal pattern. The answer to the first observation is that the equation for the test sinusoid, R, actually *is* a 2-dimensional pattern, we simply choose to only plot R in one-dimension (i.e. in ![theta]). If we let ![phi] be the 2nd angle of a coordinate system where ![phi] is orthogonal to ![theta], then R does not depend on phi (notice how ![taug] only depends on ![theta]). Finally, if we plot R as a function of ![theta] and ![phi], then we will indeed find that R looks like a two-dimensional test sinusoid. 
  
  We will address the second observation using a bit of a sly trick. First, notice that the only difference between our equation for R and a pure cosine is that R is a function of ![costheta] and not ![theta]. If we were somehow able to removed the cosine, then we would have an ideal sinusoidal testing pattern. As it turns out, we can accomplish this with a transformation of variables: instead of considering R as a function of ![theta], we define new variables, u and v, given by the relations u = ![costheta] and v = ![cosphi]. Now our two spatial dimensions are not directly functions of ![theta] and ![phi], but in their cosines, u and v
  
<img src="https://latex.codecogs.com/gif.latex?\large&space;R(u,v)&space;=&space;\frac{V^2}{2}\cos(\omega&space;|\vec{b}|u/c)"/>
  
As if by magic, our test sinusoid is no longer distorted. Note that we restrict theta and phi to be 0 to +180 degrees (i.e. horizon to horizon) such that the mappings from ![theta] to ![costheta] and ![phi] to ![cosphi] is one-to-one. That is to say there is exactly one value of ![costheta] for every value of ![theta] and the same goes for ![phi]. Practially, what this means is that we are transforming the mapping of the sky to some coordinate system in which our test sinusoids are ideal, then once we've collected in the Fourier domain and transfered it back to the image domain, we can re-map this data to an actual map of the sky using the same u,v relations[^8].
  
  Lastly, you may wonder how we can generate all the other test sinusoides. Notice that the spatial frequency (i.e. how rapidly the signal peaks and dips as a function of ![hats]) of our test sinusoid is dependent on ![vecb][vecb], the length and direction[^6] of the baseline between the antennas. Therefore, although any given pair of antennas is only able to produce one baseline (assuming the antennas don’t move), we can add additional antennas to produce additional baselines and more test sinusoids. With a sufficient number of antennas, we will be able to complete our map of the sky in the Fourier domain, and then, using a computer, transform that data through the inverse Fourier transform to obtain the intensity sky mappings for which interferometry is so renowned.
  
### Outro
  This completes my (hopefully) intuitive approach to interferometry. I hope you enjoy reading this as much as I enjoyed writing it. To maximize simplicity, I inevitably had to cut out many interesting and exciting (some might even say critical) aspects of interferometry. However, those details are recorded in greater depth and eloquence than I could ever hope to achieve in such texts as “Interferometry and Synthesis in Radio Astronomy” by A. Richard Thompson, James M. Moran, and George W. Swenson, Jr. and “Essential Radio Astronomy” by James Justin Condon and Scott M. Ransom. I would also highly recommend “The Scientist and Engineer’s Guide to Digital Signal Processing” by Steven Smith, a book that was highly influential on my approach to the Fourier transform.

  Please feel free to connect with me about this post or anything else that I'm working on if you have comments! You can reach me at: devin.cody@gmail.com



### Notes

[^1]: This is a potential trap for people who have used the the Fourier transform previously. Often the Fourier transform is introduced as a method of studying *temporal* variations which is a vaild way of understanding the Fourier transform. However, in astronomy, we are more interested in studying the *spatial* variations of a signal. As alluded to earier, this might be the brightness of an array of pixels or the compression of a spring as a function of position.
[^2]: Strictly speaking, these are the properties of the discrete Fourier Transform (DFT) however, the Fourier Transform, which exists in the domain of continuous functions can be though of as a generalization of these ideas.
[^3]: Because my background is in Electrical Engineering, I will often talk about "signals", "sequences", or "series", which can be defined as any sequence of datapoints in the "time" domain. Conversely, I will say "frequency spectrum" when talking about a set of datapoints which represent information in the "frequency" domain.
[^4]: Most equations for the discrite fourier transform include the factor of 2/N as part of the inverse fourier transform rather than the forward transform.
[^5]: Plane waves are waves that have traveled far enough from their origin that they have negligible curvature. By way of example, consider a rock dropped into a still lake. If the rock is dropped close to the shore, then the ripples will hit two points on the shore line at different times (assuming the points are not equidistant from the rock). Conversely if the rock is dropped far from the shore, then the ripples will hit the two points at approximately the same time. At such a distance, the ripples (waves) are planar and are considered to be in the *far-field*. For electromagnetic waves, the same concept applies except in this case, our metaphorical lake is quite literally the size of the universe.
[^6]: The direction of the baseline additionally changes the "direction" of the test sinusoids. To see this, remember that \theta is defined in the plane defined by the baseline between the two antennas and a vector pointing towards the zenith. Therefore, we can change the orientation of this plane relative to some global coordinate system by chaning the baseline direction. This will ultimately give us all 360 degrees of test sinusoids that we need.
[^7]: In radio astronomy terms, fringe patterns are temporally varying power signals caused by radio sources transiting the previously discussed beam pattern.
[^8]:This concept is known as "direction cosines"
[^9]: For those of you following along at home, I've plotted the Fourier transform image in dB (logarithmic) to increase dynamic range.
[^10]: As it turns out, we actually do not need to measure all of the sinusoids, and in fact, it is physically impossible to measure all of them. Forming images without full information of the sky in the fourier (visibility) domain is covered in many books and papers. For example, see “Interferometry and Synthesis in Radio Astronomy” by A. Richard Thompson, James M. Moran, and George W. Swenson, Jr.
[^11]: Here we assume that the radiation from the source is monochromatic (i.e. consisting of a single frequency), however, the equations we derive are valid for all frequencies. Furthermore, from the superposition principle, we can analyze each frequency seperately then add all the results together at the end to find the full solution.


[kinZ]: https://latex.codecogs.com/gif.latex?\fn_phv&space;k&space;\in&space;\mathbb{Z}&space;\cap&space;[0,&space;N/2&space;] "k \in \mathbb{Z} \cap [0, N/2 ]"

[taug]: https://latex.codecogs.com/gif.latex?\tau_g "\tau_g"
[phi]: https://latex.codecogs.com/gif.latex?\phi "\phi"
[cosphi]: https://latex.codecogs.com/gif.latex?\cos(\phi) "\cos(\phi)"
[theta]: https://latex.codecogs.com/gif.latex?\theta "\theta"
[costheta]: https://latex.codecogs.com/gif.latex?\cos(\theta) "\cos(\theta)"
[xn]: https://latex.codecogs.com/gif.latex?x[n] "x[n]"
[Xk]: https://latex.codecogs.com/gif.latex?X[k] "X[k]"
[yn]: https://latex.codecogs.com/gif.latex?y[n] "y[n]"
[vecb]: https://latex.codecogs.com/gif.latex?\vec{b} "\vec{b}"
[hats]: https://latex.codecogs.com/gif.latex?\hat{s} "\hat{s}"
