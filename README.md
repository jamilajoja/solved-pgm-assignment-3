Download Link: https://assignmentchef.com/product/solved-pgm-assignment-3
<br>
In this exercise you are introduced to Markov random fields and their application to image denoising. We solve

<em>p</em>(<em>true</em>|<em>noisy</em>) = <em>p</em>(<em>T</em>|<em>N</em>) = <em>p</em>(<em>N</em>|<em>T</em>)<em>P</em>(<em>T</em>)                                   (1)

with gradient ascent method and compare our results to median filtering method. Finally, we have a look at different image priors <em>p</em>(<em>T</em>) and question the meaning of this priors as well as the independence assumption.

<h1>1         Evaluation</h1>

Before we start with image denoising, we need an evaluation framework that generates noisy images and calculates a performance measure to compare our algorithms.

<strong>Tasks:              </strong>

<ul>

 <li>Generate artificial images with binary pixel values <em>x<sub>ij </sub></em>∈ {0<em>,</em>255} that shows stripes (see left image), i.e.,</li>

</ul>

<table width="215">

 <tbody>

  <tr>

   <td width="108"></td>

   <td width="108"></td>

  </tr>

 </tbody>

</table>

T = toy stripes(n, m, sSize),

and a checkerboard pattern (right image), i.e.,

T = toy checkerboard(n, m, cSize).

<em>T </em>is the true image of size <em>n </em>× <em>m </em>with stripe width <em>sSize </em>or black square size <em>cSize</em>.

<ul>

 <li>Write a function that adds Gaussian noise to an existing image <em>T</em>:</li>

</ul>

N = add noise(T, sigma)

Also, write a function which adds salt and pepper noise (randomly occurring black and white pixels):

N = add sp noise(T, p)

<ul>

 <li>Write a function that calculates peak signal-to-noise ratio</li>

</ul>

(2)

with reconstruction error <em>err </em>= (<em>T </em>− <em>N</em>)<sup>2</sup><em>/</em>(<em>nm</em>).

psnr = calc psnr(T, N)

<strong>Hints:</strong>

<ul>

 <li>You can use matlab function random() with <em>µ </em>= 0 to add or substract noise values from each pixel. Be sure that your pixel values are still in the range of [0<em>,</em>255].</li>

 <li>You should fix the random seed in your functions with Matlab function RandStream. This makes all results comparable to each other.</li>

</ul>

<h1>2         Median Filtering</h1>

A simple image denoising technique is median filtering. As you will find out, it often leads to blurred images.

<strong>Tasks:              </strong>

<ul>

 <li>Write a function T = median filter(N, nsize) that replaces each pixel in a noisy image <em>N </em>with the median of the pixel values in a window of size <em>nsize </em>× <em>nsize </em>around it. Take care at the image borders.</li>

 <li>Evaluate this denoising procedure on our different artificial examples with 10% salt’n’pepper noise and with the image png downloadable from the course website after adding Gaussian noise with <em>σ </em>= 25 (10% of the range). Show images before and after denoising and document PSNR for these images. What do you observe?</li>

 <li>Vary the amount of noise for the image png.</li>

</ul>

<h1>3         MRF-based Denoising with Gradient Ascent</h1>

A better denoising technique is a MRF-based method with gradient ascent:

<em>T<sup>t</sup></em><sup>+1 </sup>← <em>T<sup>t </sup></em>+ <em>η</em>∇<em><sub>T </sub></em>log<em>p</em>(<em>T</em>|<em>N</em>)                                             (3)

with log<em>p</em>(<em>T</em>|<em>N</em>) = log<em>p</em>(<em>N</em>|<em>T</em>) + log<em>p</em>(<em>T</em>) + <em>const</em>. <em>const </em>is ignored in all further computations as it doesn’t depend on <em>T</em>. We need the gradient of both the likelihood log<em>p</em>(<em>N</em>|<em>T</em>) and the prior log<em>p</em>(<em>T</em>). For simplicity, let’s start with a Gaussian log-likelihood

<em> ,                             </em>(4)

<table width="438">

 <tbody>

  <tr>

   <td width="421">as well as a Gaussian log-prior</td>

   <td width="17"> </td>

  </tr>

  <tr>

   <td width="421">log<em>p</em>(<em>T</em>) = Xlog(<em>f</em><em>H</em>(<em>T</em><em>i,j,T</em><em>i</em>+1<em>,j</em>)) + log(<em>f</em><em>V </em>(<em>T</em><em>i,j,T</em><em>i,j</em>+1))<em>i,j</em>with</td>

   <td width="17">(5)</td>

  </tr>

 </tbody>

</table>

(6)

for horizontal neighbors and log<em>f<sub>V </sub></em>analogously.

<strong>Tasks:              </strong>

<ul>

 <li>Write a function lp = denoising lp(T, N, sigma) to compute the log-posterior,</li>

</ul>

g = denoising grad llh(T, N, sigma) to compute the gradient of the log likelihood log<em>p</em>(<em>N</em>|<em>T</em>), and g = mrf grad log gaussian prior(T, sigma) to calculate the gradient of the Gaussian prior log<em>p</em>(<em>T</em>).

<ul>

 <li>Finally, implement the gradient ascent to denoise the image</li>

</ul>

T = denoising grad ascent(N, sigma, eta).

You should initialize the gradient ascent with the noisy image <em>N</em>.

<ul>

 <li>Explain your parameter tuning. Which observation do you make for different <em>σ </em>and <em>η</em>? (See hints.)</li>

 <li>Evaluate your implementation with the noisy images from Task 2 for the same noise parameters. Check the PSNR and the increasing log-posterior curve. Compare your results with median filtering – which algorithm shows better results and why?</li>

 <li>What happens if you initialize gradient ascent with the output of median filtering? Is there an improvement in performance or a faster convergence observable?</li>

</ul>

<strong>Hints:</strong>

<ul>

 <li>The gradient g should have the same size as the input image while the log-posterior lp is just a scalar.</li>

 <li>You may need many iterations to reach approximate convergence (<em>&gt; </em>1000). You can verify your algorithm by displaying the log-posterior curve.</li>

 <li>Start with small artificial images to see the correctness of your algorithm and to get a feeling for the parameters. Usually, it is recommended to test different powers of 10, i.e., <em>σ,η </em>∈ {<em>…,</em>10<sup>−1</sup><em>,</em>10<sup>0</sup><em>,</em>10<sup>1</sup><em>,…</em>}.</li>

</ul>

<h1>4         A Different Prior</h1>

As we know from the lecture, a Gaussian distribution does not match the statistics of a natural image very well. A more appropriate distribution is the Studentt distribution:

(7)

<strong>Tasks:              </strong>

<ul>

 <li>Implement the gradient of this log-prior given above as</li>

</ul>

g = mrf grad log student prior(T, sigma, alpha).

Do not forget the log in your partial derivatives.

<ul>

 <li>Evaluate your denoising algorithm with this new prior, <em>α </em>= 1 works fine. What do you observe?</li>

 <li>Display the gradient of the log-prior for the test images. Explain your results.</li>

</ul>

<h1>5         Independence Assumption</h1>

Finally, we question the assumption that the noise in each pixel is independent.

<strong>Tasks:              </strong>

<ul>

 <li>Is this assumption reasonable?</li>

 <li>What happens if you add spatially dependent noise to your image, e.g., a “noisy stripe“ like in old movies.</li>

 <li>Show results for both priors (Gaussian and Student-t distributions).</li>

</ul>

<h1>6         Bonus</h1>

So far, we have used MRF-based image denoising for gray images only. How would you denoise a color image?