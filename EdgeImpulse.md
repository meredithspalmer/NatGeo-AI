# Getting started with Edge Impulse! 

**DISCLAIMER**: This packet is written for the **National Geographic Tech Tutors Workshop** run through [WILDLABS](https://www.wildlabs.net/) (5/6-Oct-2022) and outlines the steps we will take in the workshop to create a audio classifier through the online [Edge Impulse](https://www.edgeimpulse.com/) platform. The instructions presented are based strongly on the accessible and comprehensive [Edge Impulse documentation](https://docs.edgeimpulse.com/docs/tutorials/audio-classification), including their Coursera series ["Introduction to Embedded Machine Learning"](https://www.coursera.org/learn/introduction-to-embedded-machine-learning/home/week/1). Modifications have been made to simplify the pipeline and to add additional explanations or clarifications to aid understanding by a non-technical audience. Dan Situnayake's Tech Tutors epsiode, ["How do I train my first machine learning model"](https://www.wildlabs.net/event/how-do-i-train-my-first-machine-learning-model), is another amazing resource. We strongly recommend that you consult these original sources for more information. 

## Table of Contents 

1. [What is Edge Impulse](#what-is-edge-impulse)
2. [What we will do today](#what-we-will-do-today)
3. [Before we begin](#before-we-begin) 
4. [The data](#the-data) 
5. [Create an audio project](#create-an-audio-project)
6. [Upload your data](#upload-your-data)
7. [Designing an impulse](#designing-an-impulse) 
8. [Configure the impulse](#configure-the-impulse) 
9. [Train the neural network](#train-the-neural-network)
10. [Deploying the model](#deploying-the-model) 

## What is Edge Impulse?

**[Edge Impulse](https://www.edgeimpulse.com/)** is a tool for training artificial intellgience models, specifically those that are optimized to perform on edge devices (**tinyML**). By using Edge Impulse, you can build sophisticated AI algorithms without having to become an expert in Python or Tensorflow. Edge Impulse allows you to create a model pipeline (called an **"impulse"**), train and test your model, and deploy it to your device. 

<p align="center">
  <img src="https://miro.medium.com/max/1400/1*3_WTRShh3TTQXEzfdcEUEA.png" width="900"/>
</p>

### What is tinyML? 

Instead of requiring massive computers to run complex machine learning algorithms, we can now optimize these algorithms to run on smaller, less powerful devices - such as sensors which can be deployed in the field. We these optimized algorithms **tinyML** or **embedded AI** and the sensors and devices that host them **edge devices** or **embedded systems**. 

When creating these models, we build the model architecture and train the model on a computer or in the cloud. When the model is ready, we deploy the fully trained neural network to run inference on an edge device. The device then processes incoming data locally without having to send data back to a server for analysis. 

### Why use tinyML? 

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

Today, we'll be building and training a neural network that can perform an audio classification task, specificallly, recogniziing the sound of a gunshot. Hypothetically, this kind of algorithm could be deployed to a passive acoustic detector and used to send an alert to anti-poaching teams if suspicious activity is detected.

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

## Create an audio project 

Navigate to the Edge Impulse website and login. Begin by selecting "Create a New Project". Here, we'll create a "Developer" project called "poacher-detector". 

<p align="center">
  <img src="https://i.imgur.com/srhTOq7.png" width="600"/>
</p>

Next, we get to choose what type of Edge Impulse project we want to create. Select "Audio", but note the many different types of input data that can be used to train a machine learning model! 

<p align="center">
  <img src="https://i.imgur.com/LA37Fmw.png" width="600"/>
</p>

## Upload your data 

While you can connect your Edge Impulse project to an embedded device to collect data directly, here, we'll **Import existing data** to upload our gunshot/ambient noise audio library. Click **Go to the uploader**. 

<p align="center">
  <img src="https://i.imgur.com/9Tvu8EH.png" width="600"/>
</p>

The audio exemplars we upload will be split between the training, testing, and validation data sets. Remember that the data used to train a model has to be labeled. First, we will choose all of our "gunshot" files (317), select that they are automatically split between training and testing data, and manually enter the label "gunshot". Begin your upload. 

<p align="center">
  <img src="https://i.imgur.com/UnJjvsn.jpg" width="600"/>
</p>

When the **Upload output** panel on the right reads "Job completed", do the same process with your ambient noise files. This time, enter the label "ambient" and click **Begin upload**. 

When this has finished, click on the tab at the top of the screen called **Training data**. 

<p align="center">
  <img src="https://i.imgur.com/FfypQ1V.png" width="900"/>
</p>

You should now see that 5m 41s of our data have been designated as data that will be used to train the model. If you hover over the pie chart on the left, you can see that approximately half of the data are gunshot exemplars and half are ambient noises. The pie chart on the right shows us that 80% of the ata are being used to train the model and 20% have been reserved to test the model. 

If you click on one of the file names under **Collected data**, the raw data will appear in a panel on the left. You can click the play button to hear your examplar. 

Now click on the tab **Test data**. 

<p align="center">
  <img src="https://i.imgur.com/2EBWNiE.png" width="900"/>
</p>

You'll arrive at a similar page, this time showing you the distribution of samples that have been set aside in the test data set. Again, this is 20% of the total data uploaded and you should see a roughly equal split of gunshot to ambient noises. 

Now that your data is uploaded, you are ready to design your impulse! 

## Designing an impulse 

An **impulse** is the machine learning model pipeline. It takes the raw data, uses signal processing to extract features from the data that the model will be trained to recognized, and then uses a learning block to classify new data. Don't worry, we'll take this step by step! 

First, click on the **Impulse design** tab on the left of the screen. You should arrive at the following page: 

<p align="center">
  <img src="https://i.imgur.com/NQejBDW.png" width="900"/>
</p>

### Raw data block

First, we'll parameterize the **Raw Data** block. This is the red block on the left of the screen, titled **Time series data**. 

To expand the amound of audio data used to train the model, Edge Impulse will chop the raw data up into many little samples. **Window size** tells Edge Impulse how long each sample should be in milliseconds. Here, set the window size to **1000 ms**, or one second. This should be long enough for the algorithm to determine whether the sound is a gunshot or background noises. 

To maximize the number of samples from each piece of raw data, windows can overlap. **Window increase** controls how long after the first window the subsequent window starts. Set this value to **100 ms**. While overlapping windows will contain similar data, each window is still a unique audio sample. Adjust this value to 300 ms. 

We'll leave the Frequency at the default for now. Ensure that the "Zero-pad data" button is checked. This prevents the models from disguarding samples that are smaller than the window size. Your screen should now look like this: 

<p align="center">
  <img src="https://i.imgur.com/Lw3a7tF.png" width="900"/>
</p>

### Processing block

The processing block extracts the features from your raw audio data that your model will train on. 

Click **Add a processing block** and choose **Audio (MFE)**. This is the preferred option for non-voice audio. 

<p align="center">
  <img src="https://i.imgur.com/70CjZBw.png" width="900"/>
</p>

### Learning block

The final step is adding a learning block - this is the neural network that is trained to learn on your data! The neutral network will take the features produced by the MFE as input, and try to map this to one of two classes: 'gunshot' or 'background'. The input will go through layers of virtual 'neurons', which filter and transform the input data to extract distinguishing features. Ultimately, the algorithm will procude two values: the probability that the input represents a gunshot and the probability that the input represents background nature noises.

During the **training phase**, the state of these 'neurons' are tweaked and refined so that they layers ultimately transform the input into the correct output. This is an iterative process, where the model is fed samples of training data, outputs are evaluated, adjustments are made, and the process is repeated thousands and thousands of times until the correct answers are produced (or you give up - sometimes, there are problems that can't be solved by ML!). 

How the 'neurons' are arranged into layers is called an **architecture**. There are many different kinds of ML architectures that have been developed for different kinds of tasks. Here, we'll stick with Edge Impulse's defualt architecture. 

Select **Add a learning block** and choose the **'Classification (Keras)'** block. 

Click **Save impulse**! You should have an impulse that looks like: 

<p align="center">
  <img src="https://i.imgur.com/Y8pa2A7.png" width="900"/>
</p>

## Configure the impulse

Next, we can start tweaking the individual blocks. Let's start with the **Processing block**. Under **Impulse design** on the left-hand panel, click **MFE**. You'll see a page that looks like this:

<p align="center">
  <img src="https://i.imgur.com/FEEh6AR.png" width="900"/>
</p>

The processing block transforms each audio sample into **spectrogram**, which can be seen in on the righthand side of the page. A spectrogram is a visual way of representing how the frequencies of an audio signal vary through time. Rather than "listening" to the audio files, the machine learning algorithm we are training is rather trying to distinguish visual patterns between spectrograms. 

As Edge Impulse provides sensible starting parameters, we can leave the default settings and start generating the spectrograms on which we'll train our algorithm. To do this, click the **'Generate features'** button at the top of the page, then click the green **'Generate features'** button. This process can take a minute or two to complete. 

<p align="center">
  <img src="https://i.imgur.com/Fl3toZu.png" width="900"/>
</p>

When the files have been processed, we can now use the feature explorer panel on the right-hand side to visualize our dataset. By mapping the features into 2D space, we can see how well our different classes separate (which, if so, bodes well for training our ML classifier!). 

## Train the neural network 

In the left-hand menu under **Impulse design**, click on **NN Classifier**. While there are parameters here you can tweak (such as the number of training cycles), we'll stick with Edge Impulse's default parameters. Scroll down to the bottom of the page and hit the green **Start training** button. This process may also take a hot second. 

<p align="center">
  <img src="https://i.imgur.com/KlJzfu8.png" width="900"/>
</p>

When training is complete, you'll see the **Model panel** appear at the right side of the page:

<p align="center">
  <img src="https://i.imgur.com/t4E832B.png" width="300"/>
</p>

**Accuracy** refers to the percentage of data samples that were correctly classified. The higher number the better. For many applications, an accuracy above 80% can be considered very good. 

**Loss** refers to 

The **Confusion matrix** shows how well the model performed on our **validation set**. Our two types of input data were pretty distinct, and the model is doing an excellent job of assigning new data to the correct category. 

<p align="center">
  <img src="https://media.giphy.com/media/8Iv5lqKwKsZ2g/giphy.gif" width="800"/>
</p>

**Congratulations**, you've trained your first machine learning model! 

## Deploying the model 

There are many options for tweaking and updating the performance of your model. You can learn more by going through Edge Impulse's excellent [online documentation](XXX). 

For now, we're going to move ahead to model deployment, and classify some data "live in the field". 

### Deploying to your device 

Click on the **Deployment** tabe at the bottom of the left-hand panel and scroll down to **Run your impulse directly**. Note that we're passing by options to package your model into a library or a binary that can be deployed onto a development board. 

Today, we're going to upload the model onto our smartphones and use our phone microphone to test whether the impulse can correctly categorize new, incoming data. Select the **Mobile phone** option and click **Build**. 

<p align="center">
  <img src="https://imgur.com/vxwAcV1" width="600"/>
</p>

This should pop up a QR code which you can scan using your phone. This will take you to smartphone.edgeimpulse.comm, where your model will be uploaded. Grant your detector access to your phone's microphone. You should now see the classifer listening for target audio. 

<p align="center">
  <img src="https://i.imgur.com/e8buI2g.jpg" width="600"/>
</p>

The classification being assigned shows up under the 'listening...' circle. In the image, the classifier is assigning general background noise correctly to the class audio. 

### Testing your model 

Now! Fire up YouTube and search for a few gunshot noise audio clips. With your model running on your phone, play a few clips and see whether it can correctly classify these sounds. 

How well does your model perform?  
