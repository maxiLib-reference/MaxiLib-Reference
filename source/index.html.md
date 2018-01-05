---
title: MaxiLib Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - javascript

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - examples
  - undocumentedClasses

search: true
---

# Overview

MaxiLib is an [emscripten](http://kripken.github.io/emscripten-site/) compilation of *Maximilian*.

*Maximilian* is an open source, MIT licensed C++ audio synthesis library. It’s designed to be cross platform and simple to use. The syntax and program structure are based on the popular ‘Processing’ environment. *Maximilian* provides standard waveforms, envelopes, sample playback, resonant filters, and delay lines. In addition, equal power stereo, quadraphonic and 8-channel ambisonic support is included. There’s also Granular synthesisers with Timestretching, FFTs and some Music Information Retrieval stuff.

[http://maximilian.strangeloop.co.uk/](http://maximilian.strangeloop.co.uk/)

**Examples:**

[http://igor.gold.ac.uk/~skata001/AVC/maxiLib/examples/] (http://igor.gold.ac.uk/~skata001/AVC/maxiLib/examples/)

<!--  /////////////////////////////////////////////// -->

# maxiAudio

This is the audio context. You must always have one to produce sound with maxiLib

## methods
```javascript
var audio = new maximJs.maxiAudio();
// initialise the audio engine
audio.init();

//  channel output
audio.outputIsArray(true, 2);

var sample = new maximJs.maxiSample();
audio.loadSample("mysample.wav", sample);

audio.play = function() {
  this.output = sample.play();
}
```
### init()

initialise the audio engine

### outputIsArray(isArray, numChannels)

for multi channel sound
  - isArray = true or false
  - numChannels = 2, 4, or 8

### loadSample(sampleUrl, maxiSample)

load a sample into a maxiSample object

## properties

### play

the function that is the play loop

### output

the current value of the audio output

<!--  /////////////////////////////////////////////// -->

# maxiDelay
A simple delay line

## methods

###dl(inputSignal, delayTime, foldback)

process a signal with delay

inputSignal (any signal eg. output from an oscillator
delayTime (a value in milliseconds)
foldback (how much of the signal to feedback into the delay buffer - determines how long echos last)

<!--  /////////////////////////////////////////////// -->

# maxiEnv
An adsr envelope

## methods

```javascript
var audio = new maximJs.maxiAudio();
audio.init();

// these oscillators will help us count and play sound
var myCounter = new maximJs.maxiOsc();
var myOsc = new maximJs.maxiOsc();

var myOscOutput, myEnvOut;

var myEnvelope = new maximJs.maxiEnv();

//Timing is in ms
myEnvelope.setAttack(500);
myEnvelope.setDecay(1); // Needs to be at least 1
myEnvelope.setSustain(1);
myEnvelope.setRelease(1000);

audio.play = function () {
    //notice that we feed in a value of 1. to create an envelope shape we can apply later.
    myEnvOut = myEnvelope.adsr(1., myEnvelope.trigger);

    //phasor can take three arguments; frequency, start value and end value.
    var currentCount = Math.round(myCounter.phasor(0.5, 1, 9));

    if (currentCount == 1) {
        //trigger the envelope
        myEnvelope.trigger = 1;
    } else {
        //release the envelope to make it fade out only if it's been triggered
        myEnvelope.trigger = 0;
    }

    myOscOutput = myOsc.sawn(150);

    this.output = myOscOutput * myEnvOut;
}
```

###setAttack(time)

time = milliseconds
###setDecay(time)

time = milliseconds
###setSustain(level)

level = a value between 0.0 and 1.0
###setRelease(time)

time = milliseconds
###adsr(level, trigger)

level (the overall level of the envelope; everything will be scaled by this value)
trigger (envelope will begin attack when set to 1.0 and release when set to 0.0)

<!--  /////////////////////////////////////////////// -->

# maxiFilter
A bunch of useful filter methods

## methods

###lores(input, cutoff, resonance)

A lowpass resonant filter. Returns the filtered frequency.

input = input signal
cutoff = cutoff frequency in Hz
resonance = a value between 0.0 & 10.0
###hires(input, cutoff, resonance)

A highpass resonant filter. Returns the filtered frequency.

input = input signal
cutoff = cutoff frequency in Hz
resonance = a value between 0.0 & 10.0
maxiFFT
methods

###setup(fftSize, windowSize, hopSize)

must be called before using the FFT

fftSize = (A power of two, 1024, 512 ... etc)
windowSize = half the fftSize
hopSize = half the windowSize
###process(sig)

returns true if successful

sig = signal in
###getMagnitude(index)

get the magnitude of a particular bin

index = A number between 0 and the fftSize/2
###getMagnitudeDB(index)

get the decibels of a particular bin

###magsToDb()

perform the conversion on all bins

<!--  /////////////////////////////////////////////// -->

# maxiMix
A multichannel mixer.

## methods

###stereo(sig, outputArray, pan)

Makes a stereo mix.

sig = inputsignal
outputArray = VectorDbl array (see maxiTools)
pan = a value between 0 & 1

<!--  /////////////////////////////////////////////// -->

# maxiOsc
An oscillator with methods for a number of waveforms

## methods

```javascript
var audio = new maximJs.maxiAudio();
audio.init();

var mySine = new maximJs.maxiOsc();
var myOtherSine = new maximJs.maxiOsc();

audio.play = function() {
	// these two sines will beat together. They're now a bit too loud though..
	this.output = mySine.sinewave(440) + myOtherSine.sinewave(441);
 }
```

###sinewave(frequency)

outputs a sine wave at the given frequency between -1.0 & 1.0

###triangle(frequency)

outputs a triangle wave at the given frequency between -1.0 & 1.0

###saw(frequency)

outputs a sawtooth wave at the given frequency between -1.0 & 1.0

###square(frequency)

outputs a square wave at the given frequency between 0.0 & 1.0

###phasor(frequency)

outputs a linear ramp at the given frequency between 0.0 & 1.0

###phaseReset(phase)

reset the phase to a specific value

phase (a value between 0 & 1)

<!--  /////////////////////////////////////////////// -->

# maxiPitchShift
plays a sample at different pitches leaving the speed unchanged

## methods

###setSample(maxiSample)

sets the sample play for pitchShift to use

###play(pitch, grainLength, overlaps, startPos)

pitch (eg. 0.5 = half pitch)
grainLength (a time in seconds)
overlaps (normally 2 is good)
startPos (where to start playing in the sample - in seconds)

###setPosition(startPos)

useful for resetting a sound

###getPosition()

returns position in ???

###getNormalisedPosition()

Useful for ending sample play back

<!--  /////////////////////////////////////////////// -->

# maxiPitchStretch
plays a sample with independent control of pitch and speed

## methods

###setSample(maxiSample)

sets the sample play for timestretch to use

###play(pitch, rate, grainLength, overlaps, startPos)

pitch (eg. 0.5 = half pitch)
rate (eg. 0.5 = half speed)
grainLength (a time in seconds)
overlaps (normally 2 is good)
startPos (where to start playing in the sample - in seconds)

###getPosition()

returns position in ???

###getNormalisedPosition()

Useful for ending sample play back

###setPosition(startPos)

useful for resetting a sound

<!--  /////////////////////////////////////////////// -->

# maxiSample

## methods

```javascript
var audio = new maximJs.maxiAudio();

var sample = new maximJs.maxiSample();
var sample2 = new maximJs.maxiSample();
var sampleOut;

// initialise the audio engine
audio.init();

audio.loadSample("sample.wav", sample);
audio.loadSample("sample2.wav", sample2);

audio.play = function() {

    if (sample.isReady() && sample2.isReady()) {

        // trigger sample2 every 1000 milliseconds
        setTimeout(function () {
            sample2.trigger();
        }, 1000);

        // play sample at half speed and play sample2 once
        sampleOut = sample.play(0.5) + sample2.playOnce();
    }

    this.output = sampleOut;
}
```

### play()

plays the sample at normal speed

### play(playRate)

plays the sample at the specified play rate

### playOnce()

play the sample once at normal speed

### playOnce(playRate)

plays the sample once at specified play rate

### trigger()

set the playhead to zero (use in conjunction with playOnce)

### isReady()

returns true if sample is loaded

<!--  /////////////////////////////////////////////// -->

# maxiTimestretch

## methods

### setSample(maxiSample)

sets the sample play for timestretch to use

### play(rate, grainLength, overlaps, startPos)

rate (eg. 0.5 = half speed)
grainLength (a time in seconds)
overlaps (normally 2 is good)
startPos (where to start playing in the sample - in seconds)

### setPosition(startPos)

useful for resetting a sound

### getPosition()

returns position in ???

### getNormalisedPosition()

Useful for ending sample play back

<!--  /////////////////////////////////////////////// -->

# maxiTools
## methods

### getArrayAsVectorDbl(inputArray)

Returns the array as a VectorDbl object. (Needed for maxiMix).

<!--  /////////////////////////////////////////////// -->

# convert
A collection of conversion functions. Currently numbering one !

## methods

###mtof(midi)

pass a midi value and its frequency is returned

<!--  /////////////////////////////////////////////// -->
