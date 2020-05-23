---
layout: post
title: Quick and Dirty Hardware RNG for ESP32!
title_suffix: <span>:hourglass::loop::watch::hourglass_flowing_sand:</span>
---

A couple days back while working with an ESP32 dev board I came across a problem whose solution required me to simulate certain signals within my embedded system.

The numbers that were to be simulated needed to look fairly realistic across a certain bounded interval. Now this is a perfect use case for the good ol' [_random()_](https://www.arduino.cc/reference/en/language/functions/random-numbers/random/) function within the Arduino IDE. 

And most readers who have used this pseudo-random generator would recall that this function needs to be __properly randomly seeded__ right before any executions of the function (other wise the seemingly random pattern that the function generates is identical for every subsequent reboot run). We use the [_randomSeed()_](https://www.arduino.cc/reference/en/language/functions/random-numbers/randomseed/) function to accomplish this at the very beginning of the program execution.

Now, I tried using the typically recommended seeding method of [_analogRead()_ on a NC pin](https://www.arduino.cc/reference/en/language/functions/random-numbers/randomseed/) but it did not work since I kept getting a constant voltage on it (not too random indeed!). My next option was to try seeding via the internal temperature sensor on the ESP32, since I thought that there was one on the newer versions. Turns out I was wrong. In the newer versions the internal temperature sensor has been deprecated in some cases.

The remaining option was to some how quickly utilize the hall sensor that comes with the micro-controllers to create some random numbers. Here is a bit of trickery that I used: 

```c++
// This function outputs a random number based on internal hall sensor readings on ESP32
unsigned long HardwareRNG (int accumulations) {
    unsigned long seed;
    unsigned long cHallReading;
    int iterations = 0;

    while (iterations < accumulations) {
        cHallReading = abs(hallRead());
        if (cHallReading != 0) {
        seed = seed * cHallReading;
        if (seed == 0) {
            seed = 1;
        }
        iterations++;
        }
    }

    return seed;
}
```
A sample usage would be as follows:

```c++
unsigned long randomNumber;

void setup() {
    // start thr serial interface:
    Serial.begin(115200);
}

void loop() {
    // run the RNG and output to serial:
    randomNumber = HardwareRNG(100);
    Serial.println(randomNumber);
    delay(100);
}
```

Note that I am not an expert in Random Number Generators or Probability Theory or Statistics so I can totally imagine that there is still some kind of pattern or the so called oracle attack vector from the outputs of this function. But for my application this worked just fine. Remember this is a __Quick & Dirty__ Method.

Next steps in validating and further improving this would be to pass this through some kind of statistical pattern analyzer (not yet sure what the technical term for that is :cry:) and then try to iterate based on that. Lets leave that up for another post!

Let me know what you think of the above way and if you have any suggestions please leave a comment!  

#### TL;DR
On the ESP32 micro-controllers there are built-in sensors like the internal temperature sensor and the hall sensor which can be used to generate hardware random numbers. They can be a good alternative in place of the floating analog voltage for simple seeding of the built-in pseudo RNG while creating randomness in your embedded systems!
