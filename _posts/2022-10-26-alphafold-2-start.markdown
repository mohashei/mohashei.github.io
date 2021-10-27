---
layout: post
comments: false
title:  "A series of posts to try and understand Alphafold 2."
excerpt: "An attempt to take a deep dive into a protein folding neural network from a beginner's point of view."
date:   2022-10-26 22:00:00
mathjax: true
---

<h2>Introduction</h2>

I've been fascinated by using data to figure out complex problems in physics, chemistry, and biology for many years. In my PhD, we used data to characterize the statistics of [slip avalanches](https://en.wikipedia.org/wiki/Dislocation_avalanches). The problem was that the analysis was often simplistic and we were going off the cuff with a lot of data that we used. One of the things I learned though, is that even complex phenomena can be broken down into simple terms if we look at things correctly. So when [Alphafold 2](https://www.nature.com/articles/s41586-021-03819-2) came out and stunned basically everyone, I knew immediately that I wanted to understand the inner workings. Luckily I had experience in training NNs, as well as training in chemistry/physics.

The goal of this post and hopefully subsequent posts is to try and understand the network in detail, why it works, or at least, why it might work. There are plenty of other blog posts on this subject, and the primary purpose of this post is for my own understanding of the subject matter.

Some prerequisits: Read [this post](https://fabianfuchsml.github.io/alphafold2/) by Fabian Fuchs to get a grasp of the problem at hand without the need for superfluous detail. I want to restate some of the facts here since it makes things clearer.

Proteins are composed of molecules known as [Amino Acids](https://en.wikipedia.org/wiki/Amino_acid). Suffice to say, these are complicated molecules in their own right, but the common trait of each of them is the  \\(-\text{NH}_3^+\\) group attached to them. One way to think about amino acids is like lego blocks for proteins that have affinities to one another, as well as affinities to and away from the surroundings (i.e. water). These affinities are the root cause of the protein folding problem, and the big reason why proteins fold in startling and unexpected ways.

The long and short of it is that 20 different amino acids are used in the production of proteins. These 20 amino acids are coded by 3 base pairs in DNA. The 3 base pairs get translated into RNA, and then a ribosome will take the RNA strand and translate it into a protein. The full process is pretty [amazing](https://www.youtube.com/watch?v=TfYf_rPWUdY) and I know way less about it than I should. I should also note that ribosomes are made up of proteins (and RNA) and are themselves constructed by other proteins (over 200!). This dizzying amount of coordination is a real triumph of biology, and it's humbling that we struggle to even figure out the structure of proteins by comparison.

The 20 amino acids are all abbreviated conveniently by capital letters to form a protein alphabet ("ARNDCQEGHILKMFPSTWYV-", the "-" is unknown). So the linear chain of amino acids can be some complicated combination of these letters. This linear chain then somehow becomes a blob which performs its function so well that it can be distinguished from millions of other blobs floating around in a cell.

<h2>Physics of Protein Folding</h2>

The physics of protein folding is remarkably simple at first glance. Proteins composed of these complicated amino acids actual reduce down to relatively few degrees of freedom per amino acid. These amino acids are chemically bonded together with [polypeptide bonds](https://en.wikipedia.org/wiki/Peptide_bond) which essentially result in a backbone that forms long chains of carbon and nitrogen atoms. Generally the sequence of carbon and nitrogen is \\(\text{N}^1\text{C}_\alpha^1\text{C}^1\text{N}^2\text{C}_\alpha^2\text{C}^2\ldots\\). The middle carbon is the denoted \\(\text{C}_\alpha\\) and the various side chain groups hang off of this carbon. The sequence repeats till the end of the protein, and basically, the side chains (i.e. the things attached to the the amino acids that distinguish them) are relatively fixed based on angles of the backbone.

By angles of the backbone, I mean the torsion angles the backbone atoms have with each other. Since each backbone atom is attached to many other atoms besides the backbone, the torsion angles essentially determine the way the protein is shaped. The torsion angle between the carbon and nitrogen is known as \\(\omega\\), between the nitrogen and the alpha carbon it is \\(\phi\\), and between the alpha carbon and the alpha it is \\(\psi\\). It turns out that because of the double bond resonance structure of the carbon nitrogen bond, the angle \\(\omega\\) is almost always \\(180^\circ\\) (i.e. a flip). The follwing diagram, taken from a classic paper, shows this in action.

<div class="imgcap">
<img src="/assets/amino-acid.png">
<div class="thecap">
  This is taken from Ramachandran, G.N.; Sasiskharan, V. (1968). <em>Conformation of polypeptides and proteins</em>.
</div>
</div>

