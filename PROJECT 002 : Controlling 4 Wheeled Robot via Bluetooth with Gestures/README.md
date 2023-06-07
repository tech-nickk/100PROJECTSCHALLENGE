# Bluetooth Gesture Controlled 4-Wheeled Robot

## Introduction
This project is an advancement of the 1st project. It puts what was learnt to real life application.
The Bluetooth Gesture Controlled 4-Wheeled Robot project enables users to control a robot using hand gestures via a Bluetooth connection. The project combines gesture recognition and robotics to create an interactive and intuitive control system for the robot.
The project also enables you to to learn how to read gestures from the inbuilt gesture sensor and send the data via bluetooth.


The goal of this project is to provide a hands-free and immersive control experience, allowing users to navigate the robot using simple hand movements and gestures wirelessly.

## Project Description
The robot is equipped with Arduino Nano 33 BLE sense, a motor driver, voltage regulator and four motors for controlling the wheels. The system captures hand gestures using inbuilt gesture senseor of the Nano 33 BLE sense, and wirelessly transmits the corresponding commands to the robot via Bluetooth.

The robot interprets the received gesture commands and translates them into motor control signals, enabling it to move forward, backward, turn left, turn right, and stop.

## Prerequisites
To work on this project, you will need the following components:

- Arduino Nano 33 BLE sense X2
- 4-wheeled robot chassis
- Motor driver module (LN298)
- Batteries or power source
- Jumper wires


## Getting Started
Follow these steps to set up the project:

### Step 1: Assembling the Robot
- Assemble the 4-wheeled robot chassis according to the manufacturer's instructions.
- Connect the motors to the motor driver module and the Arduino.
- Connect the baterry supply to the Nano and Motor driver.

### Step 2: Installing the required libraries
- Install the ArduinoBLE library and Arduino_APDS9960

### Step 3: Coding the Remote
- We will configure the remote as Central bluetooth device and we will read the gestures from the on_board gesture sensor
- We will then write then write the gesture type data to the robot(peripheral device)
- Connect the Arduino to your pc
- Upload the controller code

```cpp

#include <ArduinoBLE.h>
#include <Arduino_APDS9960.h>

#define button D12


const char* deviceServiceUuid = "19b10000-e8f2-537e-4f6c-d104768a1214";
const char* deviceServiceCharacteristicUuid = "19b10001-e8f2-537e-4f6c-d104768a1214";

int gesture = -1;
int oldGestureValue = -1;  




void setup() {
  Serial.begin(9600);
  //while (!Serial);
  
  pinMode(button, INPUT);
  
  if (!APDS.begin()) {
    Serial.println("* Error initializing APDS9960 sensor!");
  } 

  APDS.setGestureSensitivity(80); 
  
  if (!BLE.begin()) {
    Serial.println("* Starting Bluetooth® Low Energy module failed!");
    while (1);
  }
  
  BLE.setLocalName("Nano 33 BLE (Central)"); 
  BLE.advertise();

  Serial.println("Arduino Nano 33 BLE Sense (Central Device)");
  Serial.println(" ");
}

void loop() {
  connectToPeripheral();
}

void connectToPeripheral(){
  BLEDevice peripheral;
  
  Serial.println("- Discovering peripheral device...");

  do
  {
    BLE.scanForUuid(deviceServiceUuid);
    peripheral = BLE.available();
  } while (!peripheral);
  
  if (peripheral) {
    Serial.println("* Peripheral device found!");
    Serial.print("* Device MAC address: ");
    Serial.println(peripheral.address());
    Serial.print("* Device name: ");
    Serial.println(peripheral.localName());
    Serial.print("* Advertised service UUID: ");
    Serial.println(peripheral.advertisedServiceUuid());
    Serial.println(" ");
    BLE.stopScan();
    controlPeripheral(peripheral);
  }
}

void controlPeripheral(BLEDevice peripheral) {
  Serial.println("- Connecting to peripheral device...");

  if (peripheral.connect()) {
    Serial.println("* Connected to peripheral device!");
    Serial.println(" ");
  } else {
    Serial.println("* Connection to peripheral device failed!");
    Serial.println(" ");
    return;
  }

  Serial.println("- Discovering peripheral device attributes...");
  if (peripheral.discoverAttributes()) {
    Serial.println("* Peripheral device attributes discovered!");
    Serial.println(" ");
  } else {
    Serial.println("* Peripheral device attributes discovery failed!");
    Serial.println(" ");
    peripheral.disconnect();
    return;
  }

  BLECharacteristic gestureCharacteristic = peripheral.characteristic(deviceServiceCharacteristicUuid);
    
  if (!gestureCharacteristic) {
    Serial.println("* Peripheral device does not have gesture_type characteristic!");
    peripheral.disconnect();
    return;
  } else if (!gestureCharacteristic.canWrite()) {
    Serial.println("* Peripheral does not have a writable gesture_type characteristic!");
    peripheral.disconnect();
    return;
  }
  
  while (peripheral.connected()) {
    gesture = gestureDetectection();

    if (oldGestureValue != gesture) {  
      oldGestureValue = gesture;
      Serial.print("* Writing value to gesture_type characteristic: ");
      Serial.println(gesture);
      gestureCharacteristic.writeValue((byte)gesture);
      Serial.println("* Writing value to gesture_type characteristic done!");
      Serial.println(" ");
    }
  
  }
  Serial.println("- Peripheral device disconnected!");
}
  
int gestureDetectection() {
  if (APDS.gestureAvailable()) {
    gesture = APDS.readGesture();

    switch (gesture) {
      case GESTURE_UP:
        Serial.println("- UP gesture detected");
        break;
      case GESTURE_DOWN:
        Serial.println("- DOWN gesture detected");
        break;
      case GESTURE_LEFT:
        Serial.println("- LEFT gesture detected");
        break;
      case GESTURE_RIGHT:
        Serial.println("- RIGHT gesture detected");
        break;
      default:
        Serial.println("- No gesture detected");
        break;
      }
    }
    return gesture;
    

}

```

### Step 4: Coding the Robot
- In this step, we will configure the Nano as a Peripheral device and we will use the received gesture data to control the motors


```cpp


#include <ArduinoBLE.h>

#define enA A6
#define in1 D8
#define in2 D9
#define in3 D10
#define in4 D11
#define enB A7

      
enum {
  GESTURE_NONE  = -1,
  GESTURE_UP    = 0,
  GESTURE_DOWN  = 1,
  GESTURE_LEFT  = 2,
  GESTURE_RIGHT = 3,
  GESTURE_STOP = 4
};

const char* deviceServiceUuid = "19b10000-e8f2-537e-4f6c-d104768a1214";
const char* deviceServiceCharacteristicUuid = "19b10001-e8f2-537e-4f6c-d104768a1214";

int gesture = -1;

BLEService gestureService(deviceServiceUuid); 
BLEByteCharacteristic gestureCharacteristic(deviceServiceCharacteristicUuid, BLERead | BLEWrite);


void setup() {
  Serial.begin(9600);
  //while (!Serial);  
  
  pinMode(LEDR, OUTPUT);
  pinMode(LEDG, OUTPUT);
  pinMode(LEDB, OUTPUT);
  pinMode(LED_BUILTIN, OUTPUT);
  
  digitalWrite(LEDR, HIGH);
  digitalWrite(LEDG, HIGH);
  digitalWrite(LEDB, HIGH);
  digitalWrite(LED_BUILTIN, LOW);
  
  pinMode(enA, OUTPUT);
  pinMode(enB, OUTPUT);
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);

  


  
  if (!BLE.begin()) {
    Serial.println("- Starting Bluetooth® Low Energy module failed!");
    while (1);
  }

  BLE.setLocalName("Arduino Nano 33 BLE (Peripheral)");
  BLE.setAdvertisedService(gestureService);
  gestureService.addCharacteristic(gestureCharacteristic);
  BLE.addService(gestureService);
  gestureCharacteristic.writeValue(-1);
  BLE.advertise();

  Serial.println("Nano 33 BLE (Peripheral Device)");
  Serial.println(" ");
}

void loop() {
  BLEDevice central = BLE.central();
  Serial.println("- Discovering central device...");
  delay(500);

  if (central) {
    Serial.println("* Connected to central device!");
    Serial.print("* Device MAC address: ");
    Serial.println(central.address());
    Serial.println(" ");

    while (central.connected()) {
      if (gestureCharacteristic.written()) {
         gesture = gestureCharacteristic.value();
         writeGesture(gesture);
       }
    }
    
    Serial.println("* Disconnected to central device!");
  }
}

void writeGesture(int gesture) {
  Serial.println("- Characteristic <gesture_type> has changed!");
  
   switch (gesture) {
      case GESTURE_UP:
      
        analogWrite(enA, 150);
        analogWrite(enB, 150);
        
        Serial.println("* Actual value: UP (red LED on)");
        Serial.println(" ");
        digitalWrite(LEDR, LOW);
        digitalWrite(LEDG, HIGH);
        digitalWrite(LEDB, HIGH);
        digitalWrite(LED_BUILTIN, LOW);
        
        digitalWrite(in1, HIGH);
        digitalWrite(in2, LOW);
        
        digitalWrite(in3, LOW);
        digitalWrite(in4, HIGH);
        
        
  
        break;
      case GESTURE_DOWN:

        
        analogWrite(enA, 150);
        analogWrite(enB, 150);
      
        Serial.println("* Actual value: DOWN (green LED on)");
        Serial.println(" ");
        digitalWrite(LEDR, HIGH);
        digitalWrite(LEDG, LOW);
        digitalWrite(LEDB, HIGH);
        digitalWrite(LED_BUILTIN, LOW);
        
        digitalWrite(in1, LOW);
        digitalWrite(in2, HIGH);
        
        digitalWrite(in3, HIGH);
        digitalWrite(in4, LOW);
        
        break;
      case GESTURE_LEFT:
      
        analogWrite(enA, 255);
        analogWrite(enB, 255);
        
        Serial.println("* Actual value: LEFT (blue LED on)");
        Serial.println(" ");
        digitalWrite(LEDR, HIGH);
        digitalWrite(LEDG, HIGH);
        digitalWrite(LEDB, LOW);
        digitalWrite(LED_BUILTIN, LOW);
        
        digitalWrite(in1, LOW);
        digitalWrite(in2, LOW);
        
        digitalWrite(in3, LOW);
        digitalWrite(in4, HIGH);

        break;
      case GESTURE_RIGHT:
      
        analogWrite(enA, 255);
        analogWrite(enB, 255);
        
        Serial.println("* Actual value: RIGHT (built-in LED on)");
        Serial.println(" ");
        digitalWrite(LEDR, HIGH);
        digitalWrite(LEDG, HIGH);
        digitalWrite(LEDB, HIGH);
        digitalWrite(LED_BUILTIN, HIGH);
        
        digitalWrite(in1, HIGH);
        digitalWrite(in2, LOW);
        
        digitalWrite(in3, LOW);
        digitalWrite(in4, LOW);

        break;
        
      case GESTURE_STOP:
      
        
        
        Serial.println("* Actual value: RIGHT (built-in LED on)");
        Serial.println(" ");
        digitalWrite(LEDR, LOW);
        digitalWrite(LEDG, LOW);
        digitalWrite(LEDB, LOW);
        digitalWrite(LED_BUILTIN, HIGH);


        break;
      default:
        digitalWrite(LEDR, HIGH);
        digitalWrite(LEDG, HIGH);
        digitalWrite(LEDB, HIGH);
        digitalWrite(LED_BUILTIN, LOW);
        
        digitalWrite(in1, LOW);
        digitalWrite(in2, LOW);
        
        digitalWrite(in3, LOW);
        digitalWrite(in4, LOW);
        
        break;
    }      
}


```

## Usage and Examples


## Troubleshooting


## Customization and Extension
Feel free to customize and expand upon this project. You can modify the code to add additional functionalities, experiment with different sensors, or implement new ways to control the RGB LED based on the received data.


## Resources and References

For more detailed information and additional resources, refer to the Arduino Documentation  (https://www.arduino.cc/...) for working with Arduino Nano 33 BLE Sense and Bluetooth connectivity.

## Licence

