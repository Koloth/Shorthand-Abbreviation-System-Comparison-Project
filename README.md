# The Shorthand Abbreviation System Comparison Project

![](comparison_graph.svg)

## Background

One of the things that has always struck me as a little sad about the history of shorthand is that the mathematical techniques to really understand shorthand (namely information theory) came into existance essentially just as the shorthand itself was starting to fall out-of-fashion. In 1948, Claude Shannon established in *A Mathematical Theory of Communication* the mathematical limits of communication channels, which has lead to every fundamental technology in the modern digital era, such as data compression, error correction, efficient search, and even machine learning.

As shorthand is a method of communication, much of the theory applies directly to the study of shorthand systems.  In particular, this theory constrains what is possible in the *abbreviation systems* that underly any system of shorthand. This document will be restricted to only compare English shorthand systems, however the techniques are not limited to english alone.

## Methods

I'll get into details later, but for right now I simply want to say enough so that this chart can be legible.  First, I'll describe the $y$-axis (the reconstuction error), and then second I'll describe the $x$-axis (the average outline complexity overhead).

### Reconstruction Error

This is, in many ways the simpler of the two to describe.  Everyone who uses shorthand knows that most shorthand systems require taking on some level of ambiguity, where a single outline in isolation need not fully determine the word.  The reconstruciton error is a measure of how often you will encounter words for which the best guess at what their corresponsing outline means is not the original word.  Here is the full process to compute it.

1. Select a word at random with the frequency at which they occur in the English language.

2. Convert the word into it's corresponding outline.

3. Amongst all words in the English language, find the most common one that produces that outline, this is the best guess for the reconstruction of the original word.

4. Record the probability that this best guess *is not* the same as your original word (the reconstruction error).

### Average Outline Complexity Overhead

This one is a bit more complex.  In information theory, there is a fundamental quantity called the Shannon entropy, where if you have a bunch of words with probabilies $p_i$, then the entropy is:

$$
H(p) = -\sum_{i=1}^\infty p_i \log_2(p_i).
$$

The most important thing here is that the entropy tells you the smallest number of bits on average to send a message without errors.  

Thus, to compare systems what we can do is find the *optimal* cost of encoding the sequence of symbols provided by the shorthand system's theory into bits, and then see how this relates to the minumal values computed by the entropy of the dictionary of English words.  So, symbolically, if the optimal cost of encoding a shorthand outline into bits has $l_i$ bits, the *average outline complexity overhead* is defined as

$$
O(p) = \sum_{i=1}^\infty p_i (l_i + \log_2(p_i)).
$$

There are some subtleties here in how it was implemented (for instance I use the entropy of the symbols themselves instead of finding an encoding), but this gives the gist of it.

> [!IMPORTANT]
> There is a subtlety here that is very important to understand, which is that every system comes with *it's own dictionary*.  This means that the bound by the Shannon entropy is different for every system under comparsion.  This is one of the reasons that we need to look at the overhead, as it appears to be more stable under shifts of the dictionary (philosophically, looking at the overhead looks at typically how close the system gets to the optimal representation of words, which is independent of ).  The most optimal solution would be to have the same 5000 words written in every system, however this is not feasible.  Currently if you look at every system on this chart, they only share about 100 words in common between all of them.

### The Excluded Region

Now, we get into some of the interesting mathematics, and really one of the things that motivated me to make this chart in the first place: what happens when you send less information than the minimum defined by the entropy?  The answer is, you must inevitably start to introduce ambiguity into the system, and moreover [Fano's Inequality]() provides a limit on how large of error must be made.  This chart does not directly use Fano's inequality, but instead a simpler approximate bound of the difference in entropy divided by four.  This, while not fully precise, is independent of the dictionary, and is sufficient to give a sketch of the bound here.

<details>
<summary>Proof sketch.</summary>
This is a rough sketch of proof of this simplified version of a Fano-type inequality in this case.  We start by writing the entropy of our random word choice $W$ in terms of the random outline $O$.  By the definition of conditional entropy we have that

$$
H(W) = H(O) + H(W|O).
$$

That is to say, we separate the information of the random choice of word into the information of the random choice of outline plus the information of the random choice of words amongst those words with the given outline.  $H(W)$ and $H(O)$ are known, so this tells us that $H(W|O) = H(W) - H(O)$. We will assume that this difference is small.

Now consider the random variable $C$ which records if the reconstruction was correct or not (which is a deterministic function of $W$ and $O$).  Thus we have that $H(C|O) \le H(W|O)$ by the data processing inequality.  Let $p_c$ be the probability that the reconstruciton is correct.  If we assume that the difference in entropy is not larger than one bit, then we know that $p_c$ must be larger that $1/2$.  Since $C$ is a binary event, we can lower bound the entropy with the quadratic bound $4p_c(1-p_c)$.  Expanding in a linear approximation around $p_c \approx 1$ we have that this is approximately $4(1-p_c)$, and thus that $4(1-p_c) \le H(W) - H(O)$. This can be rearranged to show that $p_c \gtrsim 1-(H(W) - H(O))/4$.
</details>

## Lessons

### There are two kinds of shorthand systems

This was for me the biggest lesson of this project, and I think is a legitimately useful way to assess shorthand systems: there are in practice two kinds of shorthand systems, those that attempt to minimize error (Gregg, Pitman, T-Line, and most other similar systems live here and in my figure they cluster alond the bottom red area) and those that attempt to minimize length at the expense of errors (Taylor, Yash, Speedwriting, and most typable shorthand systems live here and they all tend to cluster along the lefthand line).  

This need not be the case: one could build a shorthand system that is bad at both, however the fact that these authors relentlessly optimized their systems means that they have been driven to the boundaries of what is mathematically allowed, and since the region that is mathematically allowed is defined by two lines, the shorthand systems themselves follow suit.

I think many people feel this one.  What it means to write in Keyscript versus Gregg is wildly different even if you ignore the fact that one is written and the other typed.  Keyscript feels highly ambiguous (because it is), but at the same time feels usable since it is so efficient (indeed only one of two systems where my analysis places it as mathematically impossible to have no errors it is so brief).

### Brief forms are almost inevitable

From this analysis, brief forms are an essentially unavoidable tool to use when trying to optimize these two things.  The reason for this is simple: for any system you have that is not the perfect system (so either has non-zero error, or has greater than the minimum possible outline complexity) you can always improve at least one of those metrics without making the other worse by introducing one or two more brief forms (the two is required if one wants to formally prove this, as sometimes you'll need to swap how two words are represented).  

Part of this is obvious to everyone in the shorthand community: of course brief forms let you make outlines shorter, that's what it is there for!  Indeed, that is true, but what at least I never realized is that breif forms can also be a tool of disambiguation.  For instance, in Taylor the fact that "to" is briefed to just "t" and "toe" is not allows for the system to actually become less ambiguous through the addition of briefs.  Sadly, not all briefs are of this type, so Taylor briefs actually increase the error rate by about 4% in total.

These feature, that literally *any* shorthand system can be made better by at least one, if not both, of these metric simultaneously by the addition of brief forms is part of why I believe they are unavoidable in shorthand systems.  Once your abbreviation principles have taken advantage of as much of the regularity of the language as possible, you can always add on briefs to make it better.

It is entertaining, and illustrative to take this to the extreme.  Take all the words in the English language, and line them up in *decreasing* frequency.  Then, similarly establish some way of representing your shorthand characters, and order all possible shorthand oulines in *increasing* order of complexity.  Now, match these up in order, that is simply assign the simplest outlines to the most common words.  This can be shown to be the optimal solution, and lives in that perfect "zero error, minimal length" corner.  All you need to do is memorize a few hundred thousand brief forms...

## Limitations

This immediately means there are many parts that this theory cannot touch.

1. **Speed.** I want to get this one out of the way immediately.  While the efficiency of an abbreviation system is indeed directly relavant for the speed at which a shorthand system can be written, it is not the only factor.  Most obviously, it doesn't actually address the actual writing of sybols on a page.  I will get into more detail later, but essentially it assumes that all symbols are optimally written, but that simply is not so: nobody has ever claimed that a Forkner "s" can be written in the same time as a Gregg "s"!  The speed of a system is a complex mix of the underlying abbreviation system along with the assigned strokes, and human bio-mechanical limitations.  I want to assert with no ambiguity here: **The only way to understand the speed of a shorthand system is to train skilled practicioners and measure their performance.**

2. **Legibility.** Every-so-often, you see someone create a shorthand system with some idea like: give every letter one of 26 distinct angles.  Such systems are completely unusable since people cannot distinguish 26 distinct angles, nor reliably write them.  Again, this is related to human factors, and no mathematical theory can completely account for them.

3. **Ambiguity.** While much of this work can inform and provide worst-case bounds on how ambiguous a shorthand system is, it cannot fully answer the questions of how ambiguous a system is.  First, it does not consider the *severity* of confused words.  A shorthand system that confuses *legal* and *illegal* is a far worse than a system that confuses *legal* and *algal*, as context can almost always distinugish the second, and almost never distinguish the first.  Second, it does not consider context at all.  It is easy to imagine systems which look decent by these two metrics, but for which context queues are essentially useless (for instance, a system that gives optimal brief forms for the first few hundred words, but then writes all rare words with a single dot).

4. **Memory Load and Reading Difficulty.** There is nothing this theoretical analysis would love more than a exhaustive list of perfectly chosen brief forms for every word in the English language.  Such a system can be astonishingly brief and unambiguous, however it is near-impossible to learn, or read (as, for intance, the briefs of "run" and "running" are not necesarily similar).  These are again dominated by human factors, and so are hard to make fully mathematical.  There is a human character to "how hard is this to learn" that cannot just be plotted on a graph.

5. **Phrasing.** This is not a fundamental limitation, but it is a limitation of the current implementation, but phrasing is not considered in this analysis.  This relates to the *Mathematical Caveats* section below, but this analysis ignores the cost of putting spaces between words.  This is not fully accurate for a number of reasons, but it means that running an analysis of phrasing is not impactful at this time, and would only serve to make this even more complex.

6. **Positional Informaiton.** Similar to phrasing, since the space between words isn't counted in this implementation, positional information isn't properly accounted for as it is essentially different types of spaces.  I've included it for systems where it is important like Pitman, but this is overly penalized in terms of outline complexity. 

## Comments on each system

At this point I want to leave a few comments on each system, particularly around how reliable I consider the data used to place it on the chart.

### Spelling

> [!Tip]
> This data is the baseline for all other comparisons, the frequency data is imperfect containing many non-sense words, but it is obtained from the [Google N-Grams Dataset](https://github.com/orgtre/google-books-ngram-frequency/blob/main/ngrams/1grams_english.csv).

Spelling is, of course, not a shorthand system.  It is, however, the basis of everything that we do here as it is the lowest error rate representation of words that exist.  Words like "read" in the sense of "I will read that book," versus "read" as in "I read that book yesterday," will still be conflated, however the general consensus I could find (sadly without proper citation) is that it is less ambiguous than spoken English as it can disambiguate "read" form "reed" and "read" from "red" and "right" from "write".  

Partially due to this feature, however, it is notoriously inefficient, writing words like "through" or "aitch" for words of three and two sounds respectively.

### IPA Spelling

> [!Tip]
> This is provided by a fairly high-quality [dictionary of IPA spellings](ttps://github.com/open-dict-data/ipa-dict/blob/master/data/en_US.txt).  It should be considered fairly high quality.

IPA Phonetic spelling aims to accurately reflect the sounds of words.  In particular, it is rather attentive to representing the space of possible vowel sounds, far more than is done in normal spelling.  Mostly due to this feature, it is less efficient than normal spelling, and has some of the errors inherent in any phonetic system.  

The most important thing to know about it on this chart is that it is essentially the lowest error that any phonetic system can achieve, as it aims to fully accurately capture the sound of the word.

### Cut Spelng

> [!Tip]
> This is provided by a fairly high-quality [dictionary of IPA spellings](ttps://github.com/open-dict-data/ipa-dict/blob/master/data/en_US.txt).  It should be considered fairly high quality.

[Cut spelng](https://en.wikipedia.org/wiki/Cut_Spelling) is a fairly recent English spelling reform introduced in 1998.  It provides a small number of rules to remove some of the redundant letters in English and move it closer to a phonetic system.  A few examples are: "fotograf", "juj", 

### Jeake, Philosophical Transactions No. 487

> [!Tip]
> This dictionary is error-free given the structure of this system.

This is a bizarre and simple system for which I can write all the rules here.  The system is orthographic, and if you encounter the letters below, write the corresponding symbol all connected together.

|:--:|:--:|:--:|:---:|:----:|:---:|:----:|:-:|
| ／ | ─  | ＼  |  │  |   (  |  )  |  ︵  | ︶ |

For the letters not on the list (**haeijo**), write nothing.  Finally, if there are multiple of the same symbol in a row, write a double size letter instead.

That's it.

It has never been popular or notable in any way as far as I can tell (being restricted to only [the original paper](https://royalsocietypublishing.org/doi/10.1098/rstl.1748.0041), and a [couple](https://royalsocietypublishing.org/doi/10.1098/rstl.1748.0046) of [saucy](https://drive.google.com/file/d/1Ue6BsKnPtQWiNo8je8ZA3-Eywnc5wfSu/view?usp=sharing) replies), but I wish to include it to show that people have proposed some pretty extreme systems that push the mathematical limit of what is possible!  One of only two systems I've found published for which is mathematically incapable of being unambiguous as it is so compressed.

## Most Wanted Help

### Better Pitman and Teeline

I do not know these systems well, nor do I have a high-quality dictionary.  This is a dangerous combination, and I almost opted to not include these systems lest people get the impression that I am trying to not give them fair consideration.  Instead, I hope that 

### Forkner

I was astonished there was not more Forkner content out there! The system is nearly typable, so I thought it would have been popular.

### All the Typables!

The typable systems are the ones most easily amenible to analysis, but I could only find a handful of them with transcribed dictionaries.  I'd love to get all the typable systems in here, so if anyone has one sitting around or is willing to transcribe one, let me know!

## A Mathematical Caveat

This is only for the true die-hards, but the *excluded region* in the graph above is not technically excluded.  That line is derived from a form of [Fano's Inequality](https://en.wikipedia.org/wiki/Fano%27s_inequality) which relates the entropy of the English language, the entropy of the abbreviation system, and the error rate.  This applies universally, however in order to connect the entropy of the abbreviation system to the average length (assuming optimal coding of the shorthand system's symbols) you need to assume that the abbreviation system is [uniquely decodable](), which means in essence that you could still read it without any spaces in between words.  

While this is not strictly true, it is actually often very close to true for any system based on either the spelling or sounds of words.  

>Afterall,Englishcanbereadevenifyoudeleteallthespacesbetweenwords.ThismeansthatEnglishisprettyclosetouniquelydecodable.Alsopeoplecanspeakquicklyblurringthesoundsofwordstogetherandstillbeunderstood.

Given this, I have decided to leave this problem as-is.  There are two ways to fix this if you want to be completely above-board:

1. **Add an explicit space character that you need to write after every word.** If your definition of "word" includes the space, then it is clearly uniquely decodable.  There is no issue, and the theory progresses normally.  This option is available in the code by sending, for instance, `delim="~"` to the `compute_statistics` function.  The effect is to shift everything off to the right.  This also can help deal naturally with positional information, which is currently over-emphasized compared to non-positional systems by encoding such systems with multiple types of space symbols.  However, this ignores the fact that many systems are essentially uniquely decodable already, even without the delimiter.

2. **Devise a notion of information theory where it is assumed delimeters are free.** This is not as crazy as is sounds!  I'll leave a link here to some handwritten notes (in Taylor shorthand) explaining this theory.  It turns out that there is a corresponding notion of entropy where, given a set of probabilities, $p_i$, you sort them in decreasing order and compute

$$
L(p) = \sum_{i=1}^\infty p_i\log(i).
$$

While option two is very enjoyable, and absolutely necessary to understand the limits when you consider the delimiter character to be free, these are all somewhat overkill, and not likely to be relevant in English shorthands, given that all the structure in words that let you perform the unique decoding would need to be purposely removed, greatly hindering readibility.

