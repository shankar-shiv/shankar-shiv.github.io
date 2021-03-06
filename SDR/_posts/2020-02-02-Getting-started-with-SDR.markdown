---
layout: post
title:  "Getting started with RTL Software Defined Radios"
date:   2020-02-02 16:12:55 +0800
categories: radio
mathjax: true
---

- [So What is RTL-SDR dongle ?](#so-what-is-rtl-sdr-dongle-)
- [Technical specifications](#technical-specifications)
  - [Rafael Micro R820T2](#rafael-micro-r820t2)
  - [Realtek RTL2832U](#realtek-rtl2832u)
- [How to get started with Hardware ?](#how-to-get-started-with-hardware-)
  - [Dipole Antenna Kit](#dipole-antenna-kit)
  - [Dipole Orientation](#dipole-orientation)
  - [Antenna Length](#antenna-length)
- [How to get started with Software ?](#how-to-get-started-with-software-)
  - [Common settings](#common-settings)
  - [Source](#source)
  - [Configure Menu aka Hardware settings menu](#configure-menu-aka-hardware-settings-menu)
  - [Frequency Input](#frequency-input)
  - [Radio Tab](#radio-tab)
  - [Bandwidth](#bandwidth)
  - [Filter](#filter)
  - [Squelch](#squelch)
  - [Order](#order)
  - [Correct IQ](#correct-iq)
  - [FM Stereo](#fm-stereo)
  - [FFT Display](#fft-display)
  - [Window](#window)
  - [Resolution](#resolution)
  - [S-Attack / S-Decay](#s-attack--s-decay)
  - [W-Attack / W-Decay](#w-attack--w-decay)
- [A brief introduction to Gnuradio](#a-brief-introduction-to-gnuradio)
- [Finding interesting Signals in Singapore](#finding-interesting-signals-in-singapore)
  - [Arduino code for 4333 MHz Transmitter](#arduino-code-for-4333-mhz-transmitter)


A week ago, I read a blog post of a [Gnu radio receiver with SDRPLAY](https://hackaday.com/2015/11/12/your-first-gnu-radio-receiver-with-sdrplay/). It was really fascinating to watch how a cheap TV dongle can be repurposed as a software defined radio and can used for Digital signal processing tasks. 

But what is a software defined radio ? Let me start with what is not a SDR,

1) If a typical hardware-based RF communication system can be modified with software, it is not an SDR. For example, if the radio has software parameters such as modulation schemes, bandwidth and frequency selection, it might be a software-operated radio.

2) The "software" in a SDR, does not mean that the system transfers digital data, it could very well handle analog signals too.

**A software defined radio is a RF communication system where the software is used to perform a significant amount of digital signal processing (DSP) tasks rather than a typical hardware based RF system**

Radio components such as modulators, demodulators and tuners are traditionally implemented in analogue hardware components. The advent of modern computing and analogue to digital converters allows most of these traditionally hardware based components to be implemented in software instead. 

Hence, the term software defined radio. This enables easy signal processing and thus cheap wide band scanner radios to be produced. Do keep in mind the basic layout of a RF front end which are mixer, local oscillator, IF amplifiers and filters, demodulators and amplifiers. 

<img src="/assets/img/Getting-started-SDR/SDR_concept.png" alt="SDR_concept" width="650" height="500"/>

<p align="center"> Wikipedia </p> 

<img src="/assets/img/Getting-started-SDR/Envelope_Detector.png" alt="Envelope_Detector" width="550" height="400"/>

 <p align="center"> AM demodulation in MultiSim simulation tool </p> 

## So What is RTL-SDR dongle ?

<img src="/assets/img/Getting-started-SDR/RTL_SDR.jpg" alt="RTL_SDR" width="550" height="400"/>


Contents :
* RTL-SDR
* 1x dipole antenna base with 60cm RG174 cable and SMA Male connector.
* 1x 3 meter RG174 coax cable extension
* 2x 23cm to 1 m telescopic antennas. 
* 2x 5cm to 13cm telescopic antennas.
* 1x flexible tripod mount with 1/4" male screw.
* 1x suction cup mount with 1/4" male screw.

I got an RTL-SDR dongle at [Aliexpress](https://www.aliexpress.com/store/4523039?spm=2114.search0104.3.13.5f7b2d20Zqs83H) at around SGD 40.00 which is much cheaper than on Amazon (SGD 50.00). RTL-SDR is a very cheap ~$25 USB dongle that can be used as a computer based radio scanner for receiving live radio signals in your area. The original purpose of an RTL-SDR dongle wereto recieve television signals but some genious chaps found that the raw I/Q data on the RTL2832U chipset could be accessed directly, which allowed the DVB-T TV tuner to be converted into a wideband software defined radio via a custom software driver developed by Steve Markgraf. These dongles are extremely popular dut to their low cost and open source software and drivers. 

## Technical specifications

<img src="/assets/img/Getting-started-SDR/technical_features.jpg" alt="technical_features" width="550" height="400"/>

- 500 KHz to 1.75 GHz frequency range
- 8 bits ADC resolution
- 50 Ohms (SMA connector) input impedance 
- No Transmit, Receive only
- The maximum sample rate is 3.2 MS/s (mega samples per second). However, the RTL-SDR is unstable at this rate and may drop samples. The maximum sample rate that does not drop samples is 2.56 MS/s.

### Rafael Micro R820T2

The R820T2 is a highly integrated tuner from Rafael Micro of Taiwan.

* integrated low-noise amplifier that takes the RF input
* RF filter, mixer, and automatic gain control
* minimal external components required
* I²C controlled
* 3.3V, low-power ＜ ~180mA
* "supports all digital TV standards"

### Realtek RTL2832U
The RTL2832U is a digital TV demodulator that supports a USB 2.0 interface and requires a front-end tuner. It is produced by Realtek of Taiwan.

## How to get started with Hardware ?

### Dipole Antenna Kit

The Dipole Antenna Kit allows for a simple v-dipole configuration for satellite reception.

### Dipole Orientation

<img src="/assets/img/Getting-started-SDR/V_shaped_dipole.jpg" alt="V_shaped_dipole" height="350" width="200"/>

A dipole can be used in either vertical or horizontal polarization, just by orienting it either vertically or horizontally. Signals are normally transmitted with either horizontal, vertical or right hand/left hand circular polarization (RHCP/LHCP). This is essentially the 'orientation' of a signal, and an antenna with the same polarization should be used too for best performance.

### Antenna Length

I recommend this [dipole calculator](http://www.csgnetwork.com/antennaedcalc.html). The exact length does not matter too much, but try to get the lengths as close to what the calculator says as you can. With the dipole you want both elements to be the same length.

When a signal is sent down a transmission channel, it travles at a certain velocity $$ V_{prop} $$ determined by the tranmission medium. Wavelength $$ \lambda $$ is defined by the distance travelled by the signal in $$ T $$ seconds where $$ T = \frac{1}{f} $$ and is the period of the signal of frequency f. 

distance = velocity * time

$$ \lambda $$ = $$ V_{prop} * T $$

$$ \lambda $$ = $$ V_{prop} * \frac{1}{f} $$

c = speed of light = $$ 3 * 10^{8} $$ metres/second 

f = Signal frequency i.e = 96.8 Mhz

The minimum antenna length $$ l_{min} $$ for good reception is $$ l_{min} = 0.1 * \lambda $$, where $$ \lambda $$ is $$ \frac{c}{f} $$.

For example, if a signal of frequency of 100 Mhz were to be propagated, the minimum antenna length is $$ 0.1 * \frac{3 * 10^{8}}{100 * 10^{6}}  = 0.3 metres $$ . The 0.3 metres is a practical length than compared to a signal of frequency of 100 Hz where we would require an antenna length of 300 Kilometres. 

The longer the antenna, the lower it's resonant frequency. The vice versa is the shorter the antenna, the higher it's resonant frequency . The closer you are to the resonant frequency, the signal reception is better. Remember that there is about 2cm of metal inside the antenna itself which needs to be added on. Below is a cheat sheet for various lengths and frequencies. Note that the length refers to the length of one side of the dipole only (e.g. the length that you need to extend each element out to).


For more indepth information, read at [https://www.rtl-sdr.com/using-our-new-dipole-antenna-kit/](https://www.rtl-sdr.com/using-our-new-dipole-antenna-kit/)

## How to get started with Software ?

Windows only (recommended by [https://www.rtl-sdr.com/](https://www.rtl-sdr.com/))
- SDR# (SDRSharp) 
- HDSDR
- SDR-RADIO V2
- CubicSDR

Linux
- GQRX
- CubicSDR

The steps to download and configure SDR# is extremely well written at [Quick Start Guide](https://www.rtl-sdr.com/rtl-sdr-quick-start-guide/)

Another alternative is GNU Radio, if you use Windows like me download it [here](http://www.gcndevelopment.com/gnuradio/downloads.htm)

Once the SDR# setup is done, open SDRSharp.exe.

<img src="/assets/img/Getting-started-SDR/sdrsharp_welcome_screen.png" alt="sdrsharp_welcome_screen" height="750" width="850"/>

After opening SDR# for the first time, I suggest that you immediately remember to perform the following steps : 

- Increase the RF gain from zero to a higher value in the configure menu.
  + Click the cog button (Configure Menu) located in the top left.
  + Locate the RF Gain slider and increase it to a higher value.
- Reduce the range slider on the right of the SDR# window to about -70 (for RTL-SDR dongles)
  - The range slider is located at the bottom right.
- Enable the “Correct IQ” setting to remove the center spike if using an R820T/R820T2, or enable - “Offset Tuning” in the configure menu if using an E4000/FC0012/13.
- Turn off the “Snap to grid” setting, or adjust the PPM offset accordingly.
  - The “Snap to grid” setting lies under the Radio toolbar
- Set the 'Mode' to the correct setting for the signal that you are listing to.

### Common settings

The Configure Menu holds all the settings for configuring hardware. In here are settings to control things like the RF gain and sample rate / bandwidth of the RTL-SDR. To optimize reception, you need to adjust settings in this window.

Most of the settings found in the main windows of SDR# affect the software digital signal processing (DSP) side of things. To optimize processing of the signal you need to adjust these DSP settings.

### Source

Choose your specific input device here, I chose RTL-SDR(USB) option.

### Configure Menu aka Hardware settings menu

Clicking this button opens up the configure menu. In here you can change settings like the sample rate (bandwidth) and RF gain

### Frequency Input

Use the mouse to set the desired frequency you wish to listen to here. You can either click on the tops and bottoms of each individual number to increase or decrease the value, or simply hover over the number you want to change and use the mouse wheel to change the value

### Radio Tab

Here you can choose what type of demodulation mode the signal at your currently tuned frequency should use.

<img src="/assets/img/Getting-started-SDR/Radio_toolbar.png" alt="Radio_toolbar" height="400" width="250"/>

| Mode    |        Acronym Expansion         |                                                                                                                                                                                          Explanation |
| ------- | :------------------------------: | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
| NFM     | Narrowband Frequency Modulation  |                                                                                                               Commonly used by walkie talkie radios, weather radio and most VHF/UHF digital signals. |
| WFM     |  Wideband Frequency Modulation   |                                                                                                                         This is the mode that broadcast FM stations use (e.g. radio music stations). |
| AM      |       Amplitude Modulation       | Used by broadcast AM stations that are receivable by normal shortwave radios and also used by air band voice frequencies used by aircraft and air traffic control. Some digital signals also use AM. |
| LSB/USB | Lower Side Band / Upper Sideband |                                                                                                 Used in the HF band by ham radio users to transmit voice and data efficiently with small bandwidths. |
| CW      |         Continuous Wave          |                                                                                                                                                                    Used for listening to Morse code. |
| DSB     |         Double Side Band         |                                                                                                                                                      Similar use to AM, but requires centered tuning |
| RAW     |          Raw IQ signal           |                                                                                                                                                          Used for listening or recording RAW IQ data |

### Bandwidth

Default : NFM/AM: 12500, WFM: 250000

This is the width of the shaded part of the tunable area. You can set it manually here, or by dragging the edges with the mouse.

### Filter

Changes the filter type used. Different filters have different shapes. The filter is used to select the highlighted signal in the RF window. Blackman-Harris is usually the best filter to choose and this setting almost never needs to be changed.

### Squelch

Default : OFF

Squelch is used to mute the audio when the signal strength is below the specified value. A larger value requires a stronger signal to unmute the audio. It is useful for when listening to speech as the sound of static when no one is talking will be muted.

### Order

Default : 500

Changes the filter order. You may notice when using low filter orders that signals outside of the tuned bandwidth can still be heard. Larger filter orders “tighten” or “sharpen” the band pass filter used within the tuned bandwidth/IF thus preventing signals outside of the tuned bandwidth from being heard. You will want to increase the filter order when there are strong signals near to your tuned area. Using higher filter orders can cause a greater load on the CPU, so slow PCs may need to reduce this value. 

### Correct IQ

Should usually be selected as ON. This setting removes the small but annoying center spike that is present with R820T/R820T2 RTL-SDR dongles.

### FM Stereo

Default : OFF

Will enable stereo output for broadcast radio WFM signals, but can make weak stations sound worse.

### FFT Display

The FFT display settings adjust options related to the spectrum analyzer screen and the waterfall.

### Window

Sets the type of windowing algorithm to use on the FFT, the default of Blackman-Harris 4 is the best in most cases.

### Resolution

Increasing the resolution will increase the quality of how the signal looks in the RF display and waterfall. Using a higher resolution may be useful when fine tuning, as high resolutions will allow you to see the peaks and structure of a signal much more clearly. Beware that high resolutions can slow your PC down and can cause trouble especially with single core machines. Generally, a value of at least 32768 should be used if your PC can handle it.

### S-Attack / S-Decay

Changes the amount of smoothing and averaging done in the RF spectrum display.

### W-Attack / W-Decay

Changes the amount of smoothing and averaging done in the waterfall display.

These are the common settings I use but YMMV. You can check all the settings [here](https://www.rtl-sdr.com/sdrsharp-users-guide/)

## A brief introduction to Gnuradio

"GNU Radio is a free & open-source software development toolkit that provides signal processing blocks to implement software radios. It can be used with readily-available low-cost external RF hardware to create software-defined radios, or without hardware in a simulation-like environment." - [GnuRadio](https://www.gnuradio.org/about/)

The GNURadio Companion (GRC) is a great graphical tool to build digital signal processing (DSP) applications easily. You could either use a hardware SDR or your computer's sound card for easy experimentation. Building a desktop PC from scratch isn't easy but you could source out the hardware components from [newegg](https://www.newegg.com/). 

Likewise, The GRC provides all the DSP blocks for you many python blocks to create a custom DSP application even with little programming. The drag and drop approach could ease many engineers from prototyping to production. Did I mention it's all written in python , so hack your way through it.

Here is a screenshot of a starter GRC project :

<img src="/assets/img/Getting-started-SDR/starter_grc.png" alt="starter_grc" height="800" width="650"/>

A few pointers,

* Almost every DSP block / function is available 
* Flowgraphs are easily manipulated.
* Out of tree modules(python or C++) meet custom needs
* Visualisations give the user a quick sanity check
* Everything flow downstream (No loops !)
* No event signalling between blocks (Used by other block to change behaviour)
* Free and Open Source, its GNU afterall!

## Finding interesting Signals in Singapore

Singapore is a small red dot in the asia pacific region,  I've always assumed that there's not much amateur radio action here, nor are the airways particularly friendly for trying to pull down low-power signals from a-far. 

While ham radio operators may be hard to find, the airways and seaports are some of the busiest in the world. 

The [Spectrum chart for Singapore](https://www.imda.gov.sg/-/media/imda/files/regulation-licensing-and-consultations/frameworks-and-policies/spectrum-management-and-coordination/spectrumchart.pdf?la=en) published by the IMDA does make the conventional allocations for the radio spectrum.

The [Signal Identification Guide](https://www.sigidwiki.com/wiki/Signal_Identification_Guide) is a great resource for capturing all kinds of signals. 

Here are some quick samples,

<img src="/assets/img/Getting-started-SDR/FM_Radio.png" alt="FM_Radio" height="950" width="800"/>

I am listening to 96.8 Mhz FM radio. 

If you have 433MHz RF Tx-Rx Modules lying around, you can use the Transmitter module to output a signal, and you can capture it in you SDR ! I used [RadioHead](https://github.com/PaulStoffregen/RadioHead) library to accomplish this. 

<img src="/assets/img/Getting-started-SDR/Transmitter_and_Receiver_433_MHz.jpg" alt="Transmitter_and_Receiver_433_MHz" height="650" width="500"/>

<img src="/assets/img/Getting-started-SDR/433MHz_RF_Wireless_Transmitter_Module.png" alt="433MHz-RF-Wireless-Transmitter-" height="650" width="500"/>

<img src="/assets/img/Getting-started-SDR/433_Mhz_SDR_SHARP.png" alt="433_Mhz_SDR_SHARP.png" height="650" width="500"/>


### Arduino code for 4333 MHz Transmitter

```c++
// Include RadioHead Amplitude Shift Keying Library
#include <RH_ASK.h>
// Include dependant SPI Library 
#include <SPI.h> 
 
// Create Amplitude Shift Keying Object
RH_ASK rf_driver;
 
void setup()
{
    // Initialize ASK Object
    rf_driver.init();
}
 
void loop()
{
    const char *msg = "Hello World";
    rf_driver.send((uint8_t *)msg, strlen(msg));
    rf_driver.waitPacketSent();
    delay(1000);
}
```


