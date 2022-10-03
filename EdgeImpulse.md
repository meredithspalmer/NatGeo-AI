28-SEP-2022: THIS IS CURRENTLY A WORK IN PROGRESS


# Getting started with Edge Impulse! 

**DISCLAIMER**: This packet is written for the **National Geographic Tech Tutors Workshop** run through [WILDLABS](https://www.wildlabs.net/) (5/6-Oct-2022) and outlines the steps we will take in the workshop to create a audio classifier through the online [Edge Impulse](https://www.edgeimpulse.com/) platform. The instructions presented are based strongly on the accessible and comprehensive [Edge Impulse documentation](https://docs.edgeimpulse.com/docs/tutorials/audio-classification), including their Coursera series ["Introduction to Embedded Machine Learning"](https://www.coursera.org/learn/introduction-to-embedded-machine-learning/home/week/1). Modifications have been made to simplify the pipeline and to add additional explanations or clarifications to aid understanding by a non-technical audience. Dan Situnayake's Tech Tutors epsiode, ["How do I train my first machine learning model"](https://www.wildlabs.net/event/how-do-i-train-my-first-machine-learning-model), is another amazing resource. We strongly recommend that you consult these original sources for more information. 

## Table of Contents 

1. [What is Edge Impulse](#what-is-edge-impulse)
2. [What we will do today](#what-we-will-do-today)
3. [Before we begin](#before-we-begin) 
4. [The data](#the-data) 
5. [Designing an impulse](#designing-an-impulse) 

## What is Edge Impulse?

[Edge Impulse](https://www.edgeimpulse.com/) is a tool for training artificial intellgience models, specifically those that are optimized to perform on edge devices (**tinyML**; more on this below!). By using Edge Impulse, you can build a sophisticated AI algorithm without having to do a deep-dive into the underlying framwork for machine learning or become an expert in Python or Tensorflow. Edge Impulse allows you to create a model pipeline (called an **"impulse"**), train and test your model, and deploy it to your device. 

<p align="center">
  <img src="https://miro.medium.com/max/1400/1*3_WTRShh3TTQXEzfdcEUEA.png" width="900"/>
</p>

### What is tinyML? 

Instead of requiring massive computers to run complex machine learning algorithms, we can now optimize these algorithms to run on smaller devices, such as sensors which can be deployed in the field. We these optimized algorithms **tinyML** or **embedded AI** and the sensors and devices that host them **edge devices** or **embedded systems**. 

??? Algorithms trained and then deployed
??? What type of algorithms 

### Why optimize for tinyML? 

TinyML is transforming the way we use sensor data. Traditionally, wildlife ecologists and conservation biologists collect data by deploying devices (like camera traps, acoustic monitors, or tracking collars) in the field; waiting a few months for data to accumulate; and then going back out, collecting those data, and - at long last! - processing that information to draw inference on their system. 

But what if you need to know immediately if, say, a poacher's gun goes off, or if an elephant starts heading out of the reserve towards a neighboring village? You can't take action to capture the poacher or divert the elephant if you only find out about the event several months later. Ideally, you would want the ability to process the incoming data as it is received and take immediate action based on what was detected. 

TinyML algorithms can run inference directly on field sensors (edge devices), transforming the way computers interact with the real world. These algorithms can be trained to detect and respond to input such as: 
- Vibration and motion (e.g., acceleromoter, pressure, speed)
- Voice and sound (e.g., keyword spotting, speech/sound recognition)
- Images and video (e.g., face unlock, object detection/classification)
allowing researchers to do WHAT 

In addition to being able to react to the world in real-time, this type of machine learning has other benefits that make them particularly apt for field research: 
- They can be deployed on small, cheap hardware (get more for your grant!) 
- They consume little energy, massively extending their battery life (and, consequentially, deployment length)
- They can work without a network connection (no need to send data to a server, so can work without a cellphone, WiFi, or other kind of network) 

## What we will do today 

Today, we'll be building and training a deep neural network that can perform an audio classification task, specificallly, recogniziing the sound of a gunshot. Hypothetically, this kind of algorithm could be deployed to a passive acoustic detector and used to send an alert to anti-poaching teams if suspicious activity is detected.

<p align="center">
  <img src="https://i.imgur.com/mlj8bfA.png" width="600"/>
</p>

Image credit: [Hack the Poacher](https://www.hackthepoacher.com/) -- UPDATE THIS 

## Before we begin 

1. [Register](https://studio.edgeimpulse.com/login) for a free Edge Impulse account.
2. If you want to deploy and test the model we build, you'll need a supported device: for this workshop, any [mobile smartphone](https://docs.edgeimpulse.com/docs/development-platforms/using-your-mobile-phone) should work.

## The data 

If you were training an algorithm to deploy in the field, you would first want to collect audio data yourself - preferably under field conditions and using the same device that will the be listening for new data and running inference. Unfortunately, we won't be shipping everyone to the African bush today to record our target audio (boo!). Instead, download **[this library](https://drive.google.com/file/d/1L5QYzbW8AYzGygZjrEdj2Qm-8KbBUo2b/view?usp=sharing)** we've compiled for you. 

This is a balanced dataset (see **Intro to AI**), containing ~7 minutes of audio divided evenly among the two classes we want the algorithm to discriminate:

1. Gunshots
2. Ambient nature noises (i.e., the background, or "everything else") 

If you listen in on these files, you can see that we compiled a variety of sounds collected under different scenarios: "background noise" contains samples of wind, rain, bugs and birds chirping, and other things you might hear in nature; "gunshots" includes blasts recorded from different types of guns at different distances. There's not guarantee that the model will generalize to noises not introduced in the datset, so the more diverse and representative of real-world conditions the dataset is, the better it will perform. 

If you want to learn how to collect and process your own audio, check out the Edge Impulse tutorial on ["Collecting your data"](https://docs.edgeimpulse.com/docs/tutorials/audio-classification#2.-collecting-your-first-data). 

## Create a audio project 

Navigate to the Edge Impulse website and login. Begin by selecting "Create a New Project". Here, we'll create a "Developer" project called "poacher-detector". 

<p align="center">
  <img src="https://i.imgur.com/srhTOq7.png" width="600"/>
</p>

Next, we get to choose what type of Edge Impulse project we want to create. Select "Audio", but note the many different types of input data that can be used to train a machine learning model! 

<p align="center">
  <img src="https://i.imgur.com/LA37Fmw.png" width="600"/>
</p>

## Upload your data 

While you can connect your Edge Impulse project to an embedded device to collect data directly, here, we'll "Import existing data" to upload our gunshot/ambient noise audio library. Click "Go to the uploader". 

<p align="center">
  <img src="https://i.imgur.com/9Tvu8EH.png" width="600"/>
</p>

The audio exemplars we upload will be split between the training, testing, and validation data sets. Remember that the data used to train a model has to be labeled. First, we will choose all of our "gunshot" files (317), select that they are automatically split between training and testing data, and manually enter the label "gunshot". Begin your upload. 

<p align="center">
  <img src="https://i.imgur.com/UnJjvsn.jpg" width="600"/>
</p>

When the "Upload output" panel on the right reads "Job completed", do the same process with your ambient noise files. This time, enter the label "ambient" and click "Begin upload". 

When this has finished, click on the tab at the top of the screen called "Training data". 

<p align="center">
  <img src="https://i.imgur.com/FfypQ1V.png" width="600"/>
</p>

You should now see that 5m 41s of our data have been designated as data that will be used to train the model. If you hover over the pie chart on the left, you can see that approximately half of the data are gunshot exemplars and half are ambient noises. The pie chart on the right shows us that 80% of the ata are being used to train the model and 20% have been reserved to test the model. 

If you click on one of the file names under "Collected data", the raw data will appear in a panel on the left. You can click the play button to hear your examplar. 

Now click on the tab "Test data". 

<p align="center">
  <img src="https://i.imgur.com/2EBWNiE.png" width="600"/>
</p>

You'll arrive at a similar page, this time showing you the distribution of samples that have been set aside in the test data set. Again, this is 20% of the total data uploaded and you should see a roughly equal split of gunshot to ambient noises. 

Now that your data is uploaded, you are ready to design your impulse! 

## Designing an impulse 

An impulse is 



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
