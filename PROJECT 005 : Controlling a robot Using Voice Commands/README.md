# Controlling a Robot using voice commands

## Introduction

In this project we will use Edge Impulse with an Arduino Nano 33 BLE Sense to run a simple Artificial Neural Network that can recognize keywords in speech. We will use the embedded digital microphone on the Nano 33 BLE Sense, the MP34DT05, to listen to our surroundings and we will navigate our robot when a keyword is detected via Bluetooth.


## Goals
The goals of this project are:

- Learn Machine Learning fundamentals.
- Use Edge Impulse with an Arduino Nano 33 BLE Sense board.
- Run a simple Artificial Neural Network that can recognize keywords in speech.
- Send data in Arduino Nano 33 BLE Sense boards via Bluetooth
- Deploy our 1st TinyML model

## Prerequisites
To work on this project, you will need the following :

- Two Arduino Nano 33 BLE Sense boards
- Arduino IDE or Arduino Web Editor
- Edge Impulse platform to train simple Artificial Neural Network.
- Basic understanding of Arduino programming and Bluetooth concepts

### Hardware

- 4-wheeled/ 3-wheeled robot chassis
- Two Arduino Nano 33 BLE Sense boards
- Motor driver module
- Batteries or power source
- Jumper wires

## Getting Started

### Setting up the Nano 33 BLE sense

1. Download the latest Nano 33 BLE Sense board Edge Impulse firmware here https://cdn.edgeimpulse.com/firmware/arduino-nano-33-ble-sense.zip . Unzip the file in a known location in your computer.

2. Open the Flash script for your computer's operating system (flash_windows.bat, flash_mac.command or flash_linux.sh).

3. Wait until the firmware flashing is complete and press the RESET button of your board once to launch it.


### Setting up Edge Impulse

An edge device refers to a hardware component responsible for managing the movement of data between two networks. These devices serve as entry or exit points within networks. Edge Impulse is a prominent development platform dedicated to machine learning on edge devices. Their objective is to empower developers and device manufacturers worldwide to address real-world challenges by utilizing machine learning models on edge devices. By leveraging these capabilities , we can create a machine learning system or model and deploy it on Nano 33 BLE Sense board.

First, create an Edge Impulse account , and create a new project called voice_control.

![Creating a new project](https://github.com/tech-nickk/100PROJECTSCHALLENGE/blob/8b0377b21e73ee609aed4f66b1da5c4fe04197c8/PROJECT%20005%20%3A%20Controlling%20a%20robot%20Using%20Voice%20Commands/photos/create%20project.png)


### Creating and Curating a Dataset

We are ready to start acquiring data for our model. Let's train a ML model that would let you identify keywords in speech, with the keywords: front, back, right, left and stop.

The first step is to create a representative dataset of the selected keywords that the ML model is supposed to identify. On Edge Impulse, navigate to Data acquisition on the left menu and then go to the USB icon on the upper right corner and click it to connect your boatrd through web USB. NOTE that you must be using Chrome or Microsoft Edge browser for this to work!. On the Sensor option select the built-in microphone. Set the sample length (in milliseconds) to 2,500 and leave the Frequency (in Hz) as 16,000.


### Collecting New Data

Now, in the Label write front and click on the Start sampling button. This will start sampling your Nano 33 BLE Sense board built-in microphone for 2500 milliseconds. In this period of time say the keyword front, but remember to have the microphone close to you. Record at least 50 samples and repeat this also for the other keywords, back, right, left and stop. You should now start seeing the collected data (each recorded sample) and a graph of each recorded sample on Edge Impulse.

After recording your first sample you can listen back to it to make sure the recording is clear and there is no disturbing background noise.

### Recording a sample.

Also, in complete silence and without saying anything, record 50 more samples with the label noise. This samples are going to help the ML model identify when no keywords are being spoken. In total, you should get around 8 minutes of collected data with 4 different labels.

This is a very basic example of data collection with Edge Impulse. If you want to train a more robust model follow the recommendations below:

1. Recorded samples should be one to three seconds long.

2. Each sample should contain only one utterance of the keyword.

3. Try to get at least 50 samples. 100 samples is better, over 1000 samples is best. The more samples the better.

4. Samples should contain a variety of pronunciations, accents, inflections, etc.


### Creating an Impulse

Now that we have all the data samples, it's time to design an impulse. An impulse, in a nutshell, is how your ML model is being trained, is where you define the actions that are going to be performed on your input data to make them better suited for ML and a learning block that defines the algorithm for the data classification. Navigate to Impulse design on the left menu and then select Add a processing block and add Audio (MFCC), then select Add learning block and add Classification (Keras). Keep all of the settings at their defaults for each block. Click on the Save impulse button.

### Generating the Impulse Features

Now, let's generate the features from the input data. With features we are referring to the unique properties of our collected data that will be used by the classification algorithm, to detect keywords in speech. For now, do not change the default settings and parameters.

Click on the Save parameters button and then go to the Generate features tab and click on the Generate features button. This process might take some time to complete depending on the size of your dataset. When the process is done you can inspect the obtained results.

On the right side you can see a representation of your dataset features.

### Training the ML Model

Now that you have your dataset features ready to be used, navigate to NN Classifier on the left menu. Keep the settings at their defaults.

Next, click on Start training to train the ML model, this might take some time to complete depending on the size of your dataset. Once it's done, you will see some statistical parameters that tell you how good the model performed during the validation process. You should see a high accuracy for each parameter, something near to 100%. If the statistical parameters results are poor, something far from 100%, you should take a look into your dataset and cure it by removing the not representative recordings.


### Using the ML Model

Now that you have your ML model, its time to test it with an edge device. The ML you trained is already optimized for its use with embedded hardware such as microcontrollers. Deploying the ML model to your Nano 33 BLE Sense board is really simple, just navigate to Deployment on the left menu.

In the Create library section, click on Arduino library and then, on the bottom, click on the Build button. This will start a process where Edge Impulse creates a library for your Arduino board that has the ML model you have trained. When the building process is done, your browser should start downloading the generated library.

Now, open the Arduino library you just created with Edge Impulse. Remember to disconnect your board from Edge Impulse by closing the command prompt or terminal. Open your Arduino IDE and navigate to Sketch, select Include library and click on Add .ZIP Library.... Go to your Downloads folder and select the .ZIP file generated by Edge Impulse. Click on Open. The library should be installed and ready to be used and tested.

Navigate to File, select Examples and navigate to Examples from Custom Libraries. Here you should see an example named "voice_control Inferencing". Select the nano_ble_33_sense_microphone_continuous. This should open a sketch with the code that will let you test the ML model you trained before with Edge Impulse. Compile it and upload it to your Nano 33 BLE Sense board. Remember to select the Arduino Nano 33 BLE Sense as your board and associated serial port.

Open the Serial Monitor, you should now see the ML model working. In order to make sure its working properly, after the keyword labels (front, back, noise and stop) you should see the predictions being printed to the screen. When the ML model detects the keywords front, back, right, left or stop on speech, one of the predictions output, or probability, should go up and get closer to one.


### Modifying the Program and Using it to control our robot via Bluetooth

How can you control your robot if a keyword is detected on speech?

