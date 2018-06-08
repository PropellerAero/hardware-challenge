# Propeller Hardware Challenge

This is a high level design task similar to the problems tackled by the Propeller hardware team. We want to see how you approach open ended design problems and what you learn from working on them.


## The Problem

3D models generated from drone images are usually only useful if they are as accurate as traditional ground-based surveys. Typically an accuracy of better than 3 cm is considered “survey grade”.

The Propeller hardware team’s mission is to make it easy to reliably capture accurate data to feed into our analytics platform. We do this at the moment with [AeroPoints](https://propelleraero.com/aeropoints), our automated “ground control points” which use precise GPS to add calibration markers to the captured images.

A complementary approach to ground control is to put a precise GPS onboard your drone and use the data it collects to generate accurate location tags for the images. By knowing accurately where the camera was for each image, fewer ground control points are needed to produce an accurate model.

The goal of this task is to design a system that can produce accurate coordinates for images taken by a drone given some raw data from a precision GPS and have the results stored in a database for use in our photogrammetry (3D model generation) workflow.


## The Background

Standard consumer GPS is only accurate to a few metres, this is due to a combination of satellite orbit, clock skew and atmospheric errors that you can read about [here](https://en.wikipedia.org/wiki/Error_analysis_for_the_Global_Positioning_System).

Carrier phase double differencing is an algorithm that can produce much more accurate position data. It works by comparing observations from a pair of receivers that are relatively close (< 30 km). Because the errors affecting these receivers are very similar they can be cancelled out to produce a very accurate relative distance between them. By adding this distance to the known position of one of the receivers, the position of the other receiver can be calculated to within a few centimetres. Doing this in real time by sending the observations from one receiver to the other over a radio or cellular link is called RTK (real time kinematic). Recording the raw data from each receiver and processing it after the fact is called PPK (post processed kinematic). There is a good summary of this technique on the [Novatel website](https://www.novatel.com/an-introduction-to-gnss/chapter-5-resolving-errors/real-time-kinematic-rtk/).


## The Task

Do some research about precision GPS and come up with a very high level sketch design for a system that can produce accurate camera coordinates using differential GPS and submit them to an online database.

The format is up to you, but something along the lines of a block diagram and a few paragraphs explaining how the system works is what we’re thinking. Please don’t feel like you need to spend more than a few hours on this. Your response will be used to guide a technical interview.

We’ve left this intentionally vague to see how you handle ambiguity. We’re much less interested in the outcome than how you approach the design process.

These are the sort of questions you should think about:
- What hardware and software will need to be built?
- Should RTK or PPK be used? What are the advantages of each?
- Should the GPS processing take place on the drone, in a base station, on the customer’s laptop, on our servers or somewhere else?
- How do you match up time the image is taken with the GPS data? If the drone is travelling at 10 m/s the timing will need to be accurate to within 0.1 ms to introduce an error of < 1 mm.
- How will you get the final results to our servers?
- What are the difficult constraints to meet? Accuracy, weight, cost, power, heat, bandwidth, size or something else?
- What tradeoffs need to be made?
- How have other people solved this problem? What’s good and bad about their solutions?

Please submit your response as a private repository on Github, Bitbucket or something similar. Let us know if you have any questions.

## The Building Blocks

Here are a few hints of components that might make up part of a system:

### Precision GPS Receiver

There are several GPS receiver modules on the market for precision positioning. They all do more or less the same thing: take input from an antenna, decode the GPS signals and output psuedorange measurements over a serial interface. Psuedorange measurements are the estimated distances to each visible GPS satellite, including all of the error that needs to be cancelled out during processing. 

The [Piksi Multi](https://www.swiftnav.com/piksi-multi) is a low cost example of one of these receivers.


### RTKLIB

[RTKLIB](http://www.rtklib.com/) is an open source GPS processing software toolkit which implements the double differencing algorithm. It is available as a C library, Windows GUI or Linux command line application. It takes in raw GPS observation data in the RINEX format output by most receivers and produces accurate distances between the receivers in a number of formats.

### Amazon Web Services

The Propeller backend runs as a collection of Docker containers running on Amazon’s AWS computing infrastructure.

