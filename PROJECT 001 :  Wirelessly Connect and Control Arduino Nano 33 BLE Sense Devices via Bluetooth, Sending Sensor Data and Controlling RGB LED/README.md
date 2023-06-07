# Wirelessly Connect and Control Arduino Nano 33 BLE Sense Devices via Bluetooth, Sending Sensor Data and Controlling RGB LED

This project demonstrates how to establish a Bluetooth connection between two Arduino Nano 33 BLE Sense devices. It enables wireless transmission of sensor data from one device to another, which is then used to control the onboard RGB LED.

## Prerequisites

I will be using the following to work on this project:

- Two Arduino Nano 33 BLE Sense boards or 1 BLE sense board and a Nano 33 BLE board
- Arduino IDE or Arduino Web Editor
- Basic understanding of Arduino programming and Bluetooth concepts

## Project Description

The project consists of two main components: the sender device and the receiver device. The sender device reads sensor data from onboard geature sensor. It then wirelessly transmits this data to the receiver device via Bluetooth.

The sender device(Nano 33 BLE Sense) will act as our cenral device and the other board(either Nano 33 BLE sense or Nano 33 BLE) will serve as our peripheral device

The receiver device receives the sensor data and utilizes it to control the RGB LED on the Arduino Nano 33 BLE Sense board. The LED's color and intensity change according to the received sensor data.

## Getting Started

Steps to set up:

1. Set up the hardware by connecting the Arduino Nano 33 BLE Sense boards to your computer via USB.

2. Install these libraries, ArduinoBLE and Arduino_APDS9960. Refer to the library installation instructions for the Arduino IDE or Arduino Web Editor.

3. Upload the code for the sender device to one Arduino Nano 33 BLE Sense board.

### Sender/Central Device Code Snippet

```cpp
    /*
      BLE_Central_Device.ino

      This program uses the ArduinoBLE library to set-up an Arduino Nano 33 BLE Sense 
      as a central device and looks for a specified service and characteristic in a 
      peripheral device. If the specified service and characteristic is found in a 
      peripheral device, the last detected value of the on-board gesture sensor of 
      the Nano 33 BLE Sense, the APDS9960, is written in the specified characteristic. 

      The circuit:
      - Arduino Nano 33 BLE Sense. 

      This example code is in the public domain.
    */

    #include <ArduinoBLE.h>
    #include <Arduino_APDS9960.h>

    const char* deviceServiceUuid = "19b10000-e8f2-537e-4f6c-d104768a1214";
    const char* deviceServiceCharacteristicUuid = "19b10001-e8f2-537e-4f6c-d104768a1214";

    int gesture = -1;
    int oldGestureValue = -1;   

    void setup() {
      Serial.begin(9600);
      while (!Serial);

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

4. Upload the code for the receiver device to the other Arduino Nano 33 BLE Sense board.

### Receiver/Peripheral Device Code Snippet

```cpp

/*
  BLE_Peripheral.ino

  This program uses the ArduinoBLE library to set-up an Arduino Nano 33 BLE 
  as a peripheral device and specifies a service and a characteristic. Depending 
  of the value of the specified characteristic, an on-board LED gets on. 

  The circuit:
  - Arduino Nano 33 BLE. 

  This example code is in the public domain.
*/

#include <ArduinoBLE.h>
      
enum {
  GESTURE_NONE  = -1,
  GESTURE_UP    = 0,
  GESTURE_DOWN  = 1,
  GESTURE_LEFT  = 2,
  GESTURE_RIGHT = 3
};

const char* deviceServiceUuid = "19b10000-e8f2-537e-4f6c-d104768a1214";
const char* deviceServiceCharacteristicUuid = "19b10001-e8f2-537e-4f6c-d104768a1214";

int gesture = -1;

BLEService gestureService(deviceServiceUuid); 
BLEByteCharacteristic gestureCharacteristic(deviceServiceCharacteristicUuid, BLERead | BLEWrite);


void setup() {
  Serial.begin(9600);
  while (!Serial);  
  
  pinMode(LEDR, OUTPUT);
  pinMode(LEDG, OUTPUT);
  pinMode(LEDB, OUTPUT);
  pinMode(LED_BUILTIN, OUTPUT);
  
  digitalWrite(LEDR, HIGH);
  digitalWrite(LEDG, HIGH);
  digitalWrite(LEDB, HIGH);
  digitalWrite(LED_BUILTIN, LOW);

  
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
        Serial.println("* Actual value: UP (red LED on)");
        Serial.println(" ");
        digitalWrite(LEDR, LOW);
        digitalWrite(LEDG, HIGH);
        digitalWrite(LEDB, HIGH);
        digitalWrite(LED_BUILTIN, LOW);
        break;
      case GESTURE_DOWN:
        Serial.println("* Actual value: DOWN (green LED on)");
        Serial.println(" ");
        digitalWrite(LEDR, HIGH);
        digitalWrite(LEDG, LOW);
        digitalWrite(LEDB, HIGH);
        digitalWrite(LED_BUILTIN, LOW);
        break;
      case GESTURE_LEFT:
        Serial.println("* Actual value: LEFT (blue LED on)");
        Serial.println(" ");
        digitalWrite(LEDR, HIGH);
        digitalWrite(LEDG, HIGH);
        digitalWrite(LEDB, LOW);
        digitalWrite(LED_BUILTIN, LOW);
        break;
      case GESTURE_RIGHT:
        Serial.println("* Actual value: RIGHT (built-in LED on)");
        Serial.println(" ");
        digitalWrite(LEDR, HIGH);
        digitalWrite(LEDG, HIGH);
        digitalWrite(LEDB, HIGH);
        digitalWrite(LED_BUILTIN, HIGH);
        break;
      default:
        digitalWrite(LEDR, HIGH);
        digitalWrite(LEDG, HIGH);
        digitalWrite(LEDB, HIGH);
        digitalWrite(LED_BUILTIN, LOW);
        break;
    }      
}



5. Power on both devices and ensure they are in close proximity for the Bluetooth connection.

6. Monitor the serial output of the receiver device to observe the received sensor data and the resulting RGB LED control.

7. To make the devices workindependently without a connection to the serial monitor, coment out this part of the code '''cpp while (!Serial);

## Customization and Expansion

Feel free to customize and expand upon this project. You can modify the code to add additional functionalities, experiment with different sensors, or implement new ways to control the RGB LED based on the received data.

## Resources

For more detailed information and additional resources, refer to tis [Arduino documentation](https://docs.arduino.cc/tutorials/nano-33-ble-sense/ble-device-to-device) for working with Arduino Nano 33 BLE Sense and Bluetooth connectivity.

