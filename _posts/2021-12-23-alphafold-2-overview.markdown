---
layout: post
comments: false
title:  "A high level overview of Alphafold 2"
excerpt: "A bird's-eye view of AF2."
date:   2021-12-23 22:00:00
mathjax: true
---

<h3>A Bird's-Eye View</h3>

Any complex ML pipeline needs both global and local understanding to really get a feel for what's going on. In the case of Alphafold 2 (AF2), we need to understand what the inputs/outputs are and how they are processed at a high level. In the [the article](https://www.nature.com/articles/s41586-021-03819-2) published on AF2, we see the central figure 1,

<div class="imgcap">
<img src="/assets/alphafold_overview.png"
     width="750"
     height="auto">
<div class="thecap">
  Taken from <em>Jumper et. al. Highly accurate protein structure prediction with AlphaFold</em>. The input sequence is an amino acid sequence. This sequence is then searched through a genetic database for other possible matches. The MSA (alignment of all closely matching sequences) is then fed into the network. Simultaneously, a pair representation and a template are also fed into the the system. Everything is combined using the Evoformer, and fed into the structure module, which then outputs the final representation as a list of coordinates and confidences. Interestingly, this output can be fed back into the network to redo the computation and improve the result.
</div>
</div>

<h4>The inputs and preprocessing layers</h4>

We can see overview of the AF2 system above. The input sequence is a sequence of \\(N_{res}\\) one-hot values, the shape of which becomes \\([N_{res}, 21]\\) according to the supplementary material. A good reference point is the [notebook](https://colab.research.google.com/github/deepmind/alphafold/blob/main/notebooks/AlphaFold.ipynb) which gives a very high level overview of how to run AF2. Sequences are input as strings (i.e. \\(\text{MAAHK}\ldots\\)) which are then searched for in a genetic database. The searching happens in databases for proteins, include [UniRef90](https://www.uniprot.org/help/uniref), [BFD](https://bfd.mmseqs.com/), and others. These sequences are then aligned using a multiple sequence alignemnt [(MSA)](https://en.wikipedia.org/wiki/Multiple_sequence_alignment) and then fed into the network.

The MSA is one of the main objects that's studied by protein folders everywhere. I will get into more detail about the MSA in the next post (the input pipeline of AF2), but basically, MSAs align multiple homologues of proteins together. As a testament to evolution, MSAs basically say that protein A in species A is similar to protein B in species B. Mutations on certain sites have a clear indication that this part of the protein isn't structurally as important (really _functionally_ it's less important, but assuming structure -> function...). 

The little symbols also mean something in the figure. They are cartoon representations of different amino acids in the protein sequence. Clearly, the MSA is a way to extract sequences with similar but still variable motifs. In addition, there's also a pair representation, which is in turn extracted from the amino acid target we're trying to predict (henceforth known as the _target_). It's all a bit overwhelming the first time I looked at it myself, but basically the target is projected from the standard one-hot shape to a feature dimension. Then it's made into a matrix by doing an outer sum (along with position encoding). We'll talk more about this later, but be forewarned, there's a ton of feature engineering going on behind the scenes here! It's actually all very surprising that it even works.

I think the details are gory, but general logic is interesting. There's a lot of emphasis on letting the network extract its own reprentations, but feeding in sort of the correct values dimensionally for the representations. Also, as we will see in the next post, there's also a huge emphasis on stochastic substitutions and deletions, both across the MSA and within it. This is such a risky move, but also very logical given that the dataset is inherently limited.

<h4>The Evoformer</h4>
<div class="imgcap">
<img src="/assets/evoformer.png"
     width="750"
     height="auto">
<div class="thecap">
  Taken from <em>Jumper et. al. Highly accurate protein structure prediction with AlphaFold</em>. The Evoformer module. It takes the MSA and pair representations and uses each to refine the other. The ultimate output will be the first row of the MSA and the pair representation. We can see axial attention at the start of the module, followed by the triangular attention updates. 
</div>
</div>

Ignoring the big block that says recycling at the bottom, we can then move on to the Evoformer, the second part AF2. The Evoformer pipeline is just a set of attention modules that mix and match both the MSA representation and the pair representation. It's actually not incredibly complicated, even though it looks so at first. And it's interesting to see how it interplays between the MSA/pair reps, essentially use one to refine the other until the final output 48 blocks later.

I think Mo Alquraishi had a great blog [post](https://moalquraishi.wordpress.com/2021/07/25/the-alphafold2-method-paper-a-fount-of-good-ideas/) that really went into some detail about the logic behind this part of the network. In particular, he mentioned the interesting back and forth of the MSA and pair reps, and how the Evoformer and structure module (SM) interact at the end.

Regardless of the details, the Evoformer consists of [axial attention layers](https://arxiv.org/abs/1912.12180), a somewhat easy to understand extension of the vanilla transformer architecture. Basically the row-wise attention mixes and matches different sequences together, while the column-wise attention mixes and matches different amino acids across the different sequences together. This is then fed into another module that makes the MSA representation into a pairwise representation using an "outer product mean" operation.

The most novel piece of the Evoformer (from the architecture perspective) is the triangular multiplication/attention pieces. Here we basically interpret the pairwise representation as a distance matrix, and note that distances have to satisfy a triangle inequality. It's interesting that the triangle attention mechanism is used in this way, and that it leads to actual results. As Mo Alquraishi mentions, basically the triangle inequality is suggested rather than rigidly enforced. This seems to be a common theme of the AF2 architecture, where pieces of physics (and in this case mathematics) are merely suggested rather than built into the system.

<h4>The Structure Module</h4>
<div class="imgcap">
<img src="/assets/structure_module.png"
     width="750"
     height="auto">
<div class="thecap">
  Taken from <em>Jumper et. al. Highly accurate protein structure prediction with AlphaFold</em>. The structure module (SM). After 48 Evoformer stages, the SM takes the data and produces a set of 3D atom coordinates. 
</div>
</div>