---
title: "Getting “avrdude: initialization failed, rc=-1″ with ATmega328 after pulled it from Arduino?"
date: "2014-04-29"
categories:
  - "diy"
  - "microcontrollers"
---

I’ve spent the entire day solving this issue :) While all ATtiny85 were
responsive and happy, both ATmega328 refused to work with avrdude throwing the
error below while working perfectly with Arduino.

![avrdude_1]({{ site.baseurl }}/assets/avrdude_1.png)

The problem was that Arduino uses a 16Mhz external crystal as chip’s clock, and
chip is pre-programmed to use it. Just add a 16 Mhz crystal with two 22 pF
capacitors as at picture below:

![ATmega_crystal]({{ site.baseurl }}/assets/ATmega_crystal.png)

And microcontroller will respond to avrdude:

![avrdude_2]({{ site.baseurl }}/assets/avrdude_2.png)

As far as I know, microcontrollers can be instructed to use internal clock
instead of external crystal, but I am yet to find out how to do this.

In general, while crystal takes extra 2 pins off AVR, it enables microcontroller
to run at a better (and more precise) frequency. In my small projects I plan to
use ATtiny mostly with internal clock since it only has 8 pins. With ATmega, I
probably will use a crystal more often, but I pulled it from Arduino and started
all this AVR programming hobby because I wanted as much compact solutions as
possible, with no to minimal extra stuff, so I still prefer more pins to a
faster clock.
