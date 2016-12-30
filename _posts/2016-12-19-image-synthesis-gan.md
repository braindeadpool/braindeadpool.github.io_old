---
layout: post
title: Attribute based image synthesis using Generative Adversarial Networks
category: Computer Vision, Deep Learning
tags: cv, deep-learning, tensorflow
---
It's been a few months since my last post - partly in thanks to courseload keeping me busy for most of it. Now that my first semester has ended, I finally found some time to hack around tensorflow and deep learning architectures.

Couple of days back, I came across a (very recent) paper by Reed et al. titled [Generative Adversarial Text to Image Synthesis](https://arxiv.org/abs/1605.05396) which uses a generative adversarial network(GAN) to take natural language sentences and synthesize images on them. (If you need some resources on GAN, I have listed some that I found useful at the end of this post). Here are some results from their [github page](https://github.com/reedscot/icml2016)
![Text to image with GAN]({{ site.baseurl }}/public/text2image_gan1.jpg)

I decided to implement a similar GAN myself which takes in scene/object attributes and generates an image. For image attributes, I used the [SUN attribute dataset](https://cs.brown.edu/~gen/sunattributes.html) which has 102 different attributes and 14k images.

The generator network takes in a 1x102 binary vector representing which attributes are present and which are not. I decided to forgo the fully convolutional network based description compression (used in the original paper to transform the text description to a fixed dimension compressed space) since the number of attributes we have are fixed in size.

We batch train the model to maximize the following:

* $$ s_w $$ - Discriminator score when fed a real image but incorrect attributes
* $$ s_f $$ - Discriminator score when fed a fake image with correct attributes

and to minimize the following:

* $$ s_r $$ - Discriminator score when fed a real image with correct attributes

by setting the discriminator loss function to
$$
\begin{equation}
	L_D = log(s_r) + (log1(-s_w) + log(1-s_f))/2  \tag{1}
\end{equation}	
$$
and the generator loss function to
$$
\begin{equation}
	L_G = log(s_f) \tag{2}
\end{equation}	
$$

Note: The score value will lie between 0 and 1 indicating how fake it is, ie the higher the discriminator score, the higher the probability of the input image to be synthetically generated according to the discriminator.

The source code is available on github - [http://www.github.com/braindeadpool/image_synthesis_gan](http://www.github.com/braindeadpool/image_synthesis_gan). Its commented for most part so sifting through should be easy.


Here are some wonderful resources to get started on understanding GAN and related stuff:

* A very nice blog post by OpenAI on generative models which cover GAN - [https://openai.com/blog/generative-models/](https://openai.com/blog/generative-models/)
* A short and easy to follow blog post (with tensorflow code) - [http://blog.aylien.com/introduction-generative-adversarial-networks-code-tensorflow/](http://blog.aylien.com/introduction-generative-adversarial-networks-code-tensorflow/)
* The original paper by Goodfellow et al. - [Generative Adversarial Networks](https://arxiv.org/abs/1406.2661)
* A great blog post and code by Brandon Amos - http://bamos.github.io/2016/08/09/deep-completion/#step-1-interpreting-images-as-samples-from-a-probability-distribution


