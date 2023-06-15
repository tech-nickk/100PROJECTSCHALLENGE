# People Counting Device Documentation

## Introduction
The People Counting Device is a system designed to accurately count the number of people entering or exiting a building. It utilizes the Arduino Nano 33 BLE Sense, which comes with a built-in gesture sensor and Bluetooth capability. This documentation provides an overview of the device, its features, use cases, and advantages.

## Features
- Built-in gesture sensor: The Arduino Nano 33 BLE Sense incorporates a gesture sensor (APDS9960), allowing it to detect movements and gestures accurately.
- Bluetooth communication: The device uses Bluetooth Low Energy (BLE) to wirelessly transmit the count data to another Arduino Nano 33 BLE Sense or compatible device.
- Real-time counting: The device provides real-time counting of people entering or exiting a building, enabling accurate and up-to-date data.
- Compact and portable: The small form factor of the Arduino Nano 33 BLE Sense makes the device easy to install discreetly and transport if necessary.

## Building it
## Prerequisites
 - Two Arduino Nano 33 BLE Sense boards
 - USB cable for Arduino Nano 33 BLE Sense
 - Computer with Arduino IDE installed or the Arduino web editor


## Usage
The device can be installed on top on on the side of the door frame.
The device is magnetic so it can be attached to metalic door frames without much struggle. It also has a built in battery so no external power connection is required.
To reset the device simply press the button on the front part.
Wait for a few seconds for the device to pair with the central device after powering it on.
Once the both devices are paired it will start taking counts and sending it to the central device.
Several peripheral devices can be used to take poll at different entrances or exits.


## Use Cases
- Retail stores: Monitor customer footfall to analyze peak hours, optimize staffing, and measure the effectiveness of marketing campaigns.
- Libraries: Track visitor numbers to assess demand, improve resource allocation, and understand usage patterns.
- Events and venues: Count attendees for capacity management, safety planning, and evaluation of event success.
- Public spaces: Gather data on pedestrian traffic to optimize infrastructure planning and urban design.
- Transportation hubs: Monitor passenger flow to enhance crowd management and improve operational efficiency.

## Advantages
- Accuracy: The built-in gesture sensor of the Arduino Nano 33 BLE Sense provides reliable and precise counting of people entering or exiting a building.
- Wireless communication: The Bluetooth Low Energy capability enables seamless transmission of count data to another Arduino Nano 33 BLE Sense or compatible device without the need for physical connections.
- Ease of deployment: The compact size and portability of the device allow for easy installation and relocation as needed.
- Cost-effective: The use of Arduino Nano 33 BLE Sense and the built-in gesture sensor provides an affordable solution for people counting compared to more complex and expensive alternatives.

## Troubleshooting


## Resources
- Link to Arduino Nano 33 BLE Sense documentation: [Arduino Nano 33 BLE Sense Documentation](https://www.arduino.cc/en/Guide/NANO33BLESense)
- Link to the APDS9960 gesture sensor datasheet: [APDS9960 Datasheet](https://www.sparkfun.com/datasheets/Components/General/APDS-9960.pdf)

## Contribution Guidelines


## Conclusion
Summarize the key points covered in the documentation, emphasizing the benefits and advantages of using the people counting device. Encourage users to explore the possibilities and provide contact information for further assistance or support.


