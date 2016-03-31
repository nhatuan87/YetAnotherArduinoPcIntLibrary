Yet Another Arduino PcInt Library
=================================

As you may have guessed, this is an Arduino library to handle Pin Change Interrupts.

Features
--------
- Easy to install and use
- Callbacks provide useful arguments:
  - _A User-provided pointer_:

    This can be an object with some contest, a string to print, or to whatever else you want.

    This is specially useful when developing components that can be instantiated many times.
    
  - _The pin's state_:

    That's the very first thing you were going to do inside the callbacks, wasn't it?
    
    Adding this as an argument prevents issues when the pin state changes so quickly that you don't have the time to call `digitalRead()`
    
- It should support `RISING`/`FALLING`/`CHANGE` modes

- Code is very efficient (It's an ISR, after all)

- Code is very small (~250 lines) and readable


About Pin Change Interruptions
------------------------------

AVR microcontrollers only have a few external Interruption pins.
But I want to monitor more pins... Looots more!

The alternative is using pin change interrupts, which is supported on lots of pins simultaneously.

The interrupt can be enabled for each supported pin individually, but there are only a few interrupt vectors, so up to 8 pins share the same service routine.

It's up to the software to figure out which pins changed state and act accordingly. This library does it for you :)


Comparisson with other libraries
--------------------------------

As the name suggests, there are many other libraries out there for handling Pin Change Interruptions.

In particular, this project started as a fork of [Sodaq_PcInt](https://github.com/SodaqMoja/Sodaq_PcInt) and 
uses [PinChangeInterruptBoards.h](src/PinChangeInterruptBoards.h) from [NicoHood's PinChangeInterrupt Library](https://github.com/NicoHood/PinChangeInterrupt)

I've looked at many of them before I decided to create this library, and this is how they compare.

- [PcInt example code](http://playground.arduino.cc/Main/PcInt) from [arduino.cc](http://www.arduino.cc/)
  - [ ] Not available on Arduino's library manager (It's not really a library, after all)
  - [ ] Doesn't support user data on or pin state on callbacks
  - [X] Supports `RISING`/`FALLING`/`CHANGE` modes
  - [ ] Not very optimized
  - [X] Code quality is Okay
  - [ ] I'm not sure it will work on all AVR Arduinos without changes

- [Sodaq_PcInt](https://github.com/SodaqMoja/Sodaq_PcInt) by [SODAQ](https://github.com/SodaqMoja)
  - [X] Available on Arduino's library manager
  - [ ] Doesn't support user data on or pin state on callbacks
  - [ ] Doesn't supports `RISING`/`FALLING`/`CHANGE` modes (Unless you are using [GabrielNotman's fork](https://github.com/GabrielNotman/Sodaq_PcInt))
  - [X] Very optimized
  - [X] Excelent code
  - [X] Should support all/most AVR Arduinos

- [PinChangeInterrupt Library](https://github.com/NicoHood/PinChangeInterrupt) by [NicoHood](https://github.com/NicoHood)
  - [X] Available on Arduino's library manager
  - [ ] Doesn't support user data on or pin state on callbacks
  - [X] Supports `RISING`/`FALLING`/`CHANGE` modes
  - [X] It's super-optimized and configurable, but...
  - [ ] The code is a bit messy (IMHO, anyway)
  - [X] Should on all/most AVR Arduinos

- [EnableInterrupt](https://github.com/GreyGnome/v) by [Mike "GreyGnome" Schwager](https://github.com/GreyGnome)
  - [X] Available on Arduino's library manager
  - [ ] Doesn't support user data on or pin state on callbacks, but does provide the pin number via the `EI_ARDUINO_INTERRUPTED_PIN` macro
  - [X] Supports `RISING`/`FALLING`/`CHANGE` modes
  - [X] It's super-optimized and configurable, but...
  - [ ] Big monolithic messy code
  - [X] Should support all/most AVR Arduinos (It also supports External Interrupts!)

- [PciManager](https://github.com/prampec/arduino-pcimanager) by [Balazs Kelemen](https://github.com/prampec)
  - [X] Available on Arduino's library manager
  - [ ] Doesn't support user data on callbacks, but does provide pin state
  - [ ] Doesn't supports `RISING`/`FALLING`/`CHANGE` modes
  - [ ] Not very optimized
  - [X] Good code quality
  - [X] Should support all/most AVR Arduinos


Examples
-------

... TODO ...
Here is a quick example to get you started

```
#include <Sodaq_PcInt.h>
void setup()
{
  pinMode(A0, INPUT_PULLUP);
  PcInt::attachInterrupt(A0, handleA0);
}

void loop()
{
  // ...
}

static uint8_t rain1ticks;
void handleA0()
{
  static bool rain1State;
  //rain counter 1
  if (digitalRead(A0) == LOW) {
    if (!rain1State) {
      rain1ticks++;
      rain1State = true;
    }
  } else {
    rain1State = false;
  }
}
```

Most important functions
------------------------
* PcInt::attachInterrupt
