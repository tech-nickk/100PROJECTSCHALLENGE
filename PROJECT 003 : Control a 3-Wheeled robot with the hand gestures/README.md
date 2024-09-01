# Gesture Controlled 3-Wheeled Robot

![Robot Demo](/images/robot-demo.gif)

The Wrist Controlled 3-Wheeled Robot project allows you to control a robot using wrist tilt gestures via Bluetooth. By utilizing the built-in accelerometer of the Arduino Nano 33 BLE Sense board, this project enables you to configure the robot's movements, including forward, backward, left, and right. I will be using the inbuilt LSM9DS1 Inertial Measurement Unit (IMU) sensor.The LSM9DS1 is a 9-axis sensor that combines a 3-axis accelerometer, a 3-axis gyroscope, and a 3-axis magnetometer. It allows you to measure acceleration, angular velocity, and magnetic field in three dimensions.

The LSM9DS1 sensor is specifically designed for low-power applications and offers high accuracy and sensitivity. It communicates with the Arduino Nano 33 BLE Sense board over the I2C (Inter-Integrated Circuit) interface. This IMU sensor is commonly used in various applications such as motion tracking, robotics, gaming, and virtual reality.

The sensor data will be read in real time and converted to the type of gesture detected. The gesture type data is the written to the peripheral bluetooth device which is the arduino in the robot.

## Getting Started

To get started with this project, follow the instructions below.

### Prerequisites

- Two Arduino Nano 33 BLE Sense boards
- 3-wheeled robot chassis
- Jumper wires
- Motor drive
- Power supply

### Hardware Setup

1. Assemble the 3-wheeled robot chassis according to the manufacturer's instructions.

2. Connect the Arduino Nano 33 BLE Sense boards to the motor driver using jumper wires.

### Software Setup



   ```cpp
   
   ```
   ```cpp
   
   ```



## Usage
Power on both Arduino Nano 33 BLE Sense boards and ensure that they are discoverable via Bluetooth.

Hold the other Arduino Nano 33 BLE Sense board on your wrist.

Tilt your wrist in the following directions to control the robot:

Tilt wrist forward: Move the robot forward.
Tilt wrist backward: Move the robot backward.
Tilt wrist to the left: Turn the robot left.
Tilt wrist to the right: Turn the robot right.
The wrist tilt gestures will be transmitted via Bluetooth to the other Arduino Nano 33 BLE Sense board, which will configure the robot's movements accordingly.

## Troubleshooting
If you encounter any issues or have trouble getting the project to work, consider the following:

Ensure that both Arduino Nano 33 BLE Sense boards are powered on and properly connected to the robot chassis.

Verify that the Bluetooth module on the receiving Arduino Nano 33 BLE Sense board is properly configured and paired with your device.

Calibrate the accelerometer if needed to improve gesture recognition accuracy.

## Customization and Extension
You can customize and extend this project in several ways:

Modify the gesture recognition algorithm to recognize additional gestures or refine the existing ones.

Integrate additional sensors or modules to enhance the robot's capabilities, such as obstacle detection or line following.

Implement a mobile app or GUI for a more user-friendly control interface.

## Resources and References

Documentation
Schematic Diagram

## License
This project is licensed under the MIT License.
