---
layout: post
comments: false
title:  "A series of posts to try and understand Alphafold 2."
excerpt: "An attempt to take a deep dive into a protein folding neural network from a beginner's point of view."
date:   2022-10-26 22:00:00
mathjax: true
---

I've been fascinated by using data to figure out complex problems in physics, chemistry, and biology for many years. In my PhD, we used data to characterize the statistics of [slip avalanches](https://en.wikipedia.org/wiki/Dislocation_avalanches). The problem was that the analysis was often simplistic and we were going off the cuff with a lot of data that we used. One of the things I learned though, is that even complex phenomena can be broken down into simple terms if we look at things correctly. So when [Alphafold 2](https://www.nature.com/articles/s41586-021-03819-2) came out and stunned basically everyone, I knew immediately that I wanted to understand the inner workings. Luckily I had experience in training NNs, as well as training in chemistry/physics.

The goal of this post and hopefully subsequent posts is to try and understand the network in detail, why it works, or at least, why it might work. There are plenty of other blog posts on this subject, and the primary purpose of this post is for my own understanding of the subject matter.

Some prerequisits: Read [this post](https://fabianfuchsml.github.io/alphafold2/) by Fabian Fuchs to get a grasp of the problem at hand without the need for superfluous detail. I want to restate some of the facts here since it makes things clearer.

Proteins are composed of molecules known as [Amino Acids](https://en.wikipedia.org/wiki/Amino_acid). Suffice to say, these are complicated molecules in their own right, but the common trait of each of them is the  \\(-\text{NH}_3^+\\) group attached to them.
