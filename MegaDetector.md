
25-SEP-2022: THIS IS CURRENTLY A WORK IN PROGRESS


# Getting started with MegaDetector! 

**DISCLAIMER**: This packet is written for the **National Geographic Tech Tutors Workshop** run through [WILDLABS](https://www.wildlabs.net/) (5/6-Oct-2022) and outlines the steps we will take in the workshop to set up and run MegaDetector and import data for review in Timelapse. The instructions presented are largely and gratefully taken from the original [MegaDetetctor documentation](https://github.com/microsoft/CameraTraps/blob/main/megadetector.md), created and maintained by [Sara Beery](https://beerys.github.io/), [Dan Morris](http://dmorris.net/), and [Siyu Yang](https://twitter.com/yangsiyu_) ([Efficiency Pipeline for Camera Trap Image Review](https://arxiv.org/abs/1907.06772), 2019). Modifications have been made to simplify the pipeline (e.g., using only the most up-to-date version of MegaDetector) and to add additional explanations or clarifications to aid understanding by a non-technical audience. We have also drawn heavily on the documentation and background provided in Saul Greenberg's excellent [Timelapse Image Recognition Guide](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseImageRecognitionGuide.pdf). We strongly recommend that you consult these original sources for additional information. 

## Table of Contents 

1. [What is MegaDetector](#what-is-megadetector)
2. [What MegaDetector does](#what-megadetector-does) 
3. [What we will do today](#what-we-will-do-today)
4. [Before we begin](#0-before-we-begin) 

## What is MegaDetector?

MegaDetector is a free, open-source image recognition system designed to detect wildlife, humans, and vehicles in camera trap images. 

Created by Microsoft and trained on millions of images from afround the world, this algorithm can be used to help automate the processing of images at far faster rates than would be possible by relying on manual human labor alone. 

![Alt Text](https://i.imgur.com/YHjuxaN.gif)

Image credit [eMammal](https://emammal.si.edu/). Video created by [Sara Beery](https://beerys.github.io/).

### Background

**Camera traps** are rugged cameras, deployed in the field, which are automatically triggered by passing animals, unobtrusively collecting data on the abundance, distribution, and behavior of medium- and large-bodied vertebrates. The information collected by these devices can be applied to address novel ecological questions and wildlife conservation conundrums. The improved reliability and continually decreasing cost of camera traps has resulted in massive uptake of this technological tool, and, concomitantly, exponential growth in the number of images they capture. 

Medium- to large-scale camera trap projects ([~78 cameras or larger](https://esajournals.onlinelibrary.wiley.com/doi/10.1002/fee.1448)) now amass millions of photos every year. In fact, the rate at which we capture camera trap data now [far exceeds](https://www.cambridge.org/core/journals/environmental-conservation/article/wildlife-insights-a-platform-to-maximize-the-potential-of-camera-trap-and-other-passive-sensor-wildlife-data-for-the-planet/98295387F86A977F2ECD96CCC5705CCC) our ability to process those images. The major bottleneck is transforming raw images (pictures) into the kinds of data that can be used in statistical analyses (numbers). Once collected, each image must be indentified to species and, often, further assessed for animal counts and characteristics (e.g., age, sex, behavior). The time and labor required to annotate these images - sometimes, years pass! - can mean that survey results are less relevant to practitioners by the time analysis is complete.  

Frustratingly, a huge proportion of the time wildlife ecologists and conservation biologists invest in reviewing camera trap images is spent going through data they aren’t even interested in – that is, empty images with no wildlife or those containing people or vehicles. The good news is, **artificial intelligence (AI)** can help accelerate this process, removing these “noise” images and allowing biologists to spend their time on images that matter!   

While AI is not a perfect replacement for the human eye/brain, integrating AI algorithms into camera trap workflows can massively accelerate research and conservation by alleviating data bottlenecks. 

### Image Recognition: Detectors vs. Classifiers

MegaDetector is **not** an image classifier! When you run MegaDetector, you will not learn what type of animal is present in your camera trap image. Rather, MegaDetector is a kind of AI that we call an image *detector*, which tells you whether there is a thing in the image and where it is. 

From Saul Greenberg's [Timelapse Image Recognition Guide](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseImageRecognitionGuide.pdf):

- A *detector* detects whether something is in an image or not. For each
suspected detection, it:
   - assigns a coarse identifying label to it (e.g., empty, animal, person, vehicle)
   - locates it via bounding box coordinates, which can be used to draw a rectangle around the suspected entity
   - assigns a confidence value indicating the likelihood that it is correct

- A *classifier* analyzes the sub-image contained by the detector's bounding box, where it performs a fine-grained classification chosen from a set
of known categories. A classifier will typically produce a list of possible classifications for each detection. For example, wildlife classification, categories will be wildlife species (e.g., elk, wolf, bear, dog). The recognizer includes a probability value that very roughly indicates the likelihood that the classification is correct.

Classifiers need to be trained for specific target specis in specific system - there is no 'MegaClassifier' for all species in all systems! (although check out emerging tools like [Wildlife Insights](https://www.wildlifeinsights.org/) that are trained on global camera trap data sets). For more information on pre-trained classifiers, Dan Morris maintains a [GitHub repository](https://agentmorris.github.io/camera-trap-ml-survey/) of available tools and resources. 

### So what?

Ugh, we will still have to go through all the images and identify each species, what a scam! Or is it? - why is information from a detector useful? 

1. Detectors can help eliminate empty images: 

An obscene number of images collected by camera traps ([up to >70%](https://hcjournal.org/index.php/jhc/article/view/123/1152021) of dataset in some cases) can contain *absolutely no animals at all!* Empty or 'blank' images can be triggered by waving vegetation, rain, excessive heat, or other environmental conditions that might cause your camera trap to go a little wonky. This might also occur when the camera is set to timelapse mode (i.e., taking photos every hour rather than waiting to be triggered). 

2. Detectors can also be used to flag images containing people or vehicles:

If your dataset will be shared (such as with citizen scientists or collaborators), you should probably remove any issues captured of people to respect their privacy. While rarely their primary function, camera traps can be used to surveil people without their consent, raising a whole host of ethical issues. As with empty images, if data from images containing people or vehicles is not important to your analysis, you can eliminate them from the classification pile. 

- For a great introduction to the ethics of conservation technology, check out the WILDLABS Tech Tutors discussion ["How do I use Conservation Tech Ethically?"](https://www.wildlabs.net/event/how-do-i-use-conservation-tech-ethically) and Koustubh Sharma's paper
[Conservation and people: Towards an ethical code of conduct for the use of camera traps in wildlife research](https://besjournals.onlinelibrary.wiley.com/doi/10.1002/2688-8319.12033). 

3. Detectors make manual classification a LOT easier: 

The detector pinpoints where in the image you need to look - you'll then be classifying an animal vs. classifying an animal and lots of noisy background. Studies have found that incorporating a image detector into a camera trap classification pipeline can [increase efficiency >500% over manual labelling](https://www.sciencedirect.com/science/article/pii/S2351989422001068#:~:text=One%20such%20model%2C%20MegaDetector%2C%20is,2019a%2C%20Microsoft%2C%202020) while still maintaining high accuracy and precision.

4. Detectors make AI classification a little easier too:

The information provided by the bounding boxes (zeroing in on the wildlife in the image) is also helpful if AI classifiers are then applied to try and sort your data by species. By cropping images to individual animals, classifiers only need to worry about identifying the wildlife pixels, rather than trying to figure out backgorund pixels as well. It's also a way to introduce counting (how many bounding boxes are in each image?) and handle images that contain multiple species.

### Under the hood

Typically, we would not recommend using an 'off-the-shelf' AI algorithm that hadn't been trained on your specific data - the huge variability in terms of species, geography, background, etc. can mean that even an algorithm that works exceptionally well for the camera trap images it is trained on might perform poorly even on new location within the very same system! 

But wait - we're talking here about an image classifer. When Sara Beery and her team took [millions of labeled camera trap images from around the world](https://lila.science), they found that an animal detector not only performed on species and sites used in the training data, but also generalized exceptionally well to animals and place that it had never 'seen' before. Thus, MegaDetector was born. 

So what kind of AI is MegaDetector? MegaDetector is an *Faster R-CNN* object detection model. **CNN** stands for **"convolutional neural network"**, a type of artificial neural network used in image recognition and processing that is specifically designed to process pixel data. CNNs evaluate image data in a hierarchical fashion similar to the way mammalian brains process visual information. They are trained to identify objects of interest by learning how to associate raw inputs (i.e., pixel values) to labelled image data. CNNs are a powerful class of models because they are able to learn and extract complex visual features without direct supervision of a human expert. 

training data - 

In a (greatly simplified) nutshell, the CNN applies a bunch of image filters over an image in a process known as *convultion*. The convolution layers use these filters to pick out visual features in theimage (e.g., colors, textures, shapes). What features the model focuses on are learned by the model, rather than prescribed by the researcher. The model then proposes regions it thinks don't belong to the background: it gives a prediction score for how confident it is that the region contains an animal. After each prediction, the model goes and checks it's prediction again the 'ground truth' data - this is the 

Initially, the regions it proposes are random, and it predicts random boxes. 

1. Faster R-CNN
CNN --> applied bunch of image filters in process known as convolution; convolutional layers use filters to pick out visual features (colors, textures, shapes) --> learned rather than prescribed 
2. Model than proposes regions that are likely non-background (gives score for each category --> prediction is score between 0-1, there confidence score comes from)

initiatially starts with 'random weights' --> predicts random boxes
but knows 'ground truth'; after each iteration, checks againsts ground truth
then adjusts parameters, runs again, checks again --> iterative


ADD IMAGE FROM SIYU'S TALK 

ADD IMAGE FROM MIAO ET AL. 2019 

<img scr="https://i.imgur.com/CEfmAGd.jpg" width="600">
Image credit: Siyu Wang, from 

### Important considerations 

Robots are not taking our wildlife ecology jobs yet! Here are a few things to keep in mind when using a 'black box' AI such 

1. Productivity gain is small if not many non-empty images 

2. AI is not perfect 

Talk about how data needs to be manually qc'd because not perfect 100% of time 

Challenges that are hard for humans (which produce the training data) are then hard for AI trained on those (mis)classifications! This can be particularly tricky for camera trap images, 

However, learning to associate specific features with objects of interest - in this case, animal species - requires a substantial amount of labelled data. For example, the ImageNet computer vision challenge dataset contains 1,000 images per object class (Russakovsky, 2015). Real-world datasets are often highly imbalanced, dominated by images of common objects with few examples of rarer classes. As such, rare objects (species) are more difficult for algorithms to recognize (see results and discussion in Norouzzadeh, 2018). Camera trap images present additional challenges in the application of deep learning as animals in the images can be difficult to discern or distorted by occlusion, unusual perspectives, camouflage, and more (Beery, 2018).


<img src="https://i.imgur.com/z386lxe.jpg" width="600">
[Sara Beery](https://beerys.github.io/) et al. 2018 ["Recognition in Terra Incognita"](https://openaccess.thecvf.com/content_ECCV_2018/papers/Beery_Recognition_in_Terra_ECCV_2018_paper.pdf)

### Evaluating MegaDetector performance 

While we will not do a deep-dive during this workshop into assessing MegaDetector's performance, here are some key metrics that can be used to evaluate how well the model works on your data. 

***Types of errors*** 

[Saul Greenberg](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseImageRecognitionGuide.pdf) highlights four types of errors to be on the look-out for when running an image recognition algorithm: 
- *False positives*: The algorithm DOES detect an entity when there is NO entity (i.e., in an empty image)
- *False negatives*: The algorithm DOES NOT detect an entity when the entity is present  
- *Incorrect identification*: The algorithm DOES detect an entity when an entity is present, but incorrectly labels it (e.g., [simple detector] a person gets labelled as wildlife, or [classifier] a warthog gets labelled as a mongoose) 
- *Amiguity*: The algorithm detects several overlapping and possibly conflicting detections

***Confidence threshold***

MegaDetector has an important parameter which is called "confidence threshold". The confidence threshold has continuous values ranging between 0 and 1; the higher the confidence threshold is, the more strictly the user wants the model to make a prediction. -- UPDATE

***Accuracy metrics***

There are three descriptive accuracy metrics that can be used to assess model performance: 

**1. Overall accuracy** is calculated by dividing True predictions by the total number of predictions. This metrics quantifies the performance of the model over all species. For example, the model's overall accuracy of 0.7 means that for every 10 images, regardless of classes, the model is able to correctly classify 7 images.

**2. Precision** is calculated by dividing True Positive by the total number of images that are classified as Positive. The result implies the data quality of each class classified by the model. For example, precision of Animal class is 0.7 implies that for every 10 images that are classified by the model as Animal, 7 of them are correct. 

**3. Recall** is calculated by dividing True Positive by the total number of ground- truth Positive class. This statistic quantifies the ability of the model to detect each species. For example, recall of Owston's civet is 0.8 means that if there are 10 images of Owston's civet in the dataset, the model is able to detect 8. 

While overall accuracy gives us an idea on how the model classifies Animal-Human-Blank, in general, precision warns us how much work we have to do to clean the model's predictions, and recall tells us how much data loss of each species we have to admit. In the context of conservation, however, recall is the most favored metrics to assess and tune the model because the most important goal of camera-trapping is to detect the most threatened species so that appropriate intervention is targeted. Threatened species are usually rare species, which makes every capture of them is valuable. Thus, in order to make the most of camera-trapping images, the model is expected to retrieve as many captures of threatened species as possible, which is reflected by recall. Nevertheless, most of the time, precision and recall are negatively correlated, i.e. if the model is tuned to increase recall, it will come at the expense of precision. This trade-off implies that more manual effort must be spent on verifying the model prediction for every increase of recall value. That said, this extra effort is several order of magnitude less than the effort spent without the model's help, so it is acceptable. -- UPDATE 


## What MegaDetector does

MegaDetector does ... adding a 'bounding box' (DEINFE) around objects of interest ... creating a JSON file that contains XX 

Trained on several hundred thousand bounding box images from a variety of ecosystems 

**Sidebar: What is a JSON file?** JSON stands for "JavaScript Object Notation" and is a text-based file format that stores simple data structures and objects. These files are Jlightweight, text-based, human-readable, can be edited using a text editor. 

Ultimately, MegaDetector produces a file that tells you 

Of course, running MegaDetector doesn't do anything useful by itself: it just produces a file that tells you which images MegaDetector thinks have animals/people/vehicles in them. You still need a way to use that file in a real image processing workflow. We've integrated with a variety of tools that camera trap researchers already use, to make it relatively painless to use our results in the context of a real workflow. Our most mature integration is with Timelapse, a fantastic open-source tool for reviewing camera trap images (very efficient even if you're not using AI!). Read more about how to use MegaDetector results with Timelapse here.

These files can be ... integrated with ..., enabling the ... 

### What MegaDetector does NOT do: 

Introduce TIMELAPSE HERE 

Timelapse](https://saul.cpsc.ucalgary.ca/timelapse). This nifty program allows researchers to visualize and encode data, and has features that include: 
   - reading, organizing, and displaying images
   - automatically extracting information (dates, times, locations) from images
   - allowing you to create a custom interface for entering data specific to your project 
... among many others! You can read more about Timelapse's functionality in their [QuickStart Guide](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseQuickStartGuide.pdf). 

Importantly for us, the MegaDetector team have collaborated with Timelapse to 

Timelapse is recommended as a ‘front-end’ for visualizing and using MegaDetector recognition data.

Of course, running MegaDetector doesn't do anything useful by itself: it just produces a file that tells you which images MegaDetector thinks have animals/people/vehicles in them. You still need a way to use that file in a real image processing workflow. We've integrated with a variety of tools that camera trap researchers already use, to make it relatively painless to use our results in the context of a real workflow. Our most mature integration is with Timelapse, a fantastic open-source tool for reviewing camera trap images (very efficient even if you're not using AI!). Read more about how to use MegaDetector results with Timelapse here.

We have somewhat-less-complete integrations with the eMammal desktop application and with dikiKam.

We also have Python tools that use MegaDetector results to just separate a folder of images into folders containing images that are probably-empty, probably-animal, etc., preserving the original folder structure within these folders. Users often use this approach to just get rid of the images that MegaDetector is really sure are empty, then you can go about your workflow exactly as you did before, just with fewer empty images.

You can read more about MegaDetector and check out the original documentation **here** *link*. 
Link to Timelapse commentation 

## What we will do today 

1. First, we will run a simple script that will process one camera trap image: the AI will attempt to locate the object of interest (wildlife, human, or car) in a camera trap image and add a bounding box around the area it predicts the object to be located. This is neat to see, but running through images one at a time is not particularly useful! Rather, we will use this as a test to make sure that your environment is set up correctly. 

2. Next, we will run a script that processes entire batches of images in one go. This script produces a JSON file that we will use to view and sort our images in a camera trap image analyser. 

3. We will then be loading our resultant JSON files and camera trap images into [Timelapse](https://saul.cpsc.ucalgary.ca/timelapse) for manual review and additional data extraction.  

**Important notes**: 
- The following instructions are for Windows and Mac machines: please follow the instructions particular to your operating system.  
- We will be using Python, but no coding knowledge required! We'll navigate you to your terminal, into which you can copy and paste the commands listed here. It should be plug and chug, but I'll be there to help trouble-shoot any issues that arise! 

## 0. Before we begin... 

Before we can run MegaDetector, we need to install the following prerequisites: 

**(1) Anaconda**: Anaconda is a free, open-source data science platform that helps users manage Python packages. You can download Anaconda [here](https://www.anaconda.com/products/individual).

On Windows, Anaconda will create two different comand prompts: 'Anaconda Prompt (anaconda3)' and 'Anaconda Powershell Prompt (anaconda3)'. *Use the 'Anacdona Prompt (anaconda3)'* not the powershell prompt. For Macs, anaconda will run directly in your terminal. 

**(2) Git**: Git is a free, open-source version control software, i.e., a program for coordinating among programmers by tracking changes in sets of files. 

- For Windows: download [Git for Windows](https://www.git-scm.comd/download/win). 

- For Macs, you can use Homebrew (a package manager) to download Git. If you haven't already installed Homebrew, open up your terminal and type: 

```batch
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Hit 'return'. When Homebrew has finished downloading, type the following into your terminal and hit 'return': 

```batch
brew install git 
```

**(3) Timelapse Software**: FILL IN 

**(4) [Mac Users Only] Windows Emulator**: [Timelapse](https://saul.cpsc.ucalgary.ca/timelapse/) (the image analyser that we will read our data in to) does not run on Macs (boo!). If you are working on a Mac, you will need to fire up a Windows emulator when we are ready to upload our AI predictions. 

*What is a Windows emulator?* An emulator allows one computer system (the "host", aka your Mac) to imitate the functions of another computer system (the "guest", aka Windows), enabling you to run software and programs that would otherwise only run on the guest system. 

If you do not already have a Windows emulator installed, you have several options: 

- Partition your hard drive using **Boot Camp**

Apple's Boot Camp allows you to install Windows alongside macOS; this is a lot faster than running a virtual machine (see below). This program comes on your Mac free of charge. You will need to back up your Mac, have sufficient space on your hard drive to install Windows, and purchase a Windows license to use. However, only one operating system can run at a time, so you'll have to restart your Mac to switch between macOS and Windows. 

More instructions on setting up your hard drive using Boot Camp can be found [here] (XXX). 

- Use **Wine** to run individual Windows programs

[Wine](https://www.winehq.org/) is a compatability layer that allows Windows applications to run on other operating systems, which is a fancy way of saying that instead of booting up an entire new OS or creating a virtual machine, you can run individual applications from your macOS. This option does not require purchasing a Windows license. Once you install Wine, you can right-click on the .exe file for Timelapse and select 'Open With > Wine' to fire up the program. 

More instructions on how to use Wine can be found [here]([XXX](https://macexpertguide.com/run-windows-applications-on-a-mac-with-wine/)); you can download the latest version of Wine [here](https://wiki.winehq.org/Download). 

However, **Wine does not run on macOS versions >12.5**; if your Mac is up-to-date, then this solution **will not work**. 

- Create a virtual machine using **Parallels**, **VWMWare Fusion**, or **VirtualBox**. 

A virtual machine is 

I will be using Parallels to demonstrate how to upload your MegaDetector results into Timelapse; **for this workshop, I recommend downloading the free trial of Parallels** and exploring alternative options if this is a tool you will be using in the future. 


## 1. Camera trap data

A sample of camera trap image data can be found here ... store on your computer WHERE 
Use your own data if also store HOW 

If interested in playing around with more camera trap data, you can find public repositiories at LILA SCIENCE --> link 


## 2. Download MegaDetector 

We'll be working with the latest version of MegaDetector (MDv5b) for this workshop. 
- Create a folder on your Desktop called 'megadetector' 
- Download [MDv5b](https://github.com/microsoft/CameraTraps/releases/download/v5.0/md_v5b.0.0.pt) to this folder 


## 3. Clone git files 

The developers of MegaDetector have created three git repositories ('repos') - *cameratraps*, *ai4eutils*, and *yolov5* - that contain utilities and tools necessary to run the MegaDetector model. You can install these to your computer by doing the following: 

**Instructions for Windows**

*First time*: open your Anacadona Prompt application and run the following code: 

```batch
mkdir c:\git
cd c:\git
git clone https://github.com/Microsoft/cameratraps
git clone https://github.com/Microsoft/ai4eutils
cd c:\git\cameratraps
conda env create --file environment-detector.yml
conda activate cameratraps-detector
set PYTHONPATH=%PYTHONPATH%;c:\git\cameratraps;c:\git\ai4eutils;c:\git\yolov5
cd c:\git
git clone https://github.com/ultralytics/yolov5/
cd c:\git\yolov5
git checkout c23a441c9df7ca9b1f275e8c8719c949269160d1
cd c:\git\cameratraps
```

This has created your MegaDetector environment! When running MegaDetector in the future, you only need to run: 

```batch



**Instructions for Mac** 





## Setting up MegaDetector: Mac

## Setting up Timelapse: Mac 


1. Open terminal

2. Install homebrew by copy/pasting the following into your terminal and hitting 'enter': 

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

When installing homebrew, you will be asked to enter your computer password. The cursor will not show your type - this is okay! When done, hit 'enter'. 

3. Install Wine packages using homebrew by copy/pasting the following into your terminal and hitting 'enter': 

brew tap homebrew/cask-versions

brew install --cask --no-quarantine wine-stable

4. Go to your Timelapse folder
5. Right-click 'Timelapse2.exe' and click 'Open' 
6. If necessary, verify the Timelapse app by going to 'System Preferences' --> 'Security & Privacy' and click on 'Allow Anyway' 

*add screenshots* 

7. Now gow back to Timelapse folder, right-click 'Timelapse2.exe' and click 'Open' 






## Further Considations

### Human anonymity 

We additionally developed and deployed a human blurring program, which uses the outputs from MegaDetector to obscure individual human identities (Fig. 1; see: https://github.com/WildCoLab/WildCo-FaceBlur). This tool uses the output file from MegaDetector, which provides classifications by category, bounding box coordinates around the detection, as well as a confidence value for each detection. Using this information, the blurring program applies a gaussian blur within bounding boxes classified as human above a user defined confidence threshold. Users interact with the program via an R (R Core Team, 2020) interface, which allows specification of a confidence threshold and a level of blurriness, while being familiar for many ecologists. The blurring process itself occurs via Python (Python Software Foundation, 2021) to maximize image handling speed, which is called from the R interface via the package reticulate (Ushey et al., 2021). from: https://www.sciencedirect.com/science/article/pii/S2351989422001068#:~:text=One%20such%20model%2C%20MegaDetector%2C%20is,2019a%2C%20Microsoft%2C%202020).



## Applying MegaDetector in your reserach 

**How many images can I get through?** 

The number of images you can process in a single day depends on the computer resources you have available. Siyu Yang provices the following guidelines: 

- 5K-10K images per day on typical mid-range laptop with no GPU
- 100K images per day on a not-quite-top-of-the-line-but-pretty-decent GPU (i.e., a gaming computer or in the cloud) 
- alternative, you can submit images to the MegaDetector team: they have 16 GPU and can process 1.6M images per day (*note: uploading the images to servers accessible to MegaDetector team can take a long time depending on the number of images and your internet connectivty) 

**Want to learn more?**
Here are some additional MegaDetector tutorials to step you through the MegaDetector pipeline: 
- ["How do I get started with MegaDetector?"](https://www.wildlabs.net/event/how-do-i-get-started-megadetector) by Siyu Wang
- ["How do I get started using Machine Learning for my camera traps?"](https://www.wildlabs.net/event/how-do-i-get-started-using-machine-learning-my-camera-traps) by Sara Beery
- ["Camera Trapping Software"](https://www.youtube.com/watch?v=7B_sf7mnpkc) by Sara Beery

**Need more help?** Contact Dan Morris (cameratraps@lila.science) to guide you through the MegaDetector process. 

