Approximate inference with sampling
================
Dave Kleinschmidt

R code is available on <https://github.com/kleinschmidt/kavli>

Slides: [davekleinschmidt.com/kavli/lab/slides.html](http://davekleinschmidt.com/kavli/lab/slides.html)

Notebook (code+output): [github.com/kleinschmidt/kavli/tree/master/lab](https://github.com/kleinschmidt/kavli/tree/master/lab)

``` r
library(tidyverse)
library(purrrlyr)

## install.packages("devtools")
## devtools::install_github("kleinschmidt/beliefupdatr", agrs="--preclean")
library(beliefupdatr)
```

What does it mean to sample from a distribution?
================================================

------------------------------------------------------------------------

``` r
p <- data_frame(vot = seq(-20, 120),
           lhood = dnorm(vot, mean=60, sd=21)) %>%
  ggplot(aes(x=vot)) +
  geom_line(aes(y=lhood)) +
  ggtitle("Distribution")
p
```

![](README_files/figure-markdown_github/unnamed-chunk-2-1.png)

------------------------------------------------------------------------

``` r
vot_samples <- data_frame(vot=rnorm(100, mean=60, sd=21))
p + geom_rug(data=vot_samples) +
  ggtitle("Samples drawn from distribution")
```

![](README_files/figure-markdown_github/unnamed-chunk-3-1.png)

------------------------------------------------------------------------

``` r
p +
  geom_rug(data=vot_samples) +
  geom_histogram(data=vot_samples, aes(y=..density..), alpha=0.5) +
  ggtitle("Histogram of samples approximates distribution")
```

![](README_files/figure-markdown_github/unnamed-chunk-4-1.png)

Updating beliefs
================

Quantifying uncertainty
-----------------------

-   We have two categories /b/ and /p/.
-   Realized as normal distributions on an acoustic cue
    *p*(*V**O**T*|*μ*, *σ*<sup>2</sup>)
-   We **don't know** the mean *μ* and variance *σ*<sup>2</sup>.
-   Express our uncertainty as a **probability distribution** over the mean and variance:
    *p*(*μ*, *σ*<sup>2</sup>)
-   This distribution assigns a **degree of belief** for each particular combination of mean *μ* and variance *σ*<sup>2</sup>.

Learning from experience
------------------------

-   How do we **update our beliefs** based on experience?
-   **Conceptually**, Bayes Rule:
    *p*(*μ*, *σ*<sup>2</sup>|*x*)∝*p*(*V**O**T* = *x*|*μ*, *σ*<sup>2</sup>)*p*(*μ*, *σ*<sup>2</sup>)

------------------------------------------------------------------------

``` r
prior <- list(b = nix2_params(mu = 10, sigma2 = 43, kappa = 3, nu = 10),
              p = nix2_params(mu = 52, sigma2 = 460, kappa = 3, nu = 10))


xs <- seq(-20, 120)
predict <- function(beliefs, xs) {
  map2(beliefs, names(beliefs),
       ~ data_frame(category = .y,
                    vot = xs,
                    lhood = d_nix2_predict(xs, .x))) %>%
    lift(bind_rows)()
}

prior_pred <- prior %>%
  predict(xs)

p <-
  prior_pred %>%
  ggplot(aes(x=vot, color=category)) +
  geom_line(aes(y=lhood), size=2) +
  ggtitle("Predictions from prior beliefs") +
  ylim(-0.0025, 0.055)

p
```

![](README_files/figure-markdown_github/unnamed-chunk-5-1.png)

------------------------------------------------------------------------

``` r
one_dat <- data_frame(vot = 42, category="p")
p + geom_rug(data=one_dat) + ggtitle("Observed data point") +
  ylim(-0.0025, 0.055)
```

![](README_files/figure-markdown_github/unnamed-chunk-6-1.png)

------------------------------------------------------------------------

``` r
## updated_beliefs <- map(prior['p'], nix2_update_one, one_dat$vot)
updated_beliefs <- update_list(prior, p = nix2_update_one(prior$p, one_dat$vot))
post_pred <- updated_beliefs %>% predict(xs)
ggplot(prior_pred, aes(x=vot, color=category)) +
  geom_line(aes(y=lhood)) +
  geom_line(data=post_pred, aes(y=lhood), size=2) +
  geom_rug(data=one_dat) +
  ggtitle("Updated beliefs after one observation") +
  ylim(-0.0025, 0.055)
```

![](README_files/figure-markdown_github/unnamed-chunk-7-1.png)

------------------------------------------------------------------------

``` r
some_dat <- data_frame(vot = c(42, rnorm(10, mean=40, sd=10)), category="p")
updated_beliefs <- update_list(prior, p = nix2_update(prior$p, some_dat$vot))
post_pred <- updated_beliefs %>% predict(xs)

ggplot(prior_pred, aes(x=vot, color=category)) +
  geom_line(aes(y=lhood)) +
  geom_line(data=post_pred, aes(y=lhood), size=2) +
  geom_rug(data=some_dat) +
  ggtitle("Updated beliefs ...after more observations") +
  ylim(-0.0025, 0.055)
```

![](README_files/figure-markdown_github/unnamed-chunk-8-1.png)

How??
=====

------------------------------------------------------------------------

![Why, god](posterior0.png)

------------------------------------------------------------------------

![Why, god.](posterior1.png)

------------------------------------------------------------------------

![Why, god..](posterior2.png)

------------------------------------------------------------------------

![](no.jpg)

------------------------------------------------------------------------

Enough
------

-   Working with the distribution directly is **hard**.
-   Neither **researchers** nor **brains** want to do a lot of algebra.
-   What if there was a better way?!
-   Replace continuous **distribution** *p*(*μ*, *σ*<sup>2</sup>) with **samples** of plausible hypotheses.
-   Re-weight samples based on how well they predict the data

One sample of prior *p*(*μ*, *σ*<sup>2</sup>)
---------------------------------------------

``` r
draw_samples <- function(beliefs, n) {
  map2(beliefs, names(beliefs),
       ~ r_nix2(n, .x) %>%
         as_data_frame() %>%
         mutate(category=.y,
                sample = row_number(),
                weight = 1/n)) %>%
    lift(bind_rows)()
}

pred_samples <- function(samples, xs) {
  samples %>%
    as_data_frame() %>%
    mutate(pred = map2(mean, variance,
                       ~ data_frame(vot = xs,
                                    lhood = dnorm(vot, mean=.x, sd=sqrt(.y))))) %>%
    unnest(pred)
}

prior %>%
  draw_samples(1) %>%
  pred_samples(xs) %>%
  ggplot(aes(x=vot, y=lhood, color=category)) +
  geom_line() +
  ylim(0, 0.09)
```

![](README_files/figure-markdown_github/unnamed-chunk-9-1.png)

Many samples approximate *p*(*μ*, *σ*<sup>2</sup>)
--------------------------------------------------

``` r
prior_samples <- 
  prior %>%
  draw_samples(100)


prior_samples_pred <-
  prior_samples %>%
  pred_samples(xs)

prior_samples_pred_marg <-
  prior_samples_pred %>%
  group_by(vot, category) %>%
  summarise(lhood = mean(lhood))

p <- ggplot(prior_samples_pred,
       aes(x=vot, y=lhood, color=category)) +
  geom_line(aes(group=interaction(sample, category)), alpha=0.2) +
  ylim(0, 0.09) +
  geom_line(data=prior_pred, aes(linetype=factor("Exact", levels=c("Approx.", "Exact"))), size=2) +
  scale_linetype_discrete("Method", drop=FALSE)
p  
```

![](README_files/figure-markdown_github/unnamed-chunk-10-1.png)

Many samples approximate *p*(*μ*, *σ*<sup>2</sup>)
--------------------------------------------------

``` r
prior_samples_pred_marg <-
  prior_samples_pred %>%
  group_by(vot, category) %>%
  summarise(lhood = mean(lhood))

p <- p +
  geom_line(data=prior_samples_pred_marg, aes(linetype="Approx."), size=2)
p  
```

![](README_files/figure-markdown_github/unnamed-chunk-11-1.png)

Weighting samples by importance
-------------------------------

-   How do you **update** samples to reflect new information?
-   Notation: for each category, there are *K* samples of (*μ*<sub>*k*</sub>, *σ*<sub>*k*</sub><sup>2</sup>), where *k* = 1…*K*.
-   Samples are all equally representative of prior, so have the same **initial weight**: *w*<sub>0</sub><sup>*k*</sup> = 1/*K*.
-   Re-weight samples based on **likelihood** of data given that sample (how well hypothesis predicts data):
    *w*<sub>*n*</sub><sup>*k*</sup> = *w*<sub>0</sub><sup>*k*</sup>*p*(*x*<sub>1</sub>, …, *x*<sub>*n*</sub>|*μ*<sub>*k*</sub>, *σ*<sub>*k*</sub><sup>2</sup>)

------------------------------------------------------------------------

``` r
p <- ggplot(prior_samples_pred,
       aes(x=vot, color=category)) +
  geom_line(aes(y=lhood, group=interaction(sample, category), alpha=weight)) +
  ylim(0, 0.09) + ggtitle("Prior samples")
p
```

![](README_files/figure-markdown_github/unnamed-chunk-12-1.png)

------------------------------------------------------------------------

``` r
p +  geom_rug(data=one_dat) + ggtitle("Prior samples with one observation")
```

![](README_files/figure-markdown_github/unnamed-chunk-13-1.png)

------------------------------------------------------------------------

``` r
reweight_samples <- function(samples, xs, x_category) {
  samples %>%
    mutate(lhood = map2_dbl(mean, variance, ~prod(dnorm(xs, mean=.x, sd=sqrt(.y)))),
           weight = ifelse(category == x_category, weight*lhood, weight)) %>%
    group_by(category) %>%
    mutate(weight = weight/sum(weight),
           lhood = NULL) %>%
    ungroup()
}

samples_one_post <-  reweight_samples(prior_samples, one_dat$vot, "p")

samples_one_post_pred <-
  samples_one_post %>%
  pred_samples(xs)

p <- samples_one_post_pred %>%
  ggplot(aes(x=vot, color=category)) +
  geom_line(aes(y = lhood, alpha=weight, group=interaction(sample, category))) +
  geom_rug(data=one_dat) +
  ylim(0, 0.09) +
  ggtitle("Re-weighted samples")
p
```

![](README_files/figure-markdown_github/unnamed-chunk-14-1.png)

------------------------------------------------------------------------

``` r
samples_some_post <-  reweight_samples(prior_samples, some_dat$vot, "p")

samples_some_post_pred <-
  samples_some_post %>%
  pred_samples(xs)

p_some_post <- samples_some_post_pred %>%
  ggplot(aes(x=vot, color=category)) +
  geom_line(aes(y = lhood, alpha=weight, group=interaction(sample, category))) +
  geom_rug(data=some_dat) +
  ylim(0, 0.09) +
  ggtitle("Re-weighted samples")
p_some_post
```

![](README_files/figure-markdown_github/unnamed-chunk-15-1.png)

------------------------------------------------------------------------

``` r
samples_post_some_pred_marg <-
  samples_some_post_pred %>%
  group_by(vot, category) %>%
  summarise(lhood = sum(weight*lhood), weight = sum(weight))

p_some_post +
  geom_line(data = samples_post_some_pred_marg,
            aes(y=lhood, linetype="Approx."), size=2) +
  geom_line(data=post_pred, aes(y=lhood, linetype="Exact"), size=2)
```

![](README_files/figure-markdown_github/unnamed-chunk-16-1.png)

Doing things under uncertainty
------------------------------

-   Even if we don't know exactly the mean and variance, we still want to be able to categorize things.
-   If we know the categories' means and variances, this is straightforward:
    $$ p(c = b | x) = \\frac{p(x | \\mu\_b, \\sigma^2\_b)p(c=b)}{p(x | \\mu\_b, \\sigma^2\_b)p(c=b) + p(x | \\mu\_p, \\sigma^2\_p)p(c=p)} $$
-   But we don't know the means and variances!
-   Taking uncertainty about *μ*, *σ*<sup>2</sup> into account requires **averaging** over plausible values ("marginalizing" in Bayesian jargon).

------------------------------------------------------------------------

$$
\\begin{align}
  p(c=\\mathrm{b} | x) =& \\int \\cdots \\int d\\mu\_b d\\mu\_p d\\sigma^2\_b d\\sigma^2\_p \\\\
  & \\frac{p(x | \\mu\_b, \\mu\_p) p(c=b)}{p(x | \\mu\_b, \\mu\_p) p(c=b) + p(x | \\mu\_p, \\mu\_p) p(c=p)} \\\\ 
  & p(\\mu\_b, \\mu\_p, \\sigma^2\_b, \\sigma^2\_p) 
  \\end{align}
$$

------------------------------------------------------------------------

![](no.jpg)

------------------------------------------------------------------------

Doing things under uncertainty...**with samples!**
--------------------------------------------------

-   An integral is really a weighted average!
-   So we can calculate the **category boundary for each sample**, and average them together.

------------------------------------------------------------------------

``` r
sample_boundaries <- 
  prior_samples_pred %>%
  filter(10 <= vot,
         vot <= 70) %>%
  select(-mean, -variance, -weight) %>%
  spread(category, lhood) %>%
  mutate(prob_p = p / (b+p),
         weight = 1/100)

prior_bounds_marg <-
  sample_boundaries %>%
  group_by(vot) %>%
  summarise(prob_p = mean(prob_p))
  
p <-
  ggplot(sample_boundaries, aes(x=vot, y=prob_p)) +
  geom_line(aes(group=sample, alpha=weight)) +
  scale_alpha_continuous("Probability", range=c(0.1, 0.5)) +
  ggtitle("Prior boundaries")
p
```

![](README_files/figure-markdown_github/unnamed-chunk-17-1.png)

------------------------------------------------------------------------

``` r
prior_bounds_marg <-
  sample_boundaries %>%
  group_by(vot) %>%
  summarise(prob_p = mean(prob_p))
  
p +
  geom_line(data=prior_bounds_marg, size=2)
```

![](README_files/figure-markdown_github/unnamed-chunk-18-1.png)

------------------------------------------------------------------------

``` r
weights_one <- samples_one_post %>%
  filter(category=="p") %>%  # only updated /p/
  select(sample, weight)

post_one_bounds <- 
  sample_boundaries %>%
  select(-weight) %>%
  left_join(weights_one)

post_one_bounds_margs <-
  post_one_bounds %>%
  group_by(vot) %>%
  summarise(prob_p = sum(prob_p*weight))

ggplot(post_one_bounds, aes(x=vot)) +
  geom_line(aes(y=prob_p, group=sample, alpha=weight)) +
  geom_line(data=post_one_bounds_margs, aes(y=prob_p), size=2) +
  geom_rug(data = one_dat) +
  scale_alpha("Probability", range=c(0.1, 0.7)) +
  ggtitle("Updated boundaries")
```

![](README_files/figure-markdown_github/unnamed-chunk-19-1.png)

------------------------------------------------------------------------

``` r
weights_some <- samples_some_post %>%
  filter(category=="p") %>%  # only updated /p/
  select(sample, weight)

post_some_bounds <- 
  sample_boundaries %>%
  select(-weight) %>%
  left_join(weights_some)

post_some_bounds_margs <-
  post_some_bounds %>%
  group_by(vot) %>%
  summarise(prob_p = sum(prob_p*weight))

ggplot(post_some_bounds, aes(x=vot)) +
  geom_line(aes(y=prob_p, group=sample, alpha=weight)) +
  geom_line(data=post_some_bounds_margs, aes(y=prob_p), size=2) +
  geom_rug(data = some_dat) +
  scale_alpha("Probability", range=c(0.1, 0.7)) +
  ggtitle("Updated boundaries")
```

![](README_files/figure-markdown_github/unnamed-chunk-20-1.png)

------------------------------------------------------------------------

Flexibility of sampling
=======================

What if category isn't known?
-----------------------------

------------------------------------------------------------------------

``` r
xtreme_dat <- data_frame(vot = c(42, rnorm(10, mean=25, sd=10)), category="p")
samples_xtreme_post <-  reweight_samples(prior_samples, xtreme_dat$vot, "p")

samples_xtreme_post_pred <-
  samples_xtreme_post %>%
  pred_samples(xs)

p_xtreme_post <- samples_xtreme_post_pred %>%
  ggplot(aes(x=vot, color=category)) +
  geom_line(aes(y = lhood, alpha=weight, group=interaction(sample, category))) +
  geom_rug(data=xtreme_dat) +
  ylim(0, 0.09) +
  ggtitle("Re-weighted samples (category known)")

samples_post_xtreme_pred_marg <-
  samples_xtreme_post_pred %>%
  group_by(vot, category) %>%
  summarise(lhood = sum(weight*lhood), weight = sum(weight))

p_xtreme_post +
  geom_line(data = samples_post_xtreme_pred_marg,
            aes(y=lhood), size=2)
```

![](README_files/figure-markdown_github/unnamed-chunk-21-1.png)

------------------------------------------------------------------------

``` r
weights_xtreme_cat_unknown <-
  prior_samples %>% 
  pred_samples(xtreme_dat$vot) %>%
  group_by(sample, vot) %>%
  summarise(lhood = sum(lhood)) %>%
  summarise(lhood = prod(lhood)) %>%
  transmute(sample, weight = lhood / sum(lhood))

pred_xtreme_cat_unknown <-
  prior_samples_pred %>%
  select(-weight) %>%
  left_join(weights_xtreme_cat_unknown)

pred_xtreme_cat_unknown_marg <-
  pred_xtreme_cat_unknown %>%
  group_by(vot, category) %>%
  summarise(lhood = sum(weight*lhood), weight = sum(weight))

pred_xtreme_cat_unknown %>%
  ggplot(aes(x=vot)) +
  geom_line(aes(y=lhood, color=category, alpha=weight, group=interaction(category, sample))) +
  geom_line(data = pred_xtreme_cat_unknown_marg,
            aes(y=lhood, color=category), size=2) +
  geom_rug(data=xtreme_dat) +
  ggtitle("Re-weighted samples (category unknown)")
```

![](README_files/figure-markdown_github/unnamed-chunk-22-1.png)

------------------------------------------------------------------------

``` r
pred_xtreme_cat_unknown %>%
  ggplot(aes(x=vot)) +
  ## geom_line(aes(y=lhood, color=category, alpha=weight, group=interaction(category, sample))) +
  geom_line(data = pred_xtreme_cat_unknown_marg,
            aes(y=lhood, color=category, linetype="Post. (unknown)")) +
  geom_line(data = samples_post_xtreme_pred_marg,
            aes(y=lhood, color=category, linetype="Post. (known)")) +
  geom_line(data=prior_samples_pred_marg,
            aes(y=lhood, color=category, linetype="Prior")) +
  geom_rug(data=xtreme_dat) +
  scale_linetype("") +
  ggtitle("Prior vs. posterior, when category is known vs. unknown")
```

![](README_files/figure-markdown_github/unnamed-chunk-23-1.png)

Weird priors
------------

-   Analytical inference relies on using a **conjugate prior** which can be very restrictive.
-   For normal distribution, means that uncertianty about mean depends on the category variance.
-   What if we're very confident about what the variance *σ*<sup>2</sup> but not the mean?

------------------------------------------------------------------------

``` r
weird_prior_samples <- 
  data_frame(mean = rnorm(100, prior$p$mu, 15),
             variance = rnorm(100, prior$p$sigma2, 20),
             category = "p",
             sample = 1:100,
             weight = 1/100) %>%
  bind_rows(prior_samples %>% filter(category == 'b'))


weird_prior_pred <-
  weird_prior_samples %>%
  pred_samples(xs)

weird_prior_samples_pred_marg <-
  weird_prior_pred %>%
  group_by(vot, category) %>%
  summarise(lhood = mean(lhood))

p <- ggplot(weird_prior_pred,
       aes(x=vot, y=lhood, color=category)) +
  geom_line(aes(group=interaction(sample, category)), alpha=0.2) +
  geom_line(data=weird_prior_samples_pred_marg, size=2) +
  ylim(0, 0.09)
p
```

![](README_files/figure-markdown_github/unnamed-chunk-24-1.png)

------------------------------------------------------------------------

``` r
weird_some_post_pred <-
  weird_prior_samples %>%
  reweight_samples(some_dat$vot, "p") %>%
  pred_samples(xs)

weird_some_post_pred_marg <-
  weird_some_post_pred %>%
  group_by(vot, category) %>%
  summarise(lhood = sum(weight*lhood))

p <- weird_some_post_pred %>%
  ggplot(aes(x=vot, color=category)) +
  geom_line(aes(y = lhood, alpha=weight, group=interaction(sample, category))) +
  geom_rug(data=some_dat) +
  ylim(0, 0.09) +
  ggtitle("Re-weighted samples")
p
```

![](README_files/figure-markdown_github/unnamed-chunk-25-1.png)

------------------------------------------------------------------------

``` r
p +
  geom_line(data = weird_some_post_pred_marg,
            aes(y=lhood), size=2)
```

![](README_files/figure-markdown_github/unnamed-chunk-26-1.png)
