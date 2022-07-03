# SpoofSense Android SDK

This repo is dedicated to Passive Liveness Detection ANdroid SDK developed at [SpoofSense](https://spoofsense.ai/ "Goto Official Website")


## Table of Contents


## Device Requirements

Devices must meet the following minimum requirements:
* Android > `5.0.1`
* Camera Resolution > `640 x 480`
* GLES > `3.1`
* Internet Connectivity


## Capture Process

1. Initialise the SDK
2. Initiate the capture with parameters
3. Camera, classes, helpers, etc are initialised
4. The capture loop starts, with a UI displaying the camera preview and guidelines to the subject
    * Obtain image from camera
    * Send to view for displaying as preview
    * Send image to backend for further processing
    * Once the backend has received enough information to make a decision, the device is signaled to stop the capture
    * Result is provided to the caller
    
**`Note:` A capture may end prematurely if a non-recoverable error occurs. Captures maybe retried as many times as is required**


## Quick Start
