---
layout: post
title: "Understanding the Influence of pegRNA sequence on Prime Editing outcomes"
date: 2022-02-07
---
*This was written as a reflection for BIOC 541. I have no intentions of conducting this study, if you do and have cool results, let me know!*

In previous work <a href="https://www.sciencedirect.com/science/article/pii/S0092867421010655?via%3Dihub">(Chen et al. Cell 2021)</a>, it was shown that silent mutations could improve prime editing efficiency. In this case, targeted silent mutations that reduced G-C to C-G mutations that would be targeted by the MMR pathway. While this has worked well, there has never been a systematic investigation of silent pegRNA mutations and their effect on prime editing outcomes. Since there are probably other pathways that reduce prime editing efficiency, having a set of design rules for pegRNAs could improve prime editing outcomes. 

I propose creating a large library of pegRNAs that all edit a single locus in HEK293 cells. I would set up 384 well plate with each well having a different pegRNA. The locus can be amplified by PCR with a barcode for each well and then sequenced by NGS. The sequencing should have the well identified and the outcome of the editing. Using this information, I would gather motifs that performed well and poorly and try to find any patterns. If patterns are apparent, more pegRNAs can be designed using the rules that were learned from the initial experiment to validate that they are indeed design rules that could be used. After validation at a single locus, the design rules can be validated by designing  pegRNAs for different loci and cell lines.

I expect that there are certain motifs that contribute to poor prime editing outcomes. Some of these motifs might uncover other pathways that compete with successful prime editing outcomes other than MMR. I expect a randomized approach will be able to find patterns between sequence and prime editing outcome given a large enough dataset. With sufficient data, I could see an attention based model being trained on these short sequences that makes a coarse prediction of efficiency and could potentially be inverted to design efficient pegRNAs. 

Motifs that have poor prime editing outcomes could be combined with a CRISPRi experiment to further understand what pathways are involved in preventing those editing experiments from integrating new genes. I think this approach has the potential to both improve the prime editing method and improve our understanding of the underlying mechanisms. 


