28-SEP-2022: THIS IS CURRENTLY A WORK IN PROGRESS


# Getting started with Edge Impulse! 

**DISCLAIMER**: This packet is written for the **National Geographic Tech Tutors Workshop** run through [WILDLABS](https://www.wildlabs.net/) (5/6-Oct-2022) and outlines the steps we will take in the workshop to create a audio classifier through the online [Edge Impulse](https://www.edgeimpulse.com/) platform. The instructions presented are based strongly on the accessible and comprehensive [Edge Impulse documentation](https://docs.edgeimpulse.com/docs/tutorials/audio-classification), including their Coursera series ["Introduction to Embedded Machine Learning"](https://www.coursera.org/learn/introduction-to-embedded-machine-learning/home/week/1). Modifications have been made to simplify the pipeline and to add additional explanations or clarifications to aid understanding by a non-technical audience. Dan Situnayake's Tech Tutors epsiode, ["How do I train my first machine learning model"](https://www.wildlabs.net/event/how-do-i-train-my-first-machine-learning-model), is another amazing resource. We strongly recommend that you consult these original sources for more information. 

## Table of Contents 

1. [What is Edge Impulse](#what-is-edge-impulse)
2. [What Edge Impulse does](#what-edge-impulse-does) 
3. [What we will do today](#what-we-will-do-today)
4. [Before we begin](#0-before-we-begin) 

## What is Edge Impulse?


## What we will do today 

In this tutorial, you'll use machine learning to build a system that can recognize when a particular sound is happening—a task known as audio classification. The system you create will be able to recognize the sound of water running from a faucet, even in the presence of other background noise.
You'll learn how to collect audio data from microphones, use signal processing to extract the most important information, and train a deep neural network that can tell you whether the sound of running water can be heard in a given clip of audio. Finally, you'll deploy the system to an embedded device and evaluate how well it works.

## Before we begin 

1. Register for an Edge Impulse account. EXPLAIN 
4. If you want to deploy and test them odel we build, you'll need a supported device: any [mobile smartphone](https://docs.edgeimpulse.com/docs/development-platforms/using-your-mobile-phone) should work.

## The data 

If you were training an algorithm to deploy in the field, you would first want to collect audio data yourself, preferably under field conditions and using the same device that will the be listening for new data and running inference. Unfortunately, we won't be shipping everyone to the African bush today to record our target audio - instead, use this library we've compiled for you. 

This is a balanced dataset (see Introduction), containing HOW MUCH AUDIO divided eveningly among the two classes we want the algorithm to discriminate between:

1. Gunshots
2. Background noises (i.e., "everything else") 

If you listen in on these files, you can see that we compiled a variety of sounds collected under different scenarios: "background noise" contains samples of wind, rain, birds chirping, and other things you might hear in nature; "gunshots" includes blasts from different types of guns, recorded with or without background noise. There's not guarantee that the model will generalize to noises not introduced in the datset, so the more diverse and representative of real-world conditions the dataset is, the better it will perform. 

If you want to learn how to collect and process your own audio, check out the Edge Impulse tutorial on ["Collecting your data"](https://docs.edgeimpulse.com/docs/tutorials/audio-classification#2.-collecting-your-first-data). 

## Designing an impulse

An **impulse** is a WHAT does several things: 
1. it takes the raw audio data and slices it up in smaller windows  
2. then, it uses *signal processing blocks* to extract features from those audio
3. next, it uses a *learning block* to classify new data

Don't worry if this sounds confusing, we'll explain this process in more detail below. We'll then pass this simplified audio data into a Neural Network block, which will learn to distinguish between the two classes of audio (faucet and noise).

### Raw data block

Go to the **Create impulse** tab. You'll then see a raw data block, like shown below. 

PICTURE 

To expand the amound of audio data used to train the model, Edge Impulse will chop 

As mentioned above, Edge Impulse slices up the raw samples into windows that are fed into the machine learning model during training. The Window size field controls how long, in milliseconds, each window of data should be. A one second audio sample will be enough to determine whether a faucet is running or not, so you should make sure Window size is set to 1000 ms. You can either drag the slider or type a new value directly.
Each raw sample is sliced into multiple windows, and the Window increase field controls the offset of each subsequent window from the first. For example, a Window increase value of 1000 ms would result in each window starting 1 second after the start of the previous one.
By setting a Window increase that is smaller than the Window size, we can create windows that overlap. This is actually a great idea. Although they may contain similar data, each overlapping window is still a unique example of audio that represents the sample's label. By using overlapping windows, we can make the most of our training data. For example, with a Window size of 1000 ms and a Window increase of 100 ms, we can extract 10 unique windows from only 2 seconds of data.
Make sure the Window increase field is set to 300 ms. The Raw data block should match the screenshot above.


   - Here, we'll use the "MFE" signal processing block. MFE stands for Mel Frequency Energy. This sounds scary, but it's basically just a way of turning raw audio—which contains a large amount of redundant information—into simplified form

