# Artificial Intelligence for Wildlife Conservation 

<p align="center">
  <img src="https://www.ft.com/__origami/service/image/v2/images/raw/http://prod-upp-image-read.ft.com/a6fcca88-112a-11ea-a7e6-62bf4f9e548a?source=next&fit=scale-down&quality=highest&width=1440" width="900"/>
</p>

Image credit: Ian Bott 

## Tech is transforming wildlife conservation 

In the ongoing quest to better understand and conserve wildlife populations, technology-enabled sampling methods have become increasingly important. On-animal and remote sensors such as camera traps, passive acoustic monitors, tracking collars, drones, and satellites enable us to non-invasively study animal occurrence, abundance, distribution, and behavior and to perform targeted interventions to mitigate activities threatening animals and ecosystems. 

### Sensors
Recent advances in sensor technologies are drastically increasing data collection capacity by reducing costs and expanding coverage relative to conventional methods, opening new frontiers for ecological studies at scale. 

> Many previously inaccessible areas of conservation interest can now be studied through the use of high-resolution remote sensing, and large amounts of data are being collected non-invasively by digital devices such as camera traps, consumer cameras, and acoustic sensors. New on-animal biologgers, including miniaturized tracking tags and sensor arrays featuring accelerometers, audiologgers, cameras, and other monitoring devices document the movement and behavior of animals in unprecedented detail, enabling researchers to track individuals across hemispheres and over their entire lifetimes at high temporal resolution.
> 
> (*From* [Perspectives in machine learning for wildlife conservation](https://www.nature.com/articles/s41467-022-27980-y))

<p align="center">
  <img src="https://i.imgur.com/w4FFuPD.png" width="600"/>
</p>

Image credit: ["Perspectives in machine learning for wildlife conservation"](https://www.nature.com/articles/s41467-022-27980-y)

In this workshop, we'll be focusing on wildlife data collected from two sources: **camera traps** and **bioacoustic sensors**. 

***Camera traps*** are remote senors which are automatically triggered by passing animals to take images or video, thereby unobtrusively collecting data on medium- and large-bodied vertebrates. Camera traps are inexpensive, easy to use, and can provide a wide variety of information (e.g., species, sex, heath, age, behavior, ecological interactions) on entire ecology communities. These data can be used to assess occurrence, richness, distribution, density, and species interactions. 

- To learn more about camera trapping, check out WWF's comprehensive [Guide to Camera Trapping](https://www.wwf.org.uk/sites/default/files/2019-04/CameraTraps-WWF-guidelines.pdf)

***Bioacoustic sensors***, also known as ***acoustic monitors***, capture sound instead of images. These microphones or hydrophones (underwater microphones) record soundscapes, which can be analyzed to detect what species are active in a given area, when these animals are active, and in some cases, what they are doing (e.g., feeding, mating, interacting). Networks of sensors can be used to derive the distribution, occupancy, density, and richness of vocalizing species. Inexpensive, open-source accoustic sensors such as the [AudioMoth](https://www.openacousticdevices.info/audiomoth) are making these types of studies increasingly popular. 

- WWF also has a great [Guide to Acoustic Monitoring](https://www.wwf.org.uk/sites/default/files/2019-04/Acousticmonitoring-WWF-guidelines.pdf)  

## The problem  

<p align="center">
  <img src="https://i.imgur.com/GvQVWNV.png" width="600"/>
</p>

Image credit: [Sara Beery](https://beerys.github.io/)

While inexpensive and accessible sensors are accelerating our ability to collect ecological data, one big hurdle curtails us from using these data for large-scale ecological understanding: the massive about of time and labor needed to distill the piles and piles information collected into data that can be used for analysis and learning. 

For example, more than a [million camera traps](https://www.nature.com/articles/s41467-022-27980-y) are currently used to monitor biodiversity worldwide. Each of these camera trap can collect thousands or tens of thousands of photographs every every year. The millions of resulting images have to be manually sorted to classify the species they contain. Often, the amount of labor and time required means that images don't get processed until months or years after they are collected, severely limiting their application in conservation or management practice and reduces research productivity.

Similarly, acoustic monitors continuously gather audio data that then has to be reviewed by researchers to identify and classify calls or noises of interest. Networks of acoustic sensors generate collect hundreds or thousands of hours of audio during a single field deployment; when it takes longer to review an audio file than the length of the file itself, these datasets quickly become overwhelming. When responding quickly to critical noises - for instance, sending out rangers to investigate the gunshot of a poacher - is key, the delay between data collection and processing reduces these devices' usefulness for conservation applications. 

<p align="center">
  <img src="https://i.imgur.com/7RtB0Oi.jpg" width="600"/>
</p>

How can we process these data on timescales (and on budgets) that make them relevant to wildlife conservation? 

## AI to the rescue! 

> By combining new machine learning approaches with ecological domain knowledge, animal ecologists can capitalize on the abundance of data generated by modern sensor technologies
> 
> (*From* [Perspectives in machine learning for wildlife conservation](https://www.nature.com/articles/s41467-022-27980-y))

With increasingly sophisticated artificial intelligence (AI) algorithms, advanced computing infrastructures,and the increasing availability of large and diverse labeled training datasets (e.g., https://lila.science for camera trap iamges or www.macaulaylibrary.org for birds calls), we are increasingly able to build accurate and robust machine learning solutions to the ecological Big Data classification problem.  

The goal of AI is to build a good predictive model, a model that takes input data such as a camera trap image or audio file, and is able to accurately predict what species or behavior is being recorded.

## What is artificial intelligence?

<p align="center">
  <img src="https://i.imgur.com/TNDF3fR.png" width="600"/>
</p>

Image credit: [McClure et al. 2020](https://www.sciencedirect.com/science/article/pii/S2666389920301434)

So what exactly is **artificial intelligence**? AI is intelligent software-based technology that receives information from the environment and then takes actions based on that information that affect the environment. At it's core, AI is the ability for machines to simulate and enhance human intelligence. 

A subset of AI is **machine learning**. Machine learning algorithms are a collection of mathematical and statistical models that learn representations from the underlying training data. Basically, machine learning algorithms extract patterns from input data in order to come up with the rules and the parameters of these models, so that they can make smart predictions and decisions when confronted with new, unseen inputs. 

A subset of machine learning is **deep learning**. Deep learning models are basically very complex, highly versatile machine learning models which use artificial neural networks to process information. 

**Artificial neural networks** are models that process information in a way inspired by the architecture of our brains, with highly interconnected processing units called neurons working together to solve problems. From a technical standpoint, neural networks have three main parts: (a) an input layer that receives the data, (b) an output layer that gives the result of the model, and (c) the processing core that contains one or more hidden layers. To put it more basically, neural networks are simply a function mapping input onto a desired output. They allow computer-based algorithms to “learn” based on training data, in order to accurately process similar yet distinct data at a later time.  

IMAGE HERE 

### Learning from data 

What is exciting - and powerful - about deep learning algorithms is this ability to learn features from data. In a process called **"supervised learning"**, a labeled dataset (i.e., a dataset where all of the classes of interest have been added - for instance, a camera trap dataset where every image is labeled with whether the picture contains a zebra, an elephant, or a mongoose) is given to a computer so that it can train to associate the labels (classifications) to the examples (data). 

This differs from conventional machine learning, where a user has to specify exactly what the algorithm has to detect. For instance, to detect a zebra, the user would have to code what specific properties of a zebra (e.g., shape, color, size, pattern) to look for. Deep learning algorithms automatically detect and extract features from data. This means that we only need to tell a deep learning algorithm whether a zebra is present in a picture and, given enough examples, it will be able to figure out by itself what a zebra looks like. 

[Alexander Fred-Ojala](https://www.coursera.org/instructor/~23954429) explains... 

>Traditional algorithms rely on parameters and rules defined by humans; data are then processed according to these rules to produce results.

<p align="center">
  <img src="https://i.imgur.com/07d7iPV.png" width="300"/>
</p>

>Machine learning algorithms, on the other hand, extract rules from data; parameter values and rules are decided during the training process. In order to train a supervised machine learning algorithm, you only have to provide it with answers together with input data and out would come the rules. These rules can then be used in order to predict answers and outputs on data that it hasn't been trained on before. 

<p align="center">
  <img src="https://i.imgur.com/Es57Hm3.png" width="370"/>
</p>

We'll be working primarily with deep learning models today, but may use these terms interchangeably. 

### Key terms

There are a few additional definitions to keep in mind as we go through this workshop: 

-	**Algorithm**: A sequence of instructions to a computer, like a recipe on how to process information
- **Model**: An artifact you get after feeding data into a machine learning algorithm (feed inputs, get output)
- **Inference**: The process of running inference is feeding the input data through the model to get output (i.e., some kind of prediction) 

## How machine learning works 

### Before you being: when is machine learning useful? 

Consider first what can and cannot be addressed using machine learning. Machine learning is not always the right tool for the job! Machine learning is a good approach when you have: 

- Lots of clean, labelled data
- A tightly scoped problem 
- Some margin of error is okay 

Maybe consider an alternative option to machine learning if:

- Conditions vary beyond original dataset
- There is no data readily available
- There is no tolerability of error 

***Sidebar: What kinds of data can you use with ML?*** Many kinds of data can be fed into machine learning algorithms. For those thinking about using AI for wildlife ecology and conservation applications, you can train ML to help classify: 

- Images (e.g., camera trap, satellite, drone)
- Audio (e.g., bioacoustic monitoring) 
- Time series sensor data (e.g., vibration, temperature, acceleration) 
- Positional data (e.g., GPS location, vertical position)
- ... or any combo of the above  

PUT SOME IMAGES HERE 

### Machine learning workflow 
1. Formulate your question in terms of what is observed and what answer you want the model to predict
2. Obtain a clean, representative dataset for training 
3. Train your model (an interative process) 
4. You have a model! (or you gave up)
5. Monitor your model periodically to make sure it is working 

Below, we'll dive deeper into putting together a dataset and training your model: 

### Obtain a clean, representative dataset 

***Labeled data***: ML problems start with data for which you already know the target answer - what we call **"labeled data"**. In supervised ML, the algorithm teaches itself to learn from the labeled examples that we provide. 

Each example/observation in your data must contain two elements:
- The answer that you want to predict. You provide data that is labeled with the correct answer to the ML algorithm to learn from. Then, you will use the trained ML model to predict this answer on data for which you do not know the correct answer.
- Variables/features: Attributes that can be used to identify patterns to predict the correct answer. If the classes that you are trying to use ML to distinguish do not differ, you may have problems running inference using your model. 

***Representative data***: When collecting data, be thinking about what you are trying to classify and whether the data truly represents that class. From Edge Impulse's ['Introduction to Embedded Machine Learning'](https://www.coursera.org/learn/introduction-to-embedded-machine-learning/lecture/fARmQ/data-collection): 

<p align="center">
  <img src="https://i.imgur.com/DEsftmt.jpg" width="600"/>
</p>

> For example, let's say we are trying to create a model that classifies pictures of dogs, and we feed it a bunch of pictures of poodles as our training data. We then test the model with an image of a Welsh Corgi. Do you think that the model will be able to classify this as a dog or not? And the answer is, it depends. Maybe the model generalizes enough to pick out common features like eyes, a snout, round nose, and so on. However, what you'll likely find is that the model trained on features unique to what we gave it in the training data. Such as, curly fur, and floppy ears. We'll have a hard time classifying new images that do not have these features. Most modern machine learning algorithms are terrible at generalizing and we must take great care to ensure that the data they learn from represents the data we ultimately want them to work with. 

Note also that your model needs to be exposed to all the things you want it to understand: if your model is trained to distinguish different breeds of dogs and you show it an image of a cat, it will attempt to classify the cat as one of the dog breeds. 

***Balanced data***: In addition to having data from multiple difference classess, the data across classes should be **balanaced**. That means having approximately equal numbers of samples in each class. For example, if you have four classes you want to distinguish, you should have ~25% of samples for each class. Note: the balance of data in your training set may not reflect the distribution of data in the real world! You may not expect to encounter one of your classes very frequently in the real world; however, your classifier will perform poorly on this class if it is trained on fewer samples of this class than of the other classes. 

### 3. Train your model 

Data is randomly divided into a **training set**, a **validation set**, and a **test set**.

During training, the training set and associated labels will be used to update the parameters in the machine learning model as the model figures out how to associate labels and data. 

The model's performance is then evaluated on the validation set, which is data that - until now - it has never seen before. If the model performs poorly on the validation set, we can go back, tweak, and retrain the model. The new model will again be tested on the validation set, and the entire process repeated until weare happy with how the model performs. 

Only now do we run the model on the test set. The performance metrics we get when running the model on the test set tell us how good the model is at accomplishing its task. We keep the test set separate from the validation step because every time we iteratively retain and test the model on the validation data, it learns some of the characteristics of the validation dataset. The test data is a check - the model could perform very well on the validation data but poorly on the test data if it has been "overfit" to the input data, i.e., has memorized the data it has been exposed to and is unable to generalize to unseen examples. 

<p align="center">
  <img src="https://meredithspalmer.weebly.com/uploads/1/1/8/5/118542972/holdoutmethod_orig.png" width="600"/>
</p>

Image credit: Edge Impulse ['Introduction to Embedded Machine Learning'](https://www.coursera.org/learn/introduction-to-embedded-machine-learning/lecture/fARmQ/data-collection)

## What will we do today? 

Today, we'll cover how to use pre-trained models for classifying camera trap images (**MegaDetector**) and how to build your own models for processing audio data (**Edge Impulse**). Below is a brief overview of the algorithms/programs we'll be working with: 

### MegaDetector 

MegaDetector is a free, open-source image recognition system designed to detect wildlife, humans, and vehicles in camera trap images. Created by Microsoft and trained on millions of images from afround the world, this deep learning algorithm can be used to help automate the processing of images at far faster rates than would be possible by relying on manual human labor alone by identifying which images do not contain wildlife. 

<p align="center">
  <img src="https://i.imgur.com/e9A9boY.png" width="600"/>
</p>

Image credit: [Sara Beery](https://beerys.github.io/)

### Edge Impuse 

Edge Impulse is a platform for developing machine learning algorithms, specifically those designed to work on "edge" devices (e.g., on sensors, processing data as it is collected in the field). We will use the Edge Impulse interface to train an algorithm which can detect gunshots from passive acoustic monitors - this type of algorithm could then be deployed onto a passive acoustic sensor and be used to alert rangers or park managers to illegal poaching incidents. 

<p align="center">
  <img src="https://i.imgur.com/Bo9GXW9.png" width="600"/>
</p>

Image credit: [Edge Impulse](https://www.edgeimpulse.com/)

## Other opportunities and future directions 

Previously inaccessible areas of ecological and conservation interest can now be studied in intense detail thanks to new break-throughs in remote sensing and conservation technology. In addition to camera traps and acoustic sensors, ML can be employed to help process and analyse data from: 
- On-animal sensors (e.g., GPS trackers, accelerometers, microphones, video cameras, heart-rate monitors)
- Remote sensing footage (e.g., unmanned aerial vehicles (UAVS) or drones, satellites)
- Crowd-sourced data (e.g., data from iPhones, images collected by iNaturalist) 
   - As a note, crowdsourcing classifications through platforms such as the [Zooniverse](https://zooniverse.org) can help produce the [massive labeled image datasets](https://www.nature.com/articles/sdata201526?origin=app) necessary to train AI algorithms 

AI are also becoming more sophisticated, extracting more different kinds of information from Big Data. For instance, camera trap image recognition algorithsm are not only getting better at detecting and classifying species, but now are being trained to extract trickier metrics, such as: 
- Counts (how many animals are there?)
- Behavior (what are they doing?)
- Multiple species (classifying >1 species per image) 
- Individual re-identification (recognizing specific individuals) 
- 3D recovery of animal pose (what is the animal doing) 
- 3D reconstructions of the environment (what is the animal navigating) 

For a more in-depth discussion, check out ["Perspectives in machine learning for wildlife conservation"](https://www.nature.com/articles/s41467-022-27980-y) and ["Applications for deep learning in ecology"](https://besjournals.onlinelibrary.wiley.com/doi/full/10.1111/2041-210X.13256).  
