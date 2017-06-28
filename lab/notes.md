# Computational Lab for Kavli 

Want to introduce sampling ideas.  Compare with exact inference.  Show that you
can use any prior you want.  Give a sense of how importance sampling works.
**Really gentle**, many people are not computational.  Also, give a sense of
how you _do_ something with your posteriors.  Why bother?

* Belief updating, sensitive to uncertainty/weight strength of evidence against
  strength of beliefs.
* Make decisions taking into account your uncertainty.  "best guess"

Question is how much code to show.

Other question is how much math to go into.  Answer: not a ton, but some.
Replace integral with sum.  "Maybe you remebmer from calculus (long ago), an
integral is basically just a weighted average".

I think one thing that might be confusing: data distribution is a normal dist,
but prior/posterior are also normal.



# Concepts

* _Why_ sampling (exact inference is hard-to-impossible. makes models
  inflexible)
* Approximate integral with a sum -> use samples to _do_ things with your
  beliefs.
    * (Beliefs are not very useful unless you can do something with them)
    * It's easier to do things (on a computer) with samples than with
      distributions


# outline

## Basics of sampling

* What does it mean to "sample" from a distribution?  It's a procedure to
  generate a bunch of values such that their histogram approximates the actual
  probability distribution
* How you generate distributions is actually really complex.
* Example: normal distribution. make sure that samples have the same mean and
  variance (for instance).
* We're going to use a bunch of samples _in place of_ the distribution.  We'll
  get to what that means in practice, but conceptually it means that you 

## Updating beliefs (with samples)

* Specifying the model: need to formalize what it means to "not know the mean":
  put a _probability distribution_ on it.  Assign a degree of belief to
  particular values of mean.
* Exact updating rules --- hard and bad.  Now that we have beliefs, how do we
  update them with data?  Bayes rule (**conceptual**, and then **practical**).
* Updating with particles --- easy and good.

## Doing something (with samples)

* Getting the category boundary
* Getting the category boundary---with samples
* ...with a weird prior

## General sampling considerations

* It's a deep sea.
* Lots of different methods.

# Basics of hierarchical inference

* Have one category distribution.  It's a normal distribution, and you know the
  variance but not the mean.  What's your uncertainty about the mean?  That's
  your _prior_.  Turns out that we can use a normal distribution for that, too.
* How do you update your beliefs?  Bayes rule: $p(\mu | x, \mu_0) = \frac{p(x |
  \mu)p(\mu | \mu_0)}{p(x)}$
* In limited cases you can write down $p(\mu | x, \mu_0)$ as a nice, known
  distribution (in this case, another normal distribution).

## Doing something with your beliefs

* Why even bother going through this inference process?  Take for example the
  task we've seen a few times: figuring out whether someone has said a /b/ or a
  /d/.
* Let's say that we know the variance of both the categories, and are trying to
  figure out the mean value for /b/ (but we know /d/).
* TO simplify even further, we can just worry about estimating the category
  _boundary_ (point where /b/ and /d/ are equally likely).  When the two
  categories have the same variance, this is just the midpoint between their
  means.

* So: given that your beliefs about the mean $\mu$ are quantified as a normal
  distribution $N(\mu | \mu_0, \sigma_0)$, what's your best guess at $\mu$?  We
  can think of this as a sort of "weigthed average", which we call the
  **expected value** of $\mu$: $E(\mu | \mu_0, \sigma_0) = \int \mu d\mu$
