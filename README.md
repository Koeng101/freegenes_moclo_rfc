# FreeGenes MoClo RFC

Authors: Keoni Gandall

Date: 2019.12.04

It's well known that DNA sequencing and DNA synthesis costs have been dropping exponentially for the past few years. From an outside perspective, these graphs are wonderful, and demonstrate that "progress is being made". However, most of the cost of building DNA is not in DNA synthesis, but in DNA build. [Twist Oligo pricing vs Twist Synthesis pricing]

Most used DNA fragments are not very unique. Out of the 77,602 plasmids at Addgene, 1/8 are derived from only 8 different vector backbones[1]. In fact, 66% of the Addgene collection uses the Ampicillin Resistance gene for selection[2]. iGEM is similar: if you took counts of all genes that have ever been reused in the iGEM collection, only 48 DNA parts account for more than 50% of those reuses[3]

In essence, most DNA parts that are used in building DNA are reused and synthesis costs are dwarfed by cloning costs. If we want to drop the overall cost of building with DNA, there is an easy solution here: standardize the most used DNA parts in a way that is designed to be cheaply and simply automated.

## DNA assembly methods

To achieve that drop in DNA build cost, we need our assembly method to have 3 properties:
1. Must allow for Standardization and interoperability
2. Must be flexible enough to accomodate most DNA builds (fusion proteins, different RBSes, different backbones)
3. Must be simple and efficient to automate (ie, no gel purification)

Homologous recombination cloning, like Gibson, Yeast Assembly, and the Ligase Cycling Reaction, are very flexible - but they can't be standarded effectively, since they require oligos to define overhangs. On the other hand, the BioBricks Assembly method is standardized, but isn't very flexible, with large scar sequences and single directionality of parts. Both systems are not simple and efficient to automate: Gibson requires oligo synthesis, while BioBricks Assembly can only assemble 2 parts at a time. 

In the recent years, a new standardized DNA assembly method has been developed called "MoClo". MoClo assembly is standardized, simple to automate, and efficient, and as a standard, already enjoys widespread adoption. However, MoClo lacks some flexibility options that has led to fragmentation of the Standard. FreeGenes minorly extends the MoClo assembly standard to add 2 useful features: Seamless C terminal fusion and Recursion.

## C terminal Fusion
There are two conflicting needs when it comes to C terminal fusions: on one side, in order to know that a protein is functioning like it would in its native context, you need to be able to express the gene as it would appear with its native amino acid sequence. In addition, you may want to purify the protein using a His tag, which would usually require a fusion to the C terminus of a protein. In any other standardized assembly method, these two objectives can not be reconciled - however, we have worked out a way to do just that. 


### C terminal fusion theory

FreeGenes MoClo takes advantage of the unique properties and sequence requirements of the 7bp Type IIS cutter SapI to enable seamless C terminal fusions

We can imagine the following DNA sequence encoding a protein (in this case, the protein MHELLQWQRLD):

`ATG CAT GAA TTG CTC CAG TGG CAA CGC CTA GAC TAA`

For now, let's only focus on the last 3 codons:

`... CTA GAC TAA`

Now, let's add the suffix for MoClo that would define this part as a CDS:

`... CTA GAC TAA GCTTNGAGACC`

In this case, `GCTT` is the overhang definition / cut site, with `NGAGACC` being the BsaI recognition sequence. SapI is an interesting enzyme, recognizes a 7 base pair site and cuts a 3 long overhang 1 base pair away, demonstrated in following sequence:

`NNN NGAAGAGC`

Let's try to fit the SapI cut site so that it cuts the last codon in any given protein. The last 2 bases, `GC`, overlap with the MoClo suffix, and we can change the stop codon so that the first 2 bases fit in. Finally, we can simply add the 3 internal base pairs.

`... CTA GAC T'GA AGA GC'TTNGAGACC`

We have now successfully overlapped SapI with both the stop codon and the MoClo suffix, and it cuts the last coding codon. Finally, let's standardize the final codon, so that instead of 61 different overhangs, there are only 20.

`... CTA GAT T'GA AGA GC'TTNGAGACC`

The last codon of any given protein is standardized according to the following table. The codons choosen were choosen manually, trying to preserve the least overlap for each codon while avoiding rare codons.

| AA | Codon |
|:--:|:-----:|
|  M |  ATG  |
|  W |  TGG  |
|  F |  TTT  |
|  L |  CTG  |
|  I |  ATT  |
|  V |  GTG  |
|  S |  TCC  |
|  P |  CCA  |
|  T |  ACC  |
|  A |  GCC  |
|  Y |  TAC  |
|  H |  CAT  |
|  Q |  CAG  |
|  N |  AAC  |
|  K |  AAG  |
|  D |  GAT  |
|  E |  GAG  |
|  C |  TGC  |
|  R |  CGC  |
|  G |  GGC  |
|  * |  TGA  |

### C terminal fusion practice




[1] https://twitter.com/koeng101/status/1183584639488839680

[2] https://twitter.com/koeng101/status/1183585287781408768

[3] ./igem_power_law/igem.ipynb


