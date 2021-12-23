---
layout: post
comments: false
title:  "A high level overview of Alphafold 2"
excerpt: "A bird's-eye view of AF2."
date:   2021-12-23 22:00:00
mathjax: true
---

<h3>A Bird's-Eye View</h3>

Any complex ML pipeline needs both global and local understanding to really get a feel for what's going on. In the case of Alphafold 2 (AF2), we need to understand what the inputs/outputs are and how they are processed at a high level. In the [the article] (https://www.nature.com/articles/s41586-021-03819-2) published on AF2, we see the central figure 1,

<div class="imgcap">
<img src="/assets/alphafold_overview.png"
     width="350"
     height="auto">
<div class="thecap">
  Taken from <em>Jumper et. al. Highly accurate protein structure prediction with AlphaFold</em>. The input sequence is an amino acid sequence. This sequence is then searched through a genetic database for other possible matches. The MSA (alignment of all closely matching sequences) is then fed into the network. Simultaneously, a pair representation and a template are also fed into the the system. Everything is combined using the Evoformer, and fed into the structure module, which then outputs the final representation as a list of coordinates and confidences. Interestingly, this output can be fed back into the network to redo the computation and improve the result.
</div>
</div>

