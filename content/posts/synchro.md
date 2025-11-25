+++
title = "Synchro devlog 0"
date = "2025-07-30T13:00:23-06:00"
lastmod = "2025-11-25T00:44:25-07:00"
tags = ["music production","lmms","programming"]
aliases = ["/blog/synchro-devlog-0"]
+++

This post chronicles the background and rationale for my amateur development of Synchro, a digital phase modulation synthesizer. I haven't yet finished Synchro; when I do, I'll update this post accordingly.

## Inception

Back in 2013, I was playing around with a demo copy of Native Instruments FM8. I found an interesting family of sounds produced by modulating a sine wave with one of the "Nth Formant" waveforms pitched several octaves down. The problem with FM8 is that each of these formant waveforms are discrete, and I wanted to blend between them. Also FM8 costs money, and I was a literal child with no income. lmao.

I got my hands on a copy of the now defunct Flowstone (identical to FL Studio's [SynthMaker](https://www.image-line.com/fl-studio-learning/fl-studio-online-manual/html/plugins/Synthmaker.htm)) and used it to make a really horrible 32-bit VST2 plugin named "Karnoid". Unfortunately, I can't find the exported VST2, nor do I have the Flowstone project file (and even if I did, Flowstone doesn't seem to exist anymore, so I'm not sure how I would even open it).

{{< figure src="/images/karnoid-free-beta.jpg" caption="Karnoid Free 1.0 Beta" alt="Karnoid Free 1.0 Beta" width="100%" >}}

I pestered the developers in the LMMS mailing list to develop it into a native LMMS plugin, and again in a [Github issue](https://github.com/LMMS/lmms/issues/1436). It's a painful read; I was an insufferable teenager and I clearly expected somebody else to do the hard work for me. Yikes! Quoting my past self, this version of Synchro worked as follows:

> The signal begins at the "voice" oscillator. This is a two-dimensional
wavetable, which morphs between a triangle, circle, and parabolic waveform
through two knobs. This can be pitched up or down 3 octaves with a knob. A
third "chop" knob adds white space at the end of the waveform.
>
> The second oscillator, the "mouth" oscillator, is a one-dimensional
wavetable that uses a single knob to cycle through the "formant" waves (I
will provide a formula to generate this). This can also be pitched up/down
3 octaves. This also has a "chop" knob.
>
> The mouth oscillator then modulates the frequency of the voice oscillator.
The maximum amount can be determined with a knob, then a second knob
determines what percentage of that maximum amount is applied. The second
knob is the one to use for modulation.

{{< figure src="/images/synchro-waveforms.png" caption="Triangle, circle, and parabolic waveforms" alt="Triangle, circle, and parabolic waveforms" width="100%" >}}

For reference, here are some functions that naively (read: will alias) generate these waveforms:

```c
#include <math.h>

// These waveforms are only really defined where 1 > x >= 0. This
// function allows the use of arbitrary input phase values.
float wrap(float x) { return x - floorf(x); }

// This triangle wave has its phase offset by 25% in order to have
// identical peak and x-intercepts to a sine wave with the same period
// (and all the other wave shapes provided here as well).
float triangle_wave(float x) {
  return 2.f * fabsf(2.f * wrap(x - 0.25f) - 1.f) - 1.f;
}

// This was originally intended to be a rather poor approximation of a
// sine wave, but it has some nice saturation-like harmonics.
float parabol_wave(float x) {
  const float p = 4.f * wrap(x) - 2.f;
  return p * (fabsf(p) - 2.f);
}

// This "circle" waveform is inspired by a similar waveform available
// in Native Instruments' FM8.
float circle_wave(float x) {
  const float w = wrap(x);
  const float p = fabsf(4.f * w - 2.f);
  const float s = 2.f * (w - 0.5f < 0.f) - 1.f;
  return s * sqrtf(p * (2.f - p));
}

// This is an easier to understand version of the above function.
// The ternary and the branch misses it may cause makes this
// unsuitable for realtime audio, hence the boolean multiplcation in
// the less readable implementation.
float circle_wave2(float x) {
  const float p = parabol_wave(x);
  return p >= 0 ? sqrtf(p) : -sqrtf(-p);
}

// This "mouth" waveform is inspired by the "Nth Formant" family of
// waveforms available in Native Instruments' FM8. It's much cleaner
// than FM8's versions of these waveforms, though that may not be
// a desirable characteristic.
// This version uses a sine wave as its base, but it also sounds neat
// when using triangle or parabol instead.
// The "sync" parameter increases the base frequency of the sine wave,
// but the overall period of the waveform is maintained by multiplying
// it by an inverted sawtooth wave.
float mouth_wave(float x, float sync) {
  return sinf(6.28318548f * x * sync) * wrap(-x);
}
```

## First Attempt

Five years later, after gaining some very rudimentary experience with C++, I decided I would finally try to code it myself. After a lot of fumbling, I opened a [pull request](https://github.com/LMMS/lmms/pull/5147). Unfortunately, I wasn't satisfied with some of its behavor (notably the awful aliasing and lack of monophonic glide), so it remains unfinished. I accidentally closed that PR due to a git skill issue, but I plan on opening a new one once I am satisfied with the quality of Synchro.

This verison of synchro introduced some additional tools, namely some more flexibility with the "mouth" wave shape and some saturation. It also uses the "mouth" wave shape for both oscillators:

```c
// This "mouth" waveform is inspired by the "Nth Formant" family of
// waveforms available in Native Instruments' FM8. This version uses
// a triangle wave as its base instead of a sine wave.
// The "sync" parameter increases the base frequency of the sine wave,
// but the overall period of the waveform is maintained through hard
// sync (the phase is reset to 0 at the start of each period).
// The "pulse" parameter introduces amplitude decay over the span of
// each waveform period. At 0, there is no decay; at 1, the amplitude
// decays linearly; higher values result in steeper exponential decay.
// The "drive" parameter introduces saturation. This is included within
// this function, since the drive happens before the "pulse" decay in
// order to preserve the effect of the "pulse" decay. Low values of 
// drive result in a base waveform that closer resembles the parabol.
float mouth_wave2(float x, float sync, float pulse, float drive) {
  drive *= 2.f;
  const float w = wrap(x);
  const float decay = powf(1 - w, pulse);
  // This is just tanhf(t * drive) / tanhf(drive), but without the
  // error checking lol. It's not that much faster unfortunately
  const float a = expf(triangle_wave(w * sync) * drive);
  const float b = expf(drive);
  return decay * ((a - 1) * (b + 1)) / ((a + 1) * (b - 1));
}
```

<iframe src="https://www.desmos.com/calculator/zhvkotmcl7?embed" frameborder=0 style="width: 100%; border-radius: var(--zs-rem); margin-block-end: var(--md-rem)"></iframe>

## Synchro's Future

I intend to implement the following features before I feel confident releasing Synchro:

- Effective oversampling to reduce aliasing
- Proper monophonic glide
- ADSR envelopes for both oscillators
- Pitch envelopes for both oscillators
- Legato mode for all envelopes
- A more performant saturation algorithm (hyperbolic tangent is too slow)

Until then, have fun replicating the nonsense above in your synthesizer of choice!
