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
     width="500"
     height="auto">
<div class="thecap">
  Taken from <em>Jumper et. al. Highly accurate protein structure prediction with AlphaFold</em>. The structure module (SM). After 48 Evoformer stages, the SM takes the data and produces a set of 3D atom coordinates. Unlike the Evoformer which has unique weights per layer, the SM has a single set of weights and the refinement happens over 8 passes of the layer. A common theme is to refine predictions from the model over and over so that each pass improves the protein structure.
</div>
</div>

The structure module (SM) is the most geometric and probably the most hand-designed part of the AlphaFold 2 system. The Evoformer spits out a pairwise output \\(\mathbf{z}\_{ij}\\) as well as the MSA representation \\(\mathbf{m}\_{si}\\). Here \\(i,j\\) stand for residue indices and \\(s\\) stands for the sequence alignment index. The input to the SM is the first row of \\(\mathbf{m}\_{1i}\\) (they use 1-based indexing in the supplementary material) and \\(\mathbf{z}\_{ij}\\). The SM takes the two inputs and produces a couple of predictions, each of which is used as a loss or for further refinement.

The representation of a protein that AF2 actually uses is somewhat complex. It's a mix-and-match of various representations we discussed before. Each residue defines a local reference frame, which we will represent as \\(T\_i\\), where \\(i\\) runs over all residues. Here the reference frame we're referring to is defined by the plane of the residue, where the plane is composed of the same set of \\(\text{N}\text{C}^\alpha\text{C}\\) backbone atoms. The reference frame \\(T\_i\\) can be parameterized by \\(T\_i = (\mathbf{R}\_i, \mathbf{t}\_i)\\), where \\(\mathbf{R}\_i\\) is the rotation from the local to a standard global frame, and \\(\mathbf{t}\_i\\) is the translation from the local to global frame. So any vector \\(x\_{local} \in \mathbb{R}^3\\) can be transformed to \\(x\_{global} = \mathbf{R}\_i\mathbf{x}\_{local} + \mathbf{t}\_i\\). This is known as a _residue gas_, a schematic of which is shown below.

<div class="imgcap">
<img src="/assets/residue_gas.png"
     width="500"
     height="auto">
<div class="thecap">
  A residue gas is just a free set of amino acids, each of which is free to rotate and translate in many distinct ways. The triangles represent the frames \\(T\_i\\), while the green circles represent rotational degrees of freedom within each residue that are predicted by AF2.
</div>
</div>

Along with the residue gas representation, which handles the global protein folding problem, there's also the local protein folding problem. Inside a residue there are different degrees of freedom, and for each degree of freedom there are distinct torsion angles that AF2 predicts. From my previous learning on proteins, I was surprised that there are more degrees of freedom than I thought. The use of these extra degrees is to pin down whatever atoms that aren't free from the residue gas representation. We'll go into more detail about these in a later post.

Given this set of predictions, the SM can then calculate the exact position of every atom in a protein. This atomic position is also relaxed by the [AMBER](https://ambermd.org/tutorials/basic/tutorial1/section5.htm) MD simulation. This corrects any latent problems with the system. In addition to calculating positions, the SM also produces a very useful signal, known as the predicted lDDT (local Distance Difference Test) score. This is sort of a measure of confidence that AF2 has for each residue in the protein. 

<h4>Metrics and Scores</h4>

Like any good ML problem, the ultimate say on how well a model performs is based on scores. For protein folding, the most obvious score is the standard RMSD, the root-mean-squared-deviation of each atom's predicted position from it's observed position after alignment. In order to do alignment, we have to choose a common frame between the two proteins, which is a non-trivial problem. Also, RMSD is dominated by large errors in atom positions, which might not be relfective of overall quality. This is easy to see from a standard formula, since \\(\frac{1}{N}\sum\_i \mathbf{x}\_i-\hat{\mathbf{x}}\_i)^2\\) is essentially dominated by the largest deviations. Here \\(N\\) is the number of residues, and \\(\mathbf{x}\\) is usually the atomic position of the model, while \\(\hat{\mathbf{x}}\\) is the measured position.

One way to fix this is to take a coverage of only a portion of residues, and throw out the rest. This is the \\(\text{RMSD}\_{95}\\), are reported in the AF2 paper, where only \\(\text{C}^\alpha\\) atoms are considered. Here AF2 scores a 0.96Å \\(\text{RMSD}\_{95}\\). 

Another method of scoring is known as GDT (global distance test), and formally I believe \\(\text{GDT}\_{TS}\\) is used (TS stands for total score). Here, essentially what is counted is the percentage of atoms within a distance cutoff. More details can be found [here](https://foldit.fandom.com/wiki/GDT).

Probably the most important metric for AF2 is lDDT (local Distance Difference Test), mostly because it is also one of the predictions output by the network. As the name suggests, lDDT is a local score, so it won't capture global folding problems. The exact reference can be found [here](https://www.ncbi.nlm.nih.gov/labs/pmc/articles/PMC3799472/). As a summary, one chooses an atom as a anchor atom, and an inclusion radius \\(R\_o\\), typically set to 15Å. Then the distance from the anchor atom to all atoms in that radius _not_ part of the residue of the anchor atom are calculated. These distances \\(\hat{L}\_i\\) are then compared with the predicted distances \\(L\_i\\). If they are within a given threshold, then the prediction is considered correct, otherwise it is incorrect. The average lDDT over all \\(\text{C}^\alpha\\) atoms is usually reported. The final measure is usually on a 0-100 scale, and can be split into a per-atom or global average.

<h4>Losses</h4>

Unlike metrics, which are not differentiable (well, RMSD is), we need differentiable losses, and AF2 comes replete with losses. I think the careful application of losses is one of the most important parts of AF2, and there is a potential for losses to be glossed over when looking at the network. The immense complexity of the intersecting losses is a testament to how much optimization has gone into the system, since for most ML projects, these losses are often very simple and standardized.

Part of the reason for so many losses is that the output of the network includes both the frames \\(T\_i\\) and the sidechain torsion angles \\(\chi\\). In addition, the loss is applied repeatedly. For each pass of the structure module, there's a loss (since it outputs a prediction). There's also a final loss after 8 passes through the structure module. In this way, the structure module is trained as a refiner. In addition, there are also losses applied to just the Evoformer part of the stack. This includes the MSA reconstruction loss and the distogram loss. I won't go into too much detail about how these are applied here, as they are somewhat nontrivial. Needless to say, the MSA reconstruction loss comes from [BERT masking](https://d2l.ai/chapter_natural-language-processing-pretraining/bert.html#masked-language-modeling) and the distogram loss from the [original Alphafold paper](https://www.nature.com/articles/s41586-019-1923-7).

The primary novel loss is known as the frame aligned point error (FAPE) loss. The FAPE loss is clearly inspired by the lDDT metric described above. Essentially we take an anchor atom denoted by index \\(i\\), and look at the frame \\(T\_\mu\\). Then for every other atom \\(\mathbf{x}\_{\nu}\\), we transform that atom's position into the anchor atom's frame. Here \\(\mu, \nu\\) run over atom positions instead of residues. The computed position is denoted \\(\mathbf{x}\_{\mu\nu} = T\_{\mu}^{-1}\mathbf{x}\_{\nu}\\). We then do the same for the experimentally determined atom frame, \\(\hat{\mathbf{x}}\_{\mu\nu}^} = \hat{T}\_{\mu}^{-1}\hat{\mathbf{x}}\_{\nu}\\) and then compute the L2 norm. We then average over the full set of frames/atoms \\(N_{frames} \cross N_{atoms}\\). Technically, I believe only \\(\text{C}^\alpha\\) atoms have frames computed, but ideally all atoms have such a local frame.

In addition to FAPE, there is also a torsional loss which is somewhat standard. Finally there are lDDT and TM score losses for the predicted lDDT and TM scores. These losses more or less binned L2 losses, which are common in AF2. Generally, when predicting a continuous variable like lDDT, there are two options. One is to use a L2 regression loss, which is often the most intuitive. However, it is often better to bin the loss values and use a one-hot representation to determine the values. This gives the model the ability to express uncertainty. Mathematically, given a range \\(\[y_{min}, y_{max}\]\\), we can split the range into \\(N\\) bins. Then we use a softmax \\(\mathbf{p} = \text{softmax}(\text{Linear}(\mathbf{a}))\\) to predict which bin the true value is in, with the loss being a standard cross entropy loss.

<h4>Recycling</h4>

Looking back at the first overview figure, there's a big arrow called recycling. One of the innovations of AF2 is that the outputs can be reused as inputs to the network. So many of the outputs (atom positions, MSAs, distograms) can be reused as inputs to the system. The recycling nature of AF2 makes it essentially a refiner. The refinement happens first in the structure module, which uses shared weights, and also gradually in the Evoformer, which then sees the outputs of the structure module in the second and third passes. This refinment structure is reminiscent of an RNN, and seems to be a very useful and generic procedure to improve neural network positions. Whether it can be extended to other networks is an open question, but one with some scientific merit (at least, I think so).

<h4>Conclusions</h4>

<div class="imgcap">
<img src="/assets/ablation.png"
     width="500"
     height="auto">
<div class="thecap">
  The ablation of techniques in AF2. The two plots show GDT and lDDT on the x-axis and the various components on the y-axis. The sheer number of techniques is not even represented by what was ablated. In fact, many more small pieces are present that have not been tested (at laest, publically). It seems like no one piece really contributes to AF2's performance.
</div>
</div>

Alphafold 2 is a massive system. It includes hundreds, if not thousands, of tiny, painstakingly small optimizations, as well as a few big ideas. Probably the most interesting plot and result for me is not that the system does so well, but that no particular piece is really the crux of the solution. Instead, it seems like interaction effects are dominant. When both invariant point attention and recycling are removed, there is a huge performance drop. But neither invariant point attention nor recycling by themselves are as important. In other words, the sum of techniques are really what works.

This means that there can potentially be spaces of techniques that we just simply haven't explored. There's no guessing which techniques are more/less important in combination with others. So many little decisions are responsible for an ultimately state-of-the-art network. How much of it is accident and how much of it is intuition? That's something that will hopefully become clearer as we dive deeper into the network. In the next section, we will look at the input preprocessing and chain of AF2. Then we will look at the Evoformer, and finally the structure module. Note that the exact specification of every detail is better left to code, but we will take a high level look at things. Also as we dive in, hopefully we can write some actual code!