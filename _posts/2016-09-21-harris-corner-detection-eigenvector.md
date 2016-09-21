---
layout: post
title: Harris-Stephens' corner detection algorithm
category: Computer Vision
tags: cv, corner detection, gatech
---

One of the courses at GaTech this semester is Computer Vision, taught by [Prof. James Hays](http://www.cc.gatech.edu/~hays/). It's a really nice course and to top it off, all the course materials are available online on the [course website](http://www.cc.gatech.edu/~hays/compvision/).

As part of the [second assignment](http://www.cc.gatech.edu/~hays/compvision/proj2/), I'm in the middle of implementing a Harris-Stephens' corner detector for feature matching. Both the [lecture slides](http://www.cc.gatech.edu/~hays/compvision/lectures/08.pdf) and [wikipedia](https://en.wikipedia.org/wiki/Corner_detection#The_Harris_.26_Stephens_.2F_Plessey_.2F_Shi.E2.80.93Tomasi_corner_detection_algorithms) give a nice summary of the math behind it. 

However, one thing that was unclear to me was how the eigenvalues of the Harris matrix lets us ascertain whether a good interest point(corner) has been found. Intuitively, you have a corner if the image intensity changes rapidly in both $$x$$ and $$y$$ directions as you move the window. And it seems this change in intensity(gradient) along the $$x$$ and $$y$$ directions somehow correspond to the eigenvalues of the Harris matrix - is there an intuitive way of understanding this relation?

In trying to figure it out, I did dig up some stuff online that helped me out understand eigenvalues and eigenvectors better:

* [math.stackexchange to the rescue](http://math.stackexchange.com/questions/243533/how-to-intuitively-understand-eigenvalue-and-eigenvector)
* [dsp.stackexchange saves the day](http://dsp.stackexchange.com/questions/10104/why-eigenvalues-concerned-in-harris-corner-detection)

The last link above in particular cleared my query and helped me understand how the eigenvalues of the Harris matrix are related to the gradient of the image intensity along the $$x$$ and $$y$$ directions. 
For a window $$(u, v)$$ and a shift of $$(\Delta{x}, \Delta{y})$$ at a location $$(x, y)$$, the sum of squared differences (SSD) between the patch and the shifted patch, $$S(\Delta{x}, \Delta{y})$$ can be approximated as:
$$
\begin{equation}
	S(\Delta{x}, \Delta{y}) \approx \begin{bmatrix} \Delta{x} & \Delta{y} \end{bmatrix} A \begin{bmatrix} \Delta{x} \\ \Delta{y} \end{bmatrix} \tag{1}
\end{equation}	
$$

where $$A$$ = Harris matrix. Now to answer my own query on how the eigenvalues of $$A$$, denoted as ($$\lambda_1, \lambda_2$$) relate to the image gradient along $$x$$ and $$y$$ - 
Remember that $$A$$ is a symmetric matrix. So it can be decomposed as:

$$ A = Q \Lambda Q^T$$

$$Q$$ = orthogonal matrix with eigenvectors of $$A$$ as its columns 

$$\Lambda$$ = diagonal matrix with eigenvalues of $$A$$

Now, rewriting equation $$({1})$$,

$$
\begin{equation}
	\begin{split}
		S(\Delta{x}, \Delta{y}) &\approx \begin{bmatrix} \Delta{x} & \Delta{y} \end{bmatrix} Q \Lambda Q^T \begin{bmatrix} \Delta{x} \\ \Delta{y} \end{bmatrix} 	\\
			&= \begin{bmatrix} \Delta{x'} & \Delta{y'} \end{bmatrix} \Lambda \begin{bmatrix} \Delta{x'} \\ \Delta{y'} \end{bmatrix} \\ 
			&= \begin{bmatrix} \Delta{x'} & \Delta{y'} \end{bmatrix} \begin{bmatrix} \lambda_1 & 0 \\ 0 & \lambda_2 \end{bmatrix} \begin{bmatrix} \Delta{x'} \\ \Delta{y'} \end{bmatrix} \\ 
			&= \lambda_1 \Delta{x'}^2 + \lambda_2 \Delta{y'}^2
	\end{split}
	\tag{2}
\end{equation}
$$

where 
$$
\begin{bmatrix} \Delta{x'} & \Delta{y'} \end{bmatrix} = \begin{bmatrix} \Delta{x} & \Delta{y} \end{bmatrix} Q
$$
is the original displacement vector transformed by the rotation matrix $$Q$$.

As you can see in $$(2)$$, the SSD now directly relates to the eigenvalues. If $$\lambda_1$$ is high, the $$S$$ changes rapidly along the $$x$$-direction and similarly if $$\lambda_2$$ is high, $$S$$ changes rapidly along $$y$$-direction. Hence, if you have high values for both $$\lambda_1$$ and $$\lambda_2$$, the image intensity changes rapidly along both axes, implying we have a good corner point in the window.

Now we have the math ready for finding corner points. However, performing eigenvalue decomposition of $$A$$ into $$Q$$ and $$\Lambda$$ is computationally expensive due to the square-root computation. So Harris and Stephens instead compute 

$$M_c = \lambda_1 \lambda_2 - \kappa.(\lambda_1 + \lambda_2)^2 = det(A) - \kappa.trace(A)^2$$

$$\kappa$$ = a tunable sensitivity parameter

$$M_c$$ is computationally much cheaper to compute.
And that's about it on Harris corner detection!