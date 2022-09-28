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

Congratulationss, you've trained your first machine learning model! 

## Okay, but, um, what now? 

Training data (20%) 

Run on training data to validate model/evaluate how well model is performing; the **Last training performance panel** displays the results of this validation, providing some vital information about your model and how well it is working.

On the left hand side of the panel, **Accuracy** refers to the percentage of data samples that were correctly classified. The higher number the better, although an accuracy approaching 100% is unlikely, and is often a sign that your model has overfit the training data. -- EXPLAIN -- For many applications, an accuracy above 80% can be considered very good.

The **Confusion matrix** is a table showing the balance of correctly versus incorrectly classified windows. EXPLAIN 

## Classifying new data

The numbers we just interpreted tell us how well the model works on training data; next, we need to test the model on data it has never seen before to ensure that the model is not overfit to the training data. 

To capture live data from your  device and immediately attempt to classify it. To try it out, click on **Live classification** in the left hand menu. Your device should show up in the **'Classify new data'** panel. Capture 5 seconds of background noise by clicking **Start sampling**:

IMAGE

The sample will be captured, uploaded, and classified. Once this has happened, you'll see a breakdown of the results:

IMAGE 

Once the sample is uploaded, it is split into windows–in this case, a total of 41. These windows are then classified. As you can see, our model classified all 41 windows of the captured audio as noise. This is a great result! Our model has correctly identified that the audio was background noise, even though this is new data that was not part of its training set.

## Model testing 

Next phase is ... you got it, more testing! That's where the **Model testing** tab comes in. If you open it up, you'll see the sample we just captured listed in the Test data panel:

IMAGE 

In addition to its training data, every Edge Impulse project also has a test dataset. To use the sample we've just captured for testing, we should correctly set its expected outcome. Click the ⋮ icon and select Edit expected outcome, then enter noise. Now, select the sample using the checkbox to the left of the table and click Classify selected:

IMAGE?? 

Ideally, you'll want to collect a test set that contains a minimum of 25% the amount of data of your training set. So, if you've collected 10 minutes of training data, you should collect at least 2.5 minutes of test data. You should make sure this test data represents a wide range of possible conditions, so that it evaluates how the model performs with many different types of inputs. For example, collecting test audio for several different faucets is a good idea.

You can use the Data acquisition tab to manage your test data. Open the tab, and then click Test data at the top. Then, use the Record new data panel to capture a few minutes of test data, including audio for both background noise and faucet. Make sure the samples are labelled correctly. Once you're done, head back to the Model testing tab, select all the samples, and click Classify selected:

^ have this already split -- get from Coursera 

The screenshot shows classification results from a large number of test samples (there are more on the page than would fit in the screenshot). The panel shows that our model is performing at 85% accuracy, which is 5% less than how it performed on validation data. It's normal for a model to perform less well on entirely fresh data, so this is a successful result. Our model is working well!

For each test sample, the panel shows a breakdown of its individual performance. For example, one of the samples was classified with only 62% accuracy. Samples that contain a lot of misclassifications are valuable, since they have examples of types of audio that our model does not currently fit. It's often worth adding these to your training data, which you can do by clicking the ⋮ icon and selecting Move to training set. If you do this, you should add some new test data to make up for the loss!

Testing your model helps confirm that it works in real life, and it's something you should do after every change. However, if you often make tweaks to your model to try to improve its performance on the test dataset, your model may gradually start to overfit to the test dataset, and it will lose its value as a metric. To avoid this, continually add fresh data to your test dataset.

## Model troubleshooting

## Deploying to your device 
