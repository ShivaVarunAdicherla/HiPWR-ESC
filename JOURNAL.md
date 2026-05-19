= Journal-1 (Ideation and Picking Components)
== [Lapse link for this Journal](https://lapse.hackclub.com/timelapse/lf7XQWSmRJG7)

---

In this Journal, I put the basic idea of what the ESC will look like on paper. Helped clear up
and what specifications I should target when shopping for components. Below is the spec I've
decided on.

+ Target Input Voltage: 12-24V (I might revise this to Max 20V because I didn't find cheap Gate Drivers)
+ Input Throttle Method: DSHOT150 (and I2C if possible)
+ Max Current: 30A (Minimum)
+ Control Methods: FoC + BEMF Based 6-Step Commutation
+ Misc: Additional support for encoders incase we want FoC at low speeds

Then I drew a basic block diagram, on which blocks will connect to which components, to plan for
the inputs and peripherals I'm going to need in the Micro-Controller.

=== Micro-Controller
Based on my block diagram I needed the following:

+ Support for Timer which can atleast do 3 Channel PWM
+ ADC for Current Sensing with a Shunt resistor.
  + Would prefer a PGA (Programmable Gain Amplifier), so I have a large dynamic range
  + A fixed gain amp is also fine, but I'd lose out on my measurable current range.
+ Comparator for creating Interrupts in 6-Step Commutation Mode
  + If there are low number of PGAs or CMPs a nice internal MUX would be necessary

Searching for Mixed Signal Micro-Controller families from ST Micro, and cheaper alternatives of
the respective ICs landed me on these choices:

+ STM32F301C8T6 - $3.6
+ STM32G431KBT6TR - $2.8
+ CH32V203F6P6 - $0.64
  + I've rejected this due to it not having a flexible enough pin assignments/channels for CMPs
  + I'll have to settle for a Fixed Gain since, I'll have to use external passives to use CMPs as Amplifiers
+ CH32V303CBT6 - $1.3114
  + It has 4 OPAs so we now don't care about the flexibility, but it still suffers from not having a PGA, at the same time not exposing the outputs of the CMPs so I can't convert them to Amplifiers (I realised this when writing this journal. I'm a genius /s)

So, for now I'm planning to use **STM32G431KBT6TR** since it's cheaper and still do the job.
**Note:** I've also looked at Micro-Controllers from Giga Devices, but their clones seem to be
mostly aimed at replicating the Digital peripheral and capabilities. (Lack of PGA + CMPs) So
they were eleminated too.

=== MosFETs

For find components in this section, I just filtered based on the Max Continuous Drain Current
(Id) to a Minimum of 30A. Then I've further narrowed down the selection based on:

+ Gate Charge
  + Lower the better, since we'll have faster switching for same gate driver.
+ Max Power Dissipation
  + Lower Priority but Higher the better obviously :)
+ Rds ON
  + Lower the better, since we'll waste less power as heat
+ Max Operating Voltage
  + I thought a 30V should be the real minimum for a nice safety buffer
+ Reputability
  + I basically made no progress here, and just picked stuff which had a reasonably good datasheet

Landed on:

+ **50N06** from *KUU* - $0.085
+ XR60N03 from XNRUSEMI - $0.0655
  + I picked this since It had an English Datasheet + Rds ON is too good to pass up

=== Gate Drivers
This is the spot/point where I wasted a lot of time. I was digging the wrong specs at one point
T_T. Most of the datasheets being in Chinese didn't help. Anyways I was searching for the
following spec:

+ Drive Half Bridges with internal Shoot Through Protection
+ Large enough Source + Sink Current
+ Decent Rise + Fall Times
+ Max Input Voltage (Minimum ~ 20V)
  + Most of them don't go to 20V so, I've limited choices
  + If I don't find cheap and reasonable drivers, I'll consider adding an additional 12V rail

I wasn't satisfied with most of the drivers, but for now I think these are reasonable:

+ U2181C from UNIU - $0.35
+ EG2131D from EG Micro - $0.0783

I still want to search for more drivers this time rising my budget for this. I realised I was
being too cheap while journaling. But as a place holder U2181C for it's English datasheet, even
though it's quite expensive to it's competition.

**Note:** Oh, also I'm planning on soldering all the components by myself using an Iron and Hot
Air so, I've avoided using any QFN or it's relative packaging formats since they are really hard
to solder.


