# The Bayesian Learning Rule

Many machine learning algorithms are specific instances of an algorithm called the 'bayesian learning rule'. Everything from optimization to gradient descent to kalman filters.

The key idea is to approximate the posterior using candidate distributions estimated by using natural gradients.

## The Basics of Learning Algorithms

Take a function (model) that maps the data to a label.

![image.png](attachment:bd15365b-454b-4560-85d6-f97985a10899.png)

The basic premise is to formulate a question as a prediction problem and learn a model on existing data to predict future outcomes. Typically we collect samples of data with features and labels, and use those to learn parameters.

We estimate these parameters through the principle of trial-and-error, using a loss function and a regularizer.

### We need two things for BLR:
- A subclass of distributions to optimize over
- An optimizing algorithm, called the Bayesian Learning Rule

#### The distributions
A non-empty open set of parameters that is **finite**, **convex**, and **differentiable**. We see this with the typical multivariate Normal distribution.

#### The optimizing algorithm
This algorithm, referred to as the *Bayesian Learning Rule (BLR)*, searches for the best combination of parameters by updating them with sequential iterations. 

These iterations use the *natural-gradients* which rescale the vanilla gradients with the *Fisher information matrix* to adjust for the shape of the parameter space.


### Main Message:
many well known learning algorithms can now be derived directly following the above scheme using a single algorithm that optimizes. Differend exponential families Q give rise to different algorithms, and within those, various approximations to natural-gradients.



## Natural Gradients are a must!

They retrieve higher-order information about the loss landscape.

**Typical Derivative**: First-order, only gets the immediate slope. It is unaware of the sorrounding neighborhood and you must learn the art of learning rates and other tricks to best optimize it.

### Example: gradient descent

![image.png](attachment:f8de8472-81b2-4227-a9fb-ba21d08c4106.png)


The process below shows a sample update using gradient descent using only the first-order information:

![image.png](attachment:fe6fb6e5-3be6-41a2-816a-b7a7b89f2b6b.png)

This is the gradient evaluated at θt. Here we find the steepest descent direction around the local neighborhood in the parameter space. Then we pick a vector d such that θ + d minimizes the loss in euclidean space.

### The Issue?

If you look in the two illustrations below, we can see there is clearly a difference between them. But when your parameterize them by only the distance they appear to be the same (4)!

This is the downside of only working in parameter space. We are not able to take into account this greater information about the distribution realized by the parameter.

![image.png](attachment:55e3b0b2-445e-4de7-b2b0-6a076e01e6e3.png)

## Example for ML
In the image below is an illustration of a common problem when running a stochastic gradient descent. The model is a function approximator that does not know the true data distribution. So while we can take the same distance in each step, we end up in wildly different outcomes each iteration.

![image.png](attachment:353de0d1-3293-43a0-b5fc-a17f65ace99c.png)

![image.png](attachment:6d5bb205-ad61-4b13-9726-ecca05b2a10e.png)

## Functions are complicated
The entire manifold of a model is much more complicated than the two images above but they help to show the point, that the gradients of each parameter are different!

## Enter KL-Divergence
If the standard method of minimizing the loss using likelihood maximization has this issue, what metric can we use to improve upon this? KL-divergence! 

This method can measure the closeness of the two distributions. In the goal of supervised learning, optimizing the objective is equivalent to minimizing the divergence from the approximate distribution (the model) to the true distribution (the real data).

## Now how do we use it?
We have now realized that using this 'closeness' metric is seemingly superior to a standard euclidean distance, how would we go about implementing it in a model to improve the learning of parameters?

## Fisher Information

![image.png](attachment:35274aad-7a53-4200-9fdd-8dda1d810b18.png)

This roughly measures the sensitivity of the model's output distribution to small changes in the parameters θ. As you have seen before when adjusting the learning rate, much consideration has to be taken when picking the correct value as to not take too long to train or overshoot the loss minimum. 

Using the fisher information we can learn the local curvature, which in the two illustrations above would show us the stark difference in the two distributions, compared to just knowing the distance between the two.

It is in effect the **2nd derivative** of the likelihood function.

## The end result
using the illustration below we can see that we have scaled the parameter distances to a more equal distribution that enables smoother and quicker optimization. We are able to take a step with the gradients knowing exactly how far we will go towards minimizing the loss.

### We are controlling the movement in prediction space, not parameter space!

![image.png](attachment:87d7d0a8-9427-4183-8e10-95abad31c0ac.png)

## So what's the catch?
If it sounds so good why do we even have to think about optimizers anymore? it comes down to practicality. The reality is the computation needs of calculating the Fisher Matrix and it's inverse **squares** with the number of parameters. 

### But we can approximate for less work!
There are tricks we use to approximate the information that we could find with a second derivative without calculating it:
- Momentum
- RMSProp
- Adam

These are all variations on the standard SGD which use a running mean to approximate the gradients. Like in the example of the ball rolling down the hill, even if we are blind we can imagine the overall curve by keeping track of how the ball has rolled up to the current time in regards to the speed.

![image.png](attachment:332b7b1e-c9ae-484a-b0b8-a7b8249d40d3.png)
