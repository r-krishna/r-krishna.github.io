---
layout: post
title: "Building Learning Machines"
date: 2024-02-05
---


#### Know your goal 

When starting to build a ML algorithm, you have to know the answer to "what is success?". There are likely some parameters that limit your project such as how compute intensive training the network is, how compute intensive running inference is and what types of input and output data structures you expect from the network. Ideally, you also have a "validation" and "test" set that accurately maps to the types of inference tasks that you expect to use with the network and a reasonable idea of what accuracy threshold is necessary for the algorithm to be "useful." This could constitute a strong baseline, likely comprised of how one would approach the problem with the current available tools. 

#### How to engineer learning
I think there's a few key practices we can learn from software engineering about building learning systems.
- *Velocity matters*: Engineering code that can be edited safely and quickly is one of the most important parts of building learning systems. It is often unclear a priori what changes are going to improve neural network performance, and being able to try a large amount of ideas allows you to learn what is important and what is not. Also, having performant training code that allows you to test and iterate quickly will definitely improve the model quicker than any 1 genius idea. 
- *Listen to Users*: When setting up a deep learning codebase, it is easy to set up a success condition and never stray from it but I argue that you should be open to updating your conception of what "success" is based on user feedback. You might find that the problems you find as a ML practioner are completely different than those that are using the network at inference time find. Depending on your goals, you might want to update your conception of "success."
- *Maintain backwards compatibility*: As you are building and deploying ML models, you will learn from your users and you might find that your answer to "what is success" changes over time. In that case, being able to move backwards is extremely important to allow you to . Additionally, maintaining inference backwards compatibility allows users to benefit from the latest bugfixes found from development of newer models on older models as well. 
- *Write tests*: Many bugs are silent in neural network, either the network will learn to ignore a feature that is actually useful or will learn a shortcut due to a bug in your code. 


#### Build your learning system
Your learning system probably has three moving parts; data, network architecture and loss function. 

- Data: Data is a tricky part of the pipeline. First, 
    1. You need to store the raw data in some way on disk. Version control of this raw data can be rather tricky. In my experience, the raw data usually stays held on disk somewhere and every time the raw data is updated (eg more data is downloaded from the internet), a separate directory is made to store the new data. I personally prefer to compose the datasets from multiple sources in code so that each experiment that involves changing the dataset can be captured in version control with git (instead of just adding the new data into the same directory as the old data). 
    2. The second step of dataset preparation is preparing the raw data into the network inputs. This could be some tokenization scheme or computing auxiliary features or values that are involved in loss calculation. Here, it is very important that the code that takes the raw data from disk can also be used at inference time since one of the biggest bugs that arises in neural networks is mismatch between training and inference tasks. 
- Network architecture: In my opinion, network architecture is an extremely empirical task so one of the most important parts of neural architecture search is maintaining backwards compatibility and being confident that the experiment you did succeeded or failed for the reasons that you think they did (so writing tests, even before training).  
    1. You might want to test out the invariant or equivariant properties of your network (or lack therof)
    2. Making sure all parameters are used for loss calculation.
    3. Making sure that the code you have written only change the property you want to test in the network. For example, if you are experimenting with different positional encoding schemes, you would test that operation is the only one that differs between the two networks. 
- Loss function: Testing difference loss functions or "problem setups" is also extremely important for neural network development. For example, is diffusion style training or flow matching training better for your problem? Here again, having a principled way to compare the models on a validation and test set is extremely important. From an engineering perspective, how do you set up the models so that they can be evaluated and compared fairly? This will vary from problem to problem, but likely involves maintaining a stable codebase where data can be run through multiple models simultaneously for evaluation. The other concern is always the numerical stability of your loss function, and whether there are better loss functions that optimize more smoothly. 

#### The model is smarter than you, but you should be learning too
Ideally, in a good learning system both you and the network should be learning. The network will likely (hopefully?) outperform you at the task once fully trained but you should learn:
    1. Details about the data. How does the data affect how the network learns?
    2. What the most important components of the network are, where is most of the learning happening?
    3. What are the numerical or mathematical properties of the loss that affect the network? Are there certain numerical instabilities in training or does the loss function need to preserve certain symmetries for the network to learn?

Throughout this post, I have described how I think about organizing machine learning codebases to optimize improvements but I am sure there is a lot that I am missing or have not learned yet! Feel free to reach out if you have any suggestions or want to talk about testing or building ML research codebases!


