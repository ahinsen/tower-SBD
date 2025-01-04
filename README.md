# Tower-Simple Battery Device
A Simple Battery Device should support the following features:
1. low power mode with timer and IO pin wake up possibilities
2. Battery voltage is measured using an analog IO pin
3. Simple radio with a few selectable channels that can transmit /receive packets of information

#1 and #2 are available on most IOT boards, #3 can be e.g. an RFM69* module

Device control
==============

Low power mode
--------------
The device has an ability to enter low power mode (go to sleep). Before going to sleep wake-up parameters have to be set:
- Wakeup interval in milliseconds. After waking up, the device will run for x milliseconds, and go to sleep for (wakeup interval - x) milliseconds. Actually there are two wakeup interval attributes:
  - **Next wakeup interval**. This is actually used when going to sleep.
  - **General wakeup interval**.  Next wakeup interval is overwritten with this value after each wake-up.

As a result, after setting these parameters, the device will wake up after "Next wakeup interval" milliseconds after the first sleep, and "General sleep time" milliseconds after the rest of the sleeps. (In other words these parameters set the phase and the frequency of wakups).This feature allows to set up multiple devices to avoid waking up (and trying to transmit radio) the same time.
- A list of GPIO pins and change mode that would trigger a wake-up. A change (rising edge, falling edge, or both as specified) on any of the listed IO pins would cause the device to wake up.

The reason of the wakeup is stored in the following global parameters (that can be communicated as part of the device status):
- **Wake-up type** ("pin-rising", "pin-falling" or "timer")
- **Wake-up pin** (in case of a "pin" type)
  
Battery status
--------------
An analog input pin of the device measures the battery voltage. Outgoing messages should contain battery status info in the form of a pecentage value between 0 and 100. To calculate this value, the voltage values for the full and empty battery status are needed. As these values may vary by battery type, it is useful to set these values as parameters.

Communication
=============
A certain radio channel is used by exactly one gateway and one or many end-devices. There are two types of radio messages:
- Device-to-gateway messages are transmitted by the devices, typically containing readings, battery status, and the status of the previous communication.
- Gateway-to-device messages are transmitted by the gateway, typically containing device config parameters.

A communication scenario is always initiated by a device, by transmitting readings. After transmission, the device listens on the same channel for some time to receive the gateway's reply. In case of no reply, the device regards the communication scenario unsuccessful, and may retry using the same channel, then retry using a different preset channel. After a successfull scenario, or after all retries the device goes to sleep. The communication behaviour is controlled by the following device config parameters:
- **Channel list**: An ordered list of channel numbers to be used for communication. First channel number is used first, in case of failiure the second is tried etc. After each successfull communication, the successfully used channel is pushed to the 1st position of the list.
- **Channel retry count**: Number of retries using the same channel before changing to the next channel.
- **Reply timeout**: time in milliseconds to wait for the gateway's reply before considering the scenario failed.
So if the channel list contains 3 channel numbers, and the channel retry count is 2, then the maximum number of retries is 6.
- 
