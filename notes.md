# Planning

## Who is the audience

People have to attend two weeks.  First week brain+lang+prediction, second week
RL.  Mix of background, some more computational, some cog neuro.  Gina says
**more teaching than cutting edge research**, and to focus on domain general
prediction/adaptation and not _exclusively_ on language.

## What is the topic

Title is "Computational perspectives on language prediction in the brain".  My
lecture is the second one.  About linking prediction and adaptation (and the
brain???)

## What is the goal

* Prediction and adaptation are two sides of the same coin: distributional
  learning is the connection.
* Prediction requires **probabilistic knowledge**.
* Adaptation requires prediction at **multiple levels**.
* It's an open question how to link probabilistic predictions/processing to
  brain organization and processing.

## What empirical stuff to cover?

* Rapid adaptation/recalibration
* Higher level expectations: prior constraints on adaptation
* Link to brain function???

This is starting to look a lot like my dissertation defense talk.  Maybe with a
gentler introduction to speech and inference?  And more of a tie-in with brain
stuff.

# Framing 

Could frame things around linking statistics of the world with
processing/representation (motivation connection with brain).  Start with
e.g. sparse coding: representations allocated to effectively represent the
information in the natural world.  Then work up to multiple (shorter) time
scales.  Maybe even get into the stocker and simoncelli stuff?  I'm afraid
that's going to go over peoples heads though, so maybe not good for a motivating
example...just want to make the point that the brain is tuned to the statistics
of the word.  Then, part of that is, potentially, _retuning_ as needed when
those statistics change.

An interesting question at this point is **how you know when the statistics
change in a meaningful way**.  For instance, in one sense the statistics of the
visual world change every time you make a saccade!  So if you buy that the brain
is fitting itself to the statistics of the world, it has to actually learn
_which_ changes in statistics are meaningful and which are not.  This might be
something to get into _after_ we've covered the basics.

Another motivation is that this is a topic that naturally cuts across all three
of Marr's levels, and there's a lot of low hanging fruit, interesting work that
bridges these levels.

Okay so: **why should the brain care about statistics** is the big motivating
question here.  This is a connection between a _computational principle_ (the
statistics of the environment) and _brain organization and function_.  There's
two kinds of answers: efficiency and accurate inference.

For efficiency: the brain has limited resources (space, energy, time).  How do
you allocate those resources to represent information about the world?  Based on
the statistics.  For instance, if you take a fixed number of neurons and a bunch
of natural images, and you try to represent those images with neural responses
subject to the constraint that as few neurons as possible are activated for each
image, then you get basis functions that look very much like the receptive
fields in primary visual cortex.  Similarly for audio waveforms.  And higher
levels (like textures or complex cells).

For inference: both cognitive and neural considerations here.  Cognitive, world
is noisy and variable.  Need to know the distribution of the cues to each
category in order to figure out the most likely interpretation of a signal.

(((I'm not sure this is the right way to make this connection... there's clearly
a connection here but it might set up the wrong kind of expectations)))

It turns out that these two ideas are closely connected.  The reason is that
_the only information that downstream areas get about the world is through
firing rates of other neurons_.  So, we can think of each part of the brain as
trying to infer _states of the world_ based on _neural activity_ from other
parts.  We can ask: how well can you do that inference, given a particular set
of neural representations?


The same logic operates at the level of neural populations.  Neurons respond
_noisily_ to input.  We normally think of the _tuning curve_ of a neuron using
as the firing _rate_ elicited by different stimulus values.  In theory, if you
know the firing rates of all these different neurons, you can figure out exactly
what stimulus they're responding to. But really you just get some spikes, which
are more or less compatible with a range of different stimuli...etc.etc.
So you can ask, how much information do you get about, say, visual orientation
given the number of spikes you observe pfrom a bunch of neurons?  @Ganguli2010



...can think of these tuning curves (in ensemble) as part of the _prediction_
about what kind of stimuli you expect to get.  And when exposed to different
kinds of




Then onto adaptation: tuning curves change after exposure to different _stimulus
ensembles_.  They do so in way that increases the mutual information between the
firing rates and the different stimuli in that ensemble.



We can think of adaptation as an inference process itself: you're making
inferences about what the underlying distributions of sensory signals ((( will
want to tie in ideas about inference involving predictions above... ))) critical
thing is that these higher level inferences _also_ involve higher-level
_predictions_: you're (implicitly) making predictions about which distributions
you think are more likely.  Your ability to adapt to any particular distribution
is going to largely depend on the amount of probability you assign to it a
priori.  And, this leads us to ask an analogous question: what's the best prior
expectations?  The first order answer is that you should roughly expect things
like you've seen before (plus some allowance for novelty); that is, you should
set your prior based on your experience with teh distributions you've
encountered in the world.

My research focuses on these issues in one particular domain: speech
perception.


## Link between neural adaptation and distributional learning

If _neural adaptation_ is adaptation to _statistical_ properties of the world
(distributional learning), then what are the limits on this?  Are there some
sets of statistics that a neural population are better able adapt to.  Surely it
seems plausible that there are changes you _can't_ adapt to.

We can apply the same logic about fitting to the statistics of the world here,
too, at this higher level.  Again, two parts: efficiency and inference.  Need to
be able to represent information in the world, including variable statistics,
given limited resources.  And if we know what statistics are more or less likely
in this world, then we assign higher probability to those statistics when they
do come.

## Dilemma: does it make sense to link inference and efficiency in neural pop.s?

People might rightly wonder how you'd do inference given a heterogeneous
population of neurons that are efficiently allocated.  For a continuous
population there are lots of ways to do it (any of the PPC readout schemes would
work, right?).  For a discrete thing, I think you can connect with
DDMs/sequential sampling: you're getting a little evidence for or against a
particular explanation.  **Ask mike about this?**

The big Q here is whether to highlight the parallels up front, or come back
around at the end in a "link to brain function" bit at the end.  I'm now kind of
leaning in that direction (tie back at the end).  Could make a list of things
that apply to accurate inference, and just replace with efficient processing.
Or point out the link in general at the end.

## Figures

### Major orienting figure: cartoon diagram

    x   --> p(r|x) --> p(x|r) \propto p(x|r) p(r)
  world      neural   inference
features   responses

want to help people get a sense of how these things fit together (and ground
symbol use).  But I'm not sure this is the best way to do it...

## Coming back around to _goals_

Where do I want people to end up?  Basically to realize that there's a tight
(conceptual) link between adaptation and prediction _in general_, and in
particular for understanding the organization and function of the brain.  My
hook is that **prediction isn't just guessing the next word**.  I want to talk
about the deep importance of prediction for dealing with the intrinsic
variability---and uncertainty---of the world.  (Prediction error ties in nicely
with the second week).

And, equally important, that adaptation involves prediction at many levels.
This makes understanding the neural circuits super hard.

I could frame the motivation as motivating a link between brain
function/organization and the high level computational perspective on the
inference processes.  Then I'm going to focus on modeling/understanding the
computational issues.

Computational level: inference intimately involves predictions.  And adaptation
involves prediction in two critical ways.  First, can look at it as reducing the
prediction error by learning the statistical properties of the world.  Second,
it is itself an inference process, involving combining prior expectations about
what distributions you think are likely with what's consistent with the data you
observe.

# Outline

* Intro and motivation
    * Adaptation is pervasive in the brain and in behavior, across many many
      time scales and domains.  (say that I'm focusing on speech here?)
    * Why does the brain adapt?  Or, _what_ is adaptation?  (Very much up in the
      air) **WHAT DOES IT MEAN**
        * ...because the statistics of the world change.
        * well that begs the question: why should the brain care about the
          statistics of the world in the first place?
    * Computational level: inference (speech as example, focus on prediction)
    * Implementation level: efficiency (allocate time, space, energy)
    * There's a deep connection between inference and efficiency considerations:
      (maybe make this real quick, say I'll come back to at the end if there's
      time)
        * quantify efficiency as mutual information between the stimulus (which
          is a fact about the world) and neural population response (which is
          all that you have to go on).
        * If you don't make accurate predictions, you're either going to miss
          potentially informative things (don't cover that part of stimulus
          space), or you're going to allocate a lot of resources to things that
          aren't very informative (they're complementary).
    * Adaptation is _distributional learning_, which is an _inference_ at a
      higher level.
* Inference and prediction
    * Speech perception as an example: how do you know what someone's said?
    * Categories are variable.  You can't just look at the spectrogram, read off
      a few cue values, and know for sure.  Best you can do is figure out how
      _likely_ each category is as an explanation.  In order to do that, you
      need to know the distributions, or the _generative model_.
    * You look at how well each of the possible interpretations _predicts_ that
      value (relative to the other possibilities).
    * In a bayesian framework, we call those predictions the likelihood
    * flag **FELDMAN** and **CLAYARDS** (among others)
    * (idea of generative model, parametrized)
    * punchline: statistically optimal inferences depend on predictions of
      "lower level" features by "higher level"
* Adaptation and prediction
    * critical piece here: accurate inference depends on knowing the
      distributions.
    * those distributions, as a general rule, are not always the same.  SPeech
      is particularly susceptible to this (lack of invariance)
    * we've known for nearly 20 years now that people deal with this by learning
      abuot particular talkers (Nygaard).  
    * Now slides on recalibration/sel.ad.: this learning can be __very
      rapid__. ...boundary shift. ((( does this order of things make sense?
      maybe want to jump straight to dist learning? but going to have to explain
      the paradigm anyway )))
    * learning distributions explains this. (walk through logic, focus on
      decreasing **prediction error**)
    * (as an aside: why not just learn the boundary? 
        * one obvious (and probably wrong) reaosn is that you need feedback
          about whether your estimate of the boundary is right.  but that might
          not necessarily be true, and there's often _implicitly_ information
          about what cateogry a person is trying to say.
        * a deeper reason is that the boundary doesn't predict which sounds you
          should actually hear, and so you don't get any prediction error which
          can guide learning.  most sounds are not near the boundary. if
          you're willing to make some commitments about what these distributions
          look like, you can get information about the distribution from _every_
          observation you make.  You also get earlier signals that you need to
          update your beliefs, since you don't necessarily need to make a
          _mistake_ in order to pick up on the fact that your beliefs aren't
          quite right.
* Predictions about _distributions_
    * Big question here is where does belief updating _start_?
    * We can model this distributional learning as an _inference process_:
      (bayes rule: conditioned on parameters)  to make math work out we need to
      _condition_ on the parameters of the generative model.  What do these look
      like?  Think of them as a big vector of all the means, variances, etc. of
      the different cue distributions.
    * Now the logic of belief updating is just the same: we've been over this
      intuitively, but you just ask "how well does this hypothetical generative
      model predict the data I've seen?".
* What can we do with this?
    * At a high level, gives us a framework for reasoning about how people
      structure their previous experience with other contexts.
    * More specifically, it provides a (qualitative) account of the wide range
      of behavior findings on how people handle talker variability, from rapid
      adaptation to long-term, persistent representaitons of specific familiar
      talkers.
    * Drilling down even further: it gives us things we can _measure_ from
      speech from many different talkers in order to make predictions about
      listeners' behavior at different levels.
    * It also simultaneously posits a bunch of specific knowledge that listeners
      (implicitly or explicitly) use to guide their adaptation **and** provides
      a way for us to probe that knowledge.  I'll talk a little more about that
      because it's a really interesting example of the benefits of bayesian
      models.
* Bayesian mind reading
* Circle back around to the brain
    * Efficient coding
    * Close connection between adaptation to improve inference and to improve
      efficiency.  (ganguli & simoncelli)
    * Evidence that adaptation does improve the mutual information between
      population firing rates and stimulus ensemble.
