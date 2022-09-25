
25-SEP-2022: THIS IS CURRENTLY A WORK IN PROGRESS


# Getting started with MegaDetector! 

**DISCLAIMER**: This packet is written for the **National Geographic Tech Tutors Workshop** run through [WILDLABS](https://www.wildlabs.net/) (5/6-Oct-2022) and outlines the steps we will take in the workshop to set up and run MegaDetector and import data for review in Timelapse. The instructions presented are largely and gratefully taken from the original [MegaDetetctor documentation](https://github.com/microsoft/CameraTraps/blob/main/megadetector.md), created and maintained by Sara Beery, Dan Morris, and Siyu Yang ([Efficiency Pipeline for Camera Trap Image Review](https://arxiv.org/abs/1907.06772), 2019). Modifications have been made to simplify the pipeline (e.g., using only the most up-to-date version of MegaDetector) and to add additional explanations or clarifications to aid understanding by a non-technical audience. We have also drawn heavily on the documentation and background provided in Saul Greenberg's excellent [Timelapse Image Recognition Guide](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseImageRecognitionGuide.pdf). We strongly recommend that you consult these original sources for additional information. 

1. [What is MegaDetector](#what-is-megadetector)
2. [What MegaDetector does](#what-megadetector-does) 
3. [What we will do today](#what-we-will-do-today)

## What is MegaDetector?

MegaDetector is a free, open-source image recognition system designed to detect wildlife, humans, and vehicles in camera trap images. 

![Alt Text](https://i.imgur.com/YHjuxaN.gif)

Image credit [eMammal](https://emammal.si.edu/). Video created by [Sara Beery](https://beerys.github.io/).



### Image Recognition: Detectors vs. Classifiers

MegaDetector is **not** an image classifier! When you run MegaDetector, you will not learn what type of animal is present in your camera trap image. Rather, MegaDetector is what we call an image *detector*, which tells you whether there is a thing in the image and where it is. 

From Saul Greenberg's [Timelapse Image Recognition Guide](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseImageRecognitionGuide.pdf):

- A *detector* detects whether something is in an image or not. For each
suspected detection, it:
   - assigns a coarse identifying label to it (e.g., empty, animal, person, vehicle)
   - locates it via bounding box coordinates, which can be used to draw a rectangle around the suspected entity
   - assigns a confidence value indicating the likelihood that it is correct

- A *classifier* analyzes the sub-image contained by the detector's bounding box, where it performs a fine-grained classification chosen from a set
of known categories. A classifier will typically produce a list of possible classifications for each detection. For example, wildlife classification, categories will be wildlife species (e.g., elk, wolf, bear, dog). The recognizer includes a probability value that very roughly indicates the likelihood that the classification is correct.

Classifiers need to be trained for specific target specis in specific system - there is no 'MegaClassifier' for all species in all systems! (although check out emerging tools like [Wildlife Insights](https://www.wildlifeinsights.org/) that are trained on global camera trap data sets). 

### So what?

Ugh, we will still have to go through all the images and identify each species, what a scam! Or is it - why is information from a detector useful? 

1. Natural way to get rid of non-animal images: 

So many images in camera trap datases (up to >70% of dataset - link to Snapshot) can contain *absolutely no animals at all!* Empty or 'blank' images can be triggered by waving vegetation, rain, excessive heat, or other environmental conditions that might cause your camera trap to go a little wonky. 

Some images might also capture people or vehicles -- typically not data interested in, but good to remove images of people before sharing data (e.g., on citizen science platform) for ethics reasons... 

2. Makes classification a LOT easier

The detector pinpoints where in the image you need to look - you'll then be classifying an animal vs. classifying an animal and lots of noisy background.

2b. This is also helpful information for AI classifiers - naturally handle multiple species in an image 

2c. Path to counting 


**The falibility of AI: AI is not taking our wildlife ecology jobs yet** 

Talk about how data needs to be manually qc'd because not perfect 100% of time 

Talk abour reliability vs recall? -- take from Phuong's report. 



You can read more about MegaDetector and check out the original documentation **here** *link*. 

## What MegaDetector does (fold in with above/below??) 

MegaDetector does ... adding a 'bounding box' (DEINFE) around objects of interest ... creating a JSON file that contains XX 

Trained on several hundred thousand bounding box images from a variety of ecosystems 

**Sidebar: What is a JSON file?** JSON stands for "JavaScript Object Notation" and is a text-based file format that stores simple data structures and objects. These files are Jlightweight, text-based, human-readable, can be edited using a text editor. 

These files can be ... integrated with ..., enabling the ... 


## What we will do today 

1. First, we will run a simple script that will process one camera trap image: the AI will attempt to locate the object of interest (wildlife, human, or car) in a camera trap image and add a bounding box around the area it predicts the object to be located. This is neat to see, but running through images one at a time is not particularly useful! Rather, we will use this as a test to make sure that your environment is set up correctly. 

2. Next, we will run a script that processes entire batches of images in one go. This script produces a JSON file that we will use to view and sort our images in a camera trap image analyser. 

3. We will then be loading our resultant JSON files and camera trap images into [Timelapse](https://saul.cpsc.ucalgary.ca/timelapse). This nifty program allows researchers to visualize and encode data, and has features that include: 
- reading, organizing, and displaying images
- automatically extracting information (dates, times, locations) from images
- allowing you to create a custom interface for entering data specific to your project 
... among many others! You can read more about Timelapse's functionality in their [QuickStart Guide](https://saul.cpsc.ucalgary.ca/timelapse/uploads/Guides/TimelapseQuickStartGuide.pdf). 

Importantly for us, Timelapse has ((worked with MegaDetector to integrate data)).  

**Important notes**: 
- The following instructions are for Windows and Mac machines: please follow the instructions particular to your operating system.  
- We will be using Python, but no coding knowledge required! We'll navigate you to your terminal, into which you can copy and paste the commands listed here. It should be plug and chug, but I'll be there to help trouble-shoot any issues that arise! 

**Want to learn more?** 
Here are some additional MegaDetector tutorials that ... 
- 

**Need more help?** Contact Dan Morris (cameratraps@lila.science) to guide you through the MegaDetector process. 

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

**(3) [Mac Users Only] Windows Emulator**: [Timelapse](https://saul.cpsc.ucalgary.ca/timelapse/) (the image analyser that we will read our data in to) does not run on Macs (boo!). If you are working on a Mac, you will need to fire up a Windows emulator when we are ready to upload our AI predictions. 

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

