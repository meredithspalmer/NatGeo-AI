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

Don't worry if this sounds confusing, we'll explain this process in more detail below. 

### Raw data block

Go to the **Create impulse** tab. You'll then see a raw data block, like shown below. 

PICTURE 

To expand the amound of audio data used to train the model, Edge Impulse will chop the raw data up into many little samples. **Window size** tells Edge Impulse how long each slide should be in milliseconds. Here, set the window size to **1000 ms**, or one second. This should be long enough for the algorithm to determine whether the sound is a gunshot or background noises. To maximize the number of samples from each piece of raw data, windows can overlap. **Window increase** controls how long after the first window the subsequent window starts. Set this value to **100 ms**. While overlapping windows will contain similar data, each window is still a unique audio sample. 

### Processing block

Click **Add a processing block** and choose the **'MFE' block**. 

MFE stands for Mel Frequency Energy: this is just a way of turning raw audio—which contains a large amount of redundant information—into simplified form. 

### Learning block

Next, click **Add a learning block** and choose the **'Classification (Keras)'** block. 

WHY?? 

Click **Save impulse**! Your impulse should look like this: 

PICTURE 

## Configure the impulse

Next, we can start tweaking the individual blocks to optimise the model. Let's start with the **Processing block**. Click on the MFE tab in the left hand navigation menu. You'll see a page that looks like this:

IMAGE

The processing block transforms each audio sample into **spectrogram**, which can be seen in on the righthand side of the page. A spectrogram is a visual way of representing how the frequencies of an audio signal vary through time. Rather than "listening" to the audio files, the machine learning algorithm we are training is rather trying to distinguish visual patterns between spectrograms. As Edge Impulse provides pretty sensible starting parameters, we can leave the default settings and start generating the spectrograms on which we'll train our algorithm. To do this, click the **'Generate features'** button at the top of the page, then click the green **'Generate features'** button. This process can take a little while to complete. 

When the files have been processed, we can now use the feature explorer to visualize our dataset. By mapping the features into 3D space, we can see how well our different classes separate (which, if so, bodes well for training our ML classifier!). 

## Configure the neural network 

Our algorithm will take the spectrograms produced by the MFE as input, and try to map this to one of two classes: 'gunshot' or 'background'. The input will go through layers of virtual 'neurons', which filter and transform the input data to extract distinguishing features. Ultimately, the algorithm will procude two values: the probability that the input represents a gunshot and the probability that the input represents background nature noises.

During the **training phase**, the state of these 'neurons' are tweaked and refined so that they layers ultimately transform the input into the correct output. This is an iterative process, where the model is fed samples of training data, outputs are evaluated, adjustments are made, and the process is repeated thousands and thousands of times until the correct answers are produced (or you give up - sometimes, there are problems that can't be solved by ML!). 

How the 'neurons' are arranged into layers is called an **architecture**. There are many different kinds of ML architectures that have been developed for different kinds of tasks. Here, we'll stick with Edge Impulse's defualt architecture. 

In the left hand menu, click on **'NN Classifier'**. You should see the following: 

IMAGE 

Click **'Start training'**; the process may take a few minutes. When it's complete, you'll see the **Model panel** appear at the right side of the page:

IMAGE 

> # YOU DID IT!  ## MAKE THIS AN IMAGE INTEAD 

Congrats, you've trained your first machine learning model! 

## Okay, but what does this mean? 

Congratulations, you've trained a neural network with Edge Impulse! But what do all these numbers mean?
At the start of training, 20% of the training data is set aside for validation. This means that instead of being used to train the model, it is used to evaluate how the model is performing. The Last training performance panel displays the results of this validation, providing some vital information about your model and how well it is working. Bear in mind that your exact numbers may differ from the ones in this tutorial.
On the left hand side of the panel, Accuracy refers to the percentage of windows of audio that were correctly classified. The higher number the better, although an accuracy approaching 100% is unlikely, and is often a sign that your model has overfit the training data. You will find out whether this is true in the next stage, during model testing. For many applications, an accuracy above 80% can be considered very good.
The Confusion matrix is a table showing the balance of correctly versus incorrectly classified windows. To understand it, compare the values in each row. For example, in the above screenshot, all of the faucet audio windows were classified as faucet, but a few noise windows were misclassified. This appears to be a great result though.
The On-device performance region shows statistics about how the model is likely to run on-device. Inferencing time is an estimate of how long the model will take to analyze one second of data on a typical microcontroller (here: an Arm Cortex-M4F running at 80MHz). Peak memory usage gives an idea of how much RAM will be required to run the model on-device.
