# Getting started with MegaDetector! 

**DISCLAIMER**: This packet is written for the **National Geographic Tech Tutors Workshop** run through [WILDLABS](https://www.wildlabs.net/) (5/6-Oct-2022) and outlines the steps we will take in the workshop to set up and run MegaDetector and import data for review in Timelapse. The instructions presented are largely and gratefully taken from the original [MegaDetetctor documentation](https://github.com/microsoft/CameraTraps/blob/main/megadetector.md), created and maintained by [Sara Beery](https://beerys.github.io/), [Dan Morris](http://dmorris.net/), and [Siyu Yang](https://twitter.com/yangsiyu_) ([Efficiency Pipeline for Camera Trap Image Review](https://arxiv.org/abs/1907.06772), 2019). Modifications have been made to simplify the pipeline (e.g., using only the most up-to-date version of MegaDetector) and to add additional explanations or clarifications to aid understanding by a non-technical audience. We have also drawn heavily on the documentation and background provided in Saul Greenberg's excellent [Timelapse Image Recognition Guide](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseImageRecognitionGuide.pdf). We strongly recommend that you consult these original sources for more information. 

## Table of Contents 

1. [What is MegaDetector](#what-is-megadetector)
2. [What MegaDetector does](#what-megadetector-does) 
3. [What we will do today](#what-we-will-do-today)
4. [Before we begin](#before-we-begin) 
5. [Camera trap data](#1-camera-trap-data) 
6. [Download MegaDetector](#2-download-megadetector) 
7. [Clone git files](#3-clone-git-files) 
8. [Testing testing testing](#4-testing-testing-testing) 
9. [Running MegaDetector](#5-running-megadetector)
10. [Reading data into Timelapse](#6-reading-data-into-timelapse) 
11. [Applying MegaDetector in your research](#applying-megadetector-in-your-research) 


## What is MegaDetector?

MegaDetector is a free, open-source image recognition system designed to detect wildlife, humans, and vehicles in camera trap images. 

Created by Microsoft and trained on millions of images from afround the world, this algorithm can be used to help automate the processing of images at far faster rates than would be possible by relying on manual human labor alone. 

<p align="center">
  <img src="https://i.imgur.com/YHjuxaN.gif" width="600"/>
</p>

Image credit [eMammal](https://emammal.si.edu/). Video created by [Sara Beery](https://beerys.github.io/).

### Background

**Camera traps** are rugged cameras, deployed in the field, which are automatically triggered by passing animals, unobtrusively collecting data on the abundance, distribution, and behavior of medium- and large-bodied vertebrates. Data from camera traps can be used to address ecological questions and tackled conservation conundrums. The improved reliability and continually decreasing cost of camera traps has resulted in their enthusiastic uptake by the ecological community - and, concommitantly, an exponential increase in the amount of camera trap footage gathered every year. 

Once collected, the images need to be transformed into the kinds of data that can be used in statistical analyses (numbers). Each picture must be manually reviewed to classify what species it contains, and often, how many individuals there are, their characteristics (e.g., age, sex, health), and their behavior. Unfortunately, with average-sixed camera trap projects ([~78 cameras or larger](https://esajournals.onlinelibrary.wiley.com/doi/10.1002/fee.1448)) now amassing millions of photos annually, the rate at which we capture data now [far exceeds](https://www.cambridge.org/core/journals/environmental-conservation/article/wildlife-insights-a-platform-to-maximize-the-potential-of-camera-trap-and-other-passive-sensor-wildlife-data-for-the-planet/98295387F86A977F2ECD96CCC5705CCC) our ability to process those images. The time and labor required to annotate these images - sometimes, years pass! - can mean that survey results are less relevant to practitioners by the time analysis is complete.  

Frustratingly, a huge proportion of the time wildlife ecologists and conservation biologists invest in reviewing camera trap images is spent going through data they aren’t even interested in – that is, empty images with no wildlife or those containing non-focal species, like people or vehicles. The good news is, **artificial intelligence (AI)** can help accelerate this process, removing these “noise” images and allowing biologists to spend their time on images that matter!   

While AI is not a perfect replacement for the human eye/brain, integrating AI algorithms into camera trap workflows can massively accelerate research and conservation by alleviating data bottlenecks. 

<p align="center">
  <img src="https://i.imgur.com/o23m8HA.png" width="600"/>
</p>

Image credit: [Meredith Palmer](https://www.meredithspalmer.weebly.com) / [Snapshot Serengeti](https//www.snapshotserengeti.org) 

## What MegaDetector does 

### Image Recognition: Detectors vs. Classifiers

MegaDetector is **not** an image classifier! When you run MegaDetector, you will not learn what type of animal is present in your camera trap image (other than the broad classification of 'wildlife', 'human', or 'vehicle'). Rather, MegaDetector is a kind of AI that we call an image *detector*, which tells you whether there is a thing in the image and where it is. 

From Saul Greenberg's [Timelapse Image Recognition Guide](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseImageRecognitionGuide.pdf):

- A *detector* detects whether something is in an image or not. For each
suspected detection, it:
   - assigns a coarse identifying label to it (e.g., empty, animal, person, vehicle)
   - locates it via **bounding box** coordinates, which can be used to draw a rectangle around the suspected entity
   - assigns a confidence value indicating the likelihood that it is correct

- A *classifier* performs a fine-grained classification on an image by choosing a prediction from a set of known categories. A classifier will typically produce a list of possible classifications for each detection. For example, wildlife classification, categories will be wildlife species (e.g., elk, wolf, bear, dog). The recognizer includes a probability value that very roughly indicates the likelihood that the classification is correct.

Classifiers need to be trained for specific target specis in specific system - there is no 'MegaClassifier' for all species in all systems! (although check out emerging tools like [Wildlife Insights](https://www.wildlifeinsights.org/) that are trained on global camera trap data sets). For more information on pre-trained classifiers, Dan Morris maintains a [GitHub repository](https://agentmorris.github.io/camera-trap-ml-survey/) of available tools and resources. 

### How does this help? 

Ugh, we will still have to go through all the images and identify each species, what a scam! Or is it? - why is information from a detector useful? 

1. **Detectors can help eliminate empty images**: 

An obscene number of images collected by camera traps ([up to >70%](https://hcjournal.org/index.php/jhc/article/view/123/1152021) of dataset in some cases) can contain *absolutely no animals at all!* Empty or 'blank' images can be triggered by waving vegetation, rain, excessive heat, or other environmental conditions that might cause your camera trap to go a little wonky. This might also occur when the camera is set to timelapse mode (i.e., taking photos every hour rather than waiting to be triggered). 

2. **Detectors can also be used to flag images containing people or vehicles**:

If your dataset will be shared (such as with [citizen scientists](www.zooniverse.org) or [collaborators](www.lila.science), you should probably remove any issues captured of people to respect their privacy. While rarely their primary function, camera traps can be used to surveil people without their consent, raising a whole host of ethical issues. As with empty images, if data from images containing people or vehicles is not important to your analysis, you can eliminate them from the classification pile. 

- For a great introduction to the ethics of conservation technology, check out the WILDLABS Tech Tutors discussion ["How do I use Conservation Tech Ethically?"](https://www.wildlabs.net/event/how-do-i-use-conservation-tech-ethically) and Koustubh Sharma's paper
[Conservation and people: Towards an ethical code of conduct for the use of camera traps in wildlife research](https://besjournals.onlinelibrary.wiley.com/doi/10.1002/2688-8319.12033). 

3. **Detectors make manual classification a LOT easier**: 

The detector pinpoints where in the image you need to look - you'll then be classifying an animal vs. classifying an animal and lots of noisy background. Studies have found that incorporating a image detector into a camera trap classification pipeline can [increase efficiency >500% over manual labelling](https://www.sciencedirect.com/science/article/pii/S2351989422001068#:~:text=One%20such%20model%2C%20MegaDetector%2C%20is,2019a%2C%20Microsoft%2C%202020) while still maintaining high accuracy and precision.

4. **Detectors make AI classification a little easier too**:

The information provided by the bounding boxes (zeroing in on the wildlife in the image) is also helpful if AI classifiers are then applied to try and sort your data by species. By cropping images to individual animals, classifiers only need to worry about identifying the wildlife pixels, rather than trying to figure out backgorund pixels as well. It's also a way to introduce counting (how many bounding boxes are in each image?) and handle images that contain multiple species.

### Under the hood

Typically, we would not recommend using an 'off-the-shelf' AI algorithm that hadn't been trained on your specific data - the huge variability in terms of species, geography, background, etc. can mean that even an algorithm that works exceptionally well for the camera trap images it is trained on might perform poorly even on new location within the very same system! 

But wait - we're talking now about an image classifer. When Sara Beery and her team took [millions of labeled camera trap images from around the world](https://lila.science), they found that an animal ***detector*** not only performed on species and sites used in the training data, but also generalized exceptionally well to animals and place that it had never 'seen' before. Thus, MegaDetector was born. 

So what kind of AI is MegaDetector? MegaDetector is an *Faster R-CNN* object detection model. **CNN** stands for **"convolutional neural network"**, a type of artificial neural network used in image recognition and processing that is specifically designed to process pixel data. CNNs evaluate image data in a hierarchical fashion similar to the way mammalian brains process visual information. They are trained to identify objects of interest by learning how to associate raw inputs (i.e., pixel values) to labelled image data. CNNs are a powerful class of models because they are able to learn and extract complex visual features without direct supervision of a human expert. 

<p align="center">
  <img src="https://i.imgur.com/CEfmAGd.jpg" width="600"/>
</p>

Image credit: Siyu Wang, ["How do I get started with MegaDetector?"](https://www.wildlabs.net/event/how-do-i-get-started-megadetector) 

For more information on how artificial neural networks are trained, visit our **Intro to AI packet**. To dig into more technical details about CNNs and deep learning models, check out Sylvain Christin's excellent paper ["Applications for deep learning in ecology"](https://besjournals.onlinelibrary.wiley.com/doi/full/10.1111/2041-210X.13256)

### Important considerations 

Robots are not taking our wildlife ecology jobs yet! Here are a few things to keep in mind when deploying MegaDetector: 

1. **Productivity gain is small if not many non-empty images**: This might be self-obvious, but as a tool for alleviating manual labor for camera trap image processing, MegaDetector has a very particular goal. 

2. **Image recognition is not perfect**: There is a tendency for those not terribly familiar with AI to treat them as infallible 'black boxes' that produce high quality output every time. AI will make mistakes, even with the best training data available. Camera trap images are particularly tricky (for humans and AI!), particularly when animals are difficult to discern against their background or distorted by occlusion, unusual perspectives, camouflage, and more. 

<p align="center">
  <img src="https://i.imgur.com/z386lxe.jpg" width="600"/>
</p>

Image credit: [Sara Beery](https://beerys.github.io/) et al. 2018 ["Recognition in Terra Incognita"](https://openaccess.thecvf.com/content_ECCV_2018/papers/Beery_Recognition_in_Terra_ECCV_2018_paper.pdf)

To learn more about situations under which image recognition works and where it can fail, you can check out ["Automated Image Recognition for Wildlife Camera Traps: Making it Work for You"](https://prism.ucalgary.ca/bitstream/handle/1880/112416/2020-08-Greenberg-ImageRecognitionCameraTraps.pdf?sequence=6) from Timelapse creator Saul Greenberg. 

## What we will do today

### Image classification pipeline

When you run MegaDetector, the program produces a JSON recognition file that contains information on which images MegaDetector predicts contain objects of interest (animals, people, vehicles), where those objects are in the image, and the confidence that the algorithm has in its classification.  

***Sidebar: What is a JSON file?*** JSON stands for "JavaScript Object Notation" and is a text-based file format that stores simple data structures and objects. These files are Jlightweight, text-based, human-readable, can be edited using a text editor. 

Hm, okay - how is this useful? We then need to integrate this information into a camera trap image processing pipeline. Here, we'll be interfacing with [Timelapse](https://saul.cpsc.ucalgary.ca/timelapse), the recommended 'front-end' for visualizing and using MegaDetector recognition data. This nifty program allows researchers to visualize and encode data, and has features that include: 
   - reading, organizing, and displaying images
   - automatically extracting information (dates, times, locations) from images
   - allowing you to create a custom interface for entering data specific to your project 
... among many others! You can read more about Timelapse's functionality in their [QuickStart Guide](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseQuickStartGuide.pdf). 

Importantly for us, the MegaDetector team have collaborated with Timelapse to make it easy for us to upload our images and JSON predictions into Timelapse, and do things like visualize bounding boxes on camera trap images and filter images (based on model confidence level) to review only those that likely contain wildlife.

<p align="center">
  <img src="https://i.imgur.com/2aRdnkN.png" width="600"/>
</p>

Image credit: Saul Greenberg

***Sidebar: Other options*** Not a fan of Timelapse? MegaDetector has (self-described) "somewhat-less-complete" integrations with the [eMammal desktop application](https://github.com/microsoft/CameraTraps/blob/master/api/batch_processing/integration/eMammal) and with open-source image organizer [dikiKam](https://github.com/microsoft/CameraTraps/tree/master/api/batch_processing/integration/digiKam). The MegaDetector team has also created [Python tools](https://github.com/microsoft/CameraTraps) that use MegaDetector results to sort images into different folders for "probably-empty", "probably-animal", "probably-human", and "probably-vehicle". This is a useful function to get rid of images that MegaDetector suspects are empty, allowing you to continue with your established camera trap classification pipeline with fewer blank images. 

### Plan of attack: 

1. First, we will run a simple script that will process one camera trap image: the AI will attempt to locate the object of interest and add a bounding box around the area it predicts the object to be located. This is pretty neat to see, but running through images one at a time is not particularly useful! Rather, we will use this as a test to make sure that your environment is set up correctly. 

2. Next, we will run a script that processes entire batches of images in one go. This script produces a JSON file that we will use to view and sort our images in a camera trap image analyser. 

3. We will then be loading our resultant JSON files and camera trap images into [Timelapse](https://saul.cpsc.ucalgary.ca/timelapse) for manual review of important images and additional data extraction.  

### Important notes

- The following instructions are for Windows and Mac machines: please follow the instructions particular to your operating system.  
- We will be using Python, but no coding knowledge required! We'll navigate you to your terminal, into which you can copy and paste the commands listed here. It should be plug and chug, but I'll be there to help trouble-shoot any issues that arise! 

## Before we begin... 

Before we can run MegaDetector, we need to install the following prerequisites: 

1. **Anaconda**: Anaconda is a free, open-source data science platform that helps users manage Python packages. You can download Anaconda [here](https://www.anaconda.com/products/individual).

On Windows, Anaconda will create two different comand prompts: 'Anaconda Prompt (anaconda3)' and 'Anaconda Powershell Prompt (anaconda3)'. **Use the 'Anacdona Prompt (anaconda3)' prompt** not the powershell prompt. For Macs, anaconda will run directly in your terminal. 

2. **Git**: Git is a free, open-source version control software, i.e., a program for coordinating among programmers by tracking changes in sets of files. 

- For Windows: download [Git for Windows](https://www.git-scm.comd/download/win). 

- For Macs, you can use Homebrew (a package manager) to download Git. If you haven't already installed Homebrew, open up your terminal and type: 

```batch
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Hit 'return'. When Homebrew has finished downloading, type the following into your terminal and hit 'return': 

```batch
brew install git 
```

3. **[Mac Users Only] Windows Emulator**: Unfortunately, Timelapse does not run on Macs (boo!). If you are working on a Mac, you will need to fire up a Windows emulator when we are ready to upload our AI predictions. An emulator allows one computer system (the "host", aka your Mac) to imitate the functions of another computer system (the "guest", aka Windows), enabling you to run software that would otherwise only run on the guest system. 

If you do not already have a Windows emulator installed, you have several options: 

- Partition your hard drive using **Boot Camp**

Apple's Boot Camp allows you to install Windows alongside macOS; this is a lot faster than running a virtual machine (see below). This program comes on your Mac free of charge. You will need to back up your Mac, have sufficient space on your hard drive to install Windows, and purchase a Windows license to use. However, only one operating system can run at a time, so you'll have to restart your Mac to switch between macOS and Windows. 

More instructions on setting up your hard drive using Boot Camp can be found [here](https://support.apple.com/en-us/HT201468). 

- Use **Wine** to run individual Windows programs

[Wine](https://www.winehq.org/) is a compatability layer that allows Windows applications to run on other operating systems, which is a fancy way of saying that instead of booting up an entire new OS or creating a virtual machine, you can run individual applications from your macOS. This option does not require purchasing a Windows license. Once you install Wine, you can right-click on the .exe file for Timelapse and select 'Open With > Wine' to fire up the program. 

More instructions on how to use Wine can be found [here](https://macexpertguide.com/run-windows-applications-on-a-mac-with-wine/); you can download the latest version of Wine [here](https://wiki.winehq.org/Download). 

However, **Wine does not run on macOS versions >12.5**; if your Mac is up-to-date, then this solution **will not work**. 

- Create a virtual machine 

Virtualization programs like **Parallels** or **VirtualBox** create a Virtual Machine that mimics the hardware of a Windows PC. This allows you to run Windows programs alongside Mac programs without rebooting. However, as both operating systems are running at the same time, this option can be a little slow, along with taking put a lot of memory and storage space. 

There are a number of different virtualization options to choose from, including: 
   - [Parallels](https://www.parallels.com/products/desktop/welcome-trial/): Easy to use, up to date with current Mac versions, but costly; however, you can get a free two-week trial to test it out! 
   - [VWMWare Fusion](https://www.vmware.com/products/fusion/fusion-evaluation.html): Free for personal use, but hasn't been update in a while (may run into compatibility issues with newer macOS); less user-friendly than Parallels
   - [VirtualBox](https://www.virtualbox.org/): Also free, and also not quite as easy to use as Parallels

**For this workshop, I recommend downloading the free trial of [Parallels](https://www.parallels.com/products/desktop/welcome-trial/)** and exploring alternative options if this is a tool you will be using in the future. 

4. **Timelapse**: Download and install Timelapse according to the instructions on the [Timelapse website](https://saul.cpsc.ucalgary.ca/timelapse/pmwiki.php?n=Main.Download2). 

## 1. Camera trap data

Download and extract **[this zipped file](https://drive.google.com/file/d/14Z72gCNshxs-qQZWkYA8bx1gCnWKiI5V/view?usp=sharing)**. Store the images in a folder on your Desktop called "cameratrap_images". (Image attribution: [Saul Greenberg](https://saul.cpsc.ucalgary.ca/timelapse/pmwiki.php?n=Main.UserGuide)). 

Feel free to use your own camera trap data if you have it! For purposes of this demonstration, we recommend using <50 images (for fast processing time). Either copy a sample of your data to a folder on your Desktop as per above or be sure to update the file directory structure in the following code to point the program to where your files are stored.

## 2. Download MegaDetector 

We'll be working with the latest version of MegaDetector (MDv5b) for this workshop. 
- Create a folder on your Desktop called 'megadetector' 
- Download [MDv5b](https://github.com/microsoft/CameraTraps/releases/download/v5.0/md_v5b.0.0.pt) to this folder 

## 3. Clone git files 

The developers of MegaDetector have created three git repositories ('repos') - *cameratraps*, *ai4eutils*, and *yolov5* - that contain utilities and tools necessary to run the MegaDetector model. You can install these to your computer by doing the following: 

### Instructions for Windows

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
cd c:\git\cameratraps
conda activate cameratraps-detector
set PYTHONPATH=%PYTHONPATH%;c:\git\cameratraps;c:\git\ai4eutils;c:\git\yolov5
```

### Instructions for Mac

*First time*: open up your terminal; you should see "(base)" at your command prompt. Now run: 

```batch
mkdir ~/git
cd ~/git
git clone https://github.com/Microsoft/cameratraps
git clone https://github.com/Microsoft/ai4eutils
cd ~/git/cameratraps
conda env create --file environment-detector-mac.yml
conda activate cameratraps-detector
export PYTHONPATH="$PYTHONPATH:$HOME/git/cameratraps:$HOME/git/ai4eutils:$HOME/git/yolov5"
cd ~/git
git clone https://github.com/ultralytics/yolov5/
cd ~/git/yolov5
git checkout c23a441c9df7ca9b1f275e8c8719c949269160d1
cd ~/git/cameratraps
``` 

This has created your MegaDetector environment! When running MegaDetector in the future, you only need to run: 

```batch
cd ~/git/cameratraps
conda activate cameratraps-detector
export PYTHONPATH="$PYTHONPATH:$HOME/git/cameratraps:$HOME/git/ai4eutils:$HOME/git/yolov5"
``` 

## 4. Testing testing testing 

Note: all the scripts we will be using have already been downloaded when you set up your environment!

Let's start out by running a script ([run_detector.py](https://github.com/Microsoft/CameraTraps/blob/master/detection/run_detector.py)) that puts bounding boxes around animals detected in a single image at a time. This is not a good method for going through large batches of images! Rather, we're using this script to test whether everything has been set up correctly. 

For both operating systems, decided what image in your **cameratrap_images** folder you'll be using to test the MegaDetctor. In the following script, replace **some_image_file.jpg** with the name and extension of that image.

### Instructions for Windows 

Into your Anaconda prompt, type the following: 

```batch
cd c:\git\cameratraps
python detection\run_detector.py "C:\Users\MPalmer\Desktop\megadetector\md_v5b.0.0.pt" --image_file "C:\Users\MPalmer\Desktop\cameratrap_images\some_image_file.jpg" --threshold 0.1
``` 

Note: You may need a longer path name to get to your Desktop. To check, right click on your Desktop > Properties. For my computer, I need to update "c:\Desktop\" in the code below to "c:\Users\MPalmer\Desktop".

Go into the **cameratrap_images** folder - if this script worked correctly, you should see a new file called "some_image_file_detections.jpg" with bounding boxes around the objects of interest! 

### Instructions for Mac

Into your terminal, type the following: 

```batch 
cd ~/git/cameratraps
python detection/run_detector.py "$HOME/Desktop/megadetector/md_v5b.0.0.pt" --image_file "some_image_file.jpg" --threshold 0.1
```

Go into the **cameratrap_images** folder - if this script worked correctly, you should see a new file called "some_image_file_detections.jpg" with bounding boxes around the objects of interest! 

## 5. Running MegaDetector 

Now we're reading to dive in with some batch processing! Here, we'll be using a different script, [run_detector_batch.py](https://github.com/Microsoft/CameraTraps/blob/master/detection/run_detector_batch.py). This doesn't create bounding boxes on your images; rather, it outputs a JSON file with information on predictions (what is in the image) and locations (where is it in the image) that can be read into Timelapse. 

### Instructions for Windows 

```batch
cd c:\git\cameratraps
python detection\run_detector_batch.py "C:\Users\MPalmer\Desktop\megadetector\md_v5b.0.0.pt" "C:\Users\MPalmer\Desktop\cameratrap_images" "C:\Users\MPalmer\Desktop\cameratrap_images\test_output.json" --output_relative_filenames --recursive --checkpoint_frequency 10000
```

Again, don't forget to update the paths to your Desktop if necessary! 

This will produce a file called "c:\Users\MPalmer\Desktop\cameratrap_images\test_output.json"

### Instructions for Mac

```batch
cd ~/git/cameratraps
python detection/run_detector_batch.py "$HOME/Desktop/megadetector/md_v5b.0.0.pt" "$HOME/Desktop/cameratrap_images" "$HOME/Desktop/cameratrap_images/test_output.json" --output_relative_filenames --recursive --checkpoint_frequency 10000
```

This will produce a file called "c:/Desktop/cameratrap_images/test_output.json"

## 6. Reading data into Timelapse

The following instructions come from Saul Greenberg's [Timelapse Image Recognition Guide](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseImageRecognitionGuide.pdf), which you should consult for more information and troubleshooting tips! 

The sample camera trap data comes with a file called "TimelapseTemplate.tdb". This is a file that specifies what data fields will be displayed in the Timelapse interface. You can check out the [Timelapse Image Recognition Guide](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseImageRecognitionGuide.pdf) for information on how to create your own template (e.g., with fields for collecting the specific data you are interested in). 

Make sure that this file and the JSON file are in the same folder as your camera trap image data (**cameratrap_images**). 

### Importing the image data 

Remember, Timelapse can only be run on a **Windows OS**: if you are using a Mac, now is the time to fire up your Windows emulator. 

To open Timelapse, go to the downloaded and extracted **'Timelapse2-Executables > Timelapse'** folder and double-click on the **Timelapse2.exe** file. 

This will open up the Timelapse interface. To load the image data, select **'File > Load templates, images, and video files'**. Navigate to your **cameratrap_images** folder and select the **TimelapseTemplate.tdb** file. The images will appear in your Timelapse interface. 

### Importing the MegaDetector data

Select **'File > Import image recognition data'**; navigate to your **cameratrap_images** folder and select the **test_output.json** file. Hurrah! You should now see the bounding boxes appear around the entities detected in your camera trap images. 

<p align="center">
  <img src="https://i.imgur.com/X096AP7.png" width="600"/>
</p>

The bounding boxes are color-coded to distinguish the detection category (e.g., red for person, blue for animal); the boxes are also labeled with what MegaDetector predicts the entity to be (e.g., person, animal) and its confidence in that prediction (ranging from 0 [low confidence] to 1 [high confidence]). 

Use the left and right arrow keys to flip between your files, noting the accuracy of the predictions. In some images, bounding boxes may appear where there are no animals (*false positives*); in other cases, animals in the image may have escaped detection (*false negatives*). 

<p align="center">
  <img src="https://i.imgur.com/96F22PK.png" width="600"/>
</p>

One thing you can do is play around with the **confidence threshold** over which Timelapse displays bounding boxes (i.e., only assign a classification when the algorithm is >0.8 confidence) - for different image sets, the value you set here may differ. But remember! AI algorithms will never be 100% perfect, hence the need for quick, manual review in programs like Timelapse.

## Applying MegaDetector in your research 

### Timelapse functionality 

[Timelapse](http://saul.cpsc.ucalgary.ca/timelapse/) has an incredibly degree of functionality, enabling you to automatically pull MegaDetector classifications into a spreadsheet, extract additional image exif data, record or update classifications, and quickly through the review of large quanities of images very quickly. We won't go into this process here, but be sure to check out the Timelapse [QuickStart Guide](https://saul.cpsc.ucalgary.ca/timelapse/pmwiki.php?n=Main.UserGuide) and accomanpying [video tutorials](https://saul.cpsc.ucalgary.ca/timelapse/pmwiki.php?n=Main.Videos) for easy-to-follow instructions on setting up your image classification pipeline! 

### Evaluating MegaDetector performance 

While we will not do a deep-dive during this workshop into assessing MegaDetector's performance, here are some key metrics that can be used to evaluate how well the model works on your data. 

***Types of errors*** 

[Saul Greenberg](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseImageRecognitionGuide.pdf) highlights four types of errors to be on the look-out for when running an image recognition algorithm: 
- *False positives*: The algorithm DOES detect an entity when there is NO entity (i.e., in an empty image)
- *False negatives*: The algorithm DOES NOT detect an entity when the entity is present  
- *Incorrect identification*: The algorithm DOES detect an entity when an entity is present, but incorrectly labels it (e.g., [simple detector] a person gets labelled as wildlife, or [classifier] a warthog gets labelled as a mongoose) 
- *Amiguity*: The algorithm detects several overlapping and possibly conflicting detections

***Accuracy metrics***

There are three descriptive accuracy metrics that can be used to assess model performance: 

**1. Overall accuracy** is calculated by dividing number of true predictions by the total number of predictions. This metrics quantifies the performance of the model over all species. For example, an overall model accuracy of 0.7 means that for every 10 images - regardless of classes - the model can correctly classify 7 images.

**2. Precision** is calculated by dividing the number of true positives by the total number of images that are classified as positive (i.e., containing an entity). This result tells you about the data quality of each class classified by the model. For example, precision of 0.7 for 'animal' class implies that for every 10 images that are classified by the model as 'animal', 7 of them are correct. 

**3. Recall** is calculated by dividing the number of true positives by the total number of ground-truth positive images. This statistic quantifies the ability of the model to detect each species. For example, recall of 0.8 for elk means that if there are 10 images of elk in the dataset, the model is able to detect 8. 

### Batch processing camera trap data 

You've just had a successful field season - hurrah! You return to home base with... terabytes and ... oh god, more terabytes ... of camera trap data. How quickly can MegaDetector plow through your images?  

The number of images you can process in a single day depends on the computer resources you have available. Siyu Yang provices the following guidelines: 

- 5K-10K images per day on typical mid-range laptop with no GPU
- 100K images per day on a not-quite-top-of-the-line-but-pretty-decent GPU (i.e., a gaming computer or in the cloud) 
- alternative, you can submit images to the MegaDetector team: they have 16 GPU and can process 1.6M images per day (*note: uploading the images to servers accessible to MegaDetector team can take a long time depending on the number of images and your internet connectivty) 

### Want to learn more?

Here are some additional MegaDetector tutorials to step you through the MegaDetector pipeline: 
- ["How do I get started with MegaDetector?"](https://www.wildlabs.net/event/how-do-i-get-started-megadetector) by Siyu Wang
- ["How do I get started using Machine Learning for my camera traps?"](https://www.wildlabs.net/event/how-do-i-get-started-using-machine-learning-my-camera-traps) by Sara Beery
- ["Camera Trapping Software"](https://www.youtube.com/watch?v=7B_sf7mnpkc) by Sara Beery

### Need more help?

Contact Dan Morris (cameratraps@lila.science) to guide you through the MegaDetector process. 

