---
layout: post
title: "Are ResNets Antibody Surface Learners?"
date: 2021-05-27
---

**Introduction**

Antibodies are Y-shaped proteins that humans use as a part of the adaptive immune system. Antibodies can identify new foreign invaders in humans and neutralize them with very high specificity. These properties attracted drug hunters and antibodies are currently one of the fastest growing category of medicines with 100 antibody drugs approved by the FDA. Despite all of these successes, the discovery and engineering of therapeutic antibodies rely on many unscalable practices such as immunizing animals with antigens and “guess and check” methods to optimize sequences. Given the prevalence of antibody structural data, computational approaches such as deep learning are promising methods to improve the antibody discovery and engineering process.

Deep learning has recently catalyzed many breakthrough successes in the world of protein structures, replacing physics based models. Until recently, the protein structure prediction world adopted many approaches from the image processing world such as the use of convolutional neural networks and more specifically ResNet architectures (AlphaFold1, Raptor-X, trRosetta). More recently, attention based mechanisms have been used both on unsupervised tasks (TAPE, UniRep etc.) on protein sequences and supervised tasks on protein structures (AlphaFold2).

These advances are extremely exciting but when we want to design new antibody therapeutics we care more about the properties of the molecule than its structure. For example, a good medicine should bind its disease target while also being soluble and not toxic. When considering these secondary characteristics, the physical and chemical properties of amino acids on the surface of the protein are extremely important because they control interactions with other molecules. Currently, these are calculated by bioinformatic pipelines that look for the most similar solved structure and then adapt that structure to make a model called a homology model. This process is time consuming with each sequence taking on the order of minutes to make a model. 

Antibodies have a special structure that is slightly different from the use cases of AlphaFold and other structure prediction algorithms. The two upper arms of the Y-shape are called the Fv (or fragment variable) which are responsible for identifying new antigens and binding them. The stalk is called the Fc (fragment crystallizable) and is responsible for attracting other immune cells to destroy the antigen. The Fv specifically is made out of two proteins that associate with eachother called the heavy chain and the light chain (VH and VL respectively). All of these pieces work together in concert to protect us against pathogens!

Here, I’m going to describe a small project I did last year that uses a ResNet architecture to predict the solvent exposed surface area (SASA) of each amino acid in a Fv. This approach would be much quicker than building a full homology model and thus allow you to test more potential sequences to find the optimal sequences to test in lab. This has applications in both antibody optimization and could potentially be used in conjunction with other tools to design antibody sequences from scratch on a computer. 

![Antibody Surface](/images/5d71_surface.png)

Image of an antibody Fv surface. We want to predict how much surface area each residue contributes to the entire surface.

**Model Architecture**

The problem was conceptualized as a classification problem where every amino acid was to be bucketed into one of 25 buckets of normalized SASA values increasing linearly from 0 to 1. The model has 25 1D ResNet blocks with a dropout layer (p=0.2) and a 1d convolutional layer to create the output logits. 

![Model Architecture](/images/model_architecture.png)

**Dataset**

All structures with a paired VH/VL domain with at most 99% sequence similarity and resolution above 3 Angstroms were selected for dataset. The sequences were truncated to only include the variable region that is known to bind antigens. The sequences in the RosettaAntibody benchmark were removed from the training dataset and used as a test set. Of the remaining structures, 95% of the structures were used as a training set and the remaining set was the validation set. The validation set was never seen during training. The input sequences were one hot encoded with an additional feature indicating where the heavy chain ends and the light chain begins. The SASA values were calculated using the freeSASA python package which used the Shrake-Rupley algorithm which rolls a “ball” over the surface of the structure to find the surface area of each residue that is in contact with the external solvent. The values were normalized to the maximum residue solvent exposure (defined as the exposure of Ala-Xxx-Ala peptide). Any values that were above 1 were set to 1 to prevent the model from learning features about outliers. 

**Model Training**

The model was trained for 30 epochs with a batch size of 4 and the epoch where the training and validation loss were closest was chosen for evaluation. The Adam optimizer was used with an initial learning rate of 0.01 and reduced when the model was plateuing. The outputs were normalized with the Softmax function and the bucket with the highest probability was chosen as the model prediction when evaluating the model against the experimental structures. 

![Train Loss](/images/train_loss.png)

This model was trained with vanilla pytorch on my personal AWS instance on a NVIDIA k80 GPU in about 17 minutes. Inference was also performed on the same machine. I have since migrated to using pytorch lightning for my training to avoid pytorch version and hardware dependency issues. 

**Overall Model Performance**
The model had a 0.45 weighted average precision on the RosettaAntibody benchmark set. The model had significantly higher predictive power on buried positions than exposed positions. 

![Test Confusion](/images/test_confusion.png)

