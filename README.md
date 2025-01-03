# Tower-Simple Battery Device
A Simple Battery Device has the following main functions

Device control
==============

Low power mode
--------------
The device has an ability to enter low power mode (go to sleep). Before going to sleep wake-up parameters have to be set:
- Wakeup interval in milliseconds. After waking up, the device will run for x milliseconds, and go to sleep for (wakeup interval - x) milliseconds. Actually there are two wakeup interval attributes:
  - Next wakeup interval. This is actually used as wake-up time when going to sleep.
  - General wakeup interval.  Next wakeup interval is overwritten with this value after each wake-up.

As a result, after setting these parameters, the device will wake up after "Next wakeup interval" milliseconds for the first sleep, and "General sleep time" milliseconds for the rest of the sleeps. (In other words these parameters set the phase and the frequency of wakups).This feature allows to set up multiple devices to avoid waking up (and trying to transmit radio) the same time.
- A list of GPIO pins and change mode that would trigger a wake-up. A change (rising edge, falling edge, or both as specified) on any of the listed IO pins would cause the device to wake up.

The **Wake-up event** global variable contains the wake-up reason

Battery status
--------------
An analog input pin of the device measures the battery voltage. Outgoing messages should contain battery status info in the form of a pecentage value between 0 and 100. To calculate this value, the voltage values for the full and empty battery status are needed. As these values may vary by battery type, it is useful to set these values as parameters.

