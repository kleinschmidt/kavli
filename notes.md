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



# Outline

* Intro and motivation
    * Why does the brain care about statistics?
        * Computational level: inference (speech as example, focus on
          prediction)
        * Implementation level: efficiency (allocate time, space, energy)
    * Connection between inference and efficiency considerations:
        * quantify efficiency as mutual information between the stimulus (which
          is a fact about the world) and neural population response (which is
          all that you have to go on)
* _Which_ statistics does the brain care about?
    * "Statistics of the world" are **non-stationary**: change from one
      situation to the another.  Both for the sake of accurate inference _and_
      efficiency, the brain ought to care about these changes.
