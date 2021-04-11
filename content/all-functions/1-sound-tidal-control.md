---
title: 'Sound.Tidal.Control'
metaTitle: 'Sound.Tidal.Control'
metaDescription: 'Sound.Tidal.Control'
## _cF
---

## _cF

```haskell
cF :: Double f => String s => Pattern Double f
```

**cF** uses floating point values from state, such as that from MIDI or OSC controller input.

The first parameter is the default value, for when tidal hasn't received that control. The second parameter is the CC Number of the controller.

```haskell
d1 $ sound "bd*16" # djf (cF 0.5 "42")
```

In recent versions of Tidal, the mininotation allows to write the same statement as:

```haskell
d1 $ sound "bd*16" # djf ("^42")
```

## _cP

todo

## _cS

todo

## _cX

todo

## _chop

```haskell
chop :: Pattern Int -> ControlPattern -> ControlPattern
```

**chop** cuts each sample into the given number of parts, allowing you to explore a technique known as 'granular synthesis'. It turns a pattern of samples into a pattern of parts of samples. For example:

```haskell
d1 $ chop 16 $ sound "arpy ~ feel*2 newnotes"
```

In the above, each sample is chopped into 16 bits, resulting in 64 (16*4) events. You can pattern that first parameter:

```haskell
d1 $ chop "<16 128 32>" $ sound "arpy ~ feel*2 newnotes"
```

You end up with a pattern of the chopped up bits of samples. You'll already be able to hear this more clearly if you for example reverse the pattern, as you'll reverse the order of the sample parts:

```haskell
d1 $ slow 2 $ rev $ chop 16 $ sound "breaks125"
```

Lets try that reverse in just one speaker:

d1 $ slow 2 $ jux rev $ chop 16 $ sound "breaks125"
Different values of chop can yield very different results, depending on the samples used:

```haskell
d1 $ chop 16 $ sound (samples "arpy*8" (run 16))
d1 $ chop 32 $ sound (samples "arpy*8" (run 16))
d1 $ chop 256 $ sound "bd*4 [sn cp] [hh future]*2 [cp feel]"
```

You can also use chop or striate with very long samples, to cut it into short chunks and pattern those chunks. The following cuts a sample into 32 parts, and plays it over 8 cycles:

```haskell
d1 $ loopAt 8 $ chop 32 $ sound "bev"
```

The loopAt takes care of changing the speed of sample playback so that the sample fits in the given number of cycles perfectly. As a result, in the above the granules line up perfectly, so you can’t really hear that the sample has been cut into bits. Again, this becomes more apparent when you do further manipulations of the pattern, for example rev to reverse the order of the cut up bits:

```haskell
d1 $ loopAt 8 $ rev $ chop 32 $ sound "bev"
```

## _gap

```haskell
gap :: Pattern Int -> ControlPattern -> ControlPattern
```

**gap** is similar to chop in that it granulates every sample in place as it is played, but every other grain is silent. Use an integer value to specify how many granules each sample is chopped into:

```haskell
d1 $ gap 8 $ sound "jvbass"
```

```haskell
d1 $ gap 16 $ sound "[jvbass drum:4]"
```

You can also provide a pattern here:

```haskell
d1 $ gap "<32 16 8 4>" $ sound "rave"
```

See also: striate, chop

## _slice

```haskell
slice :: Pattern Int -> Pattern Int -> ControlPattern -> ControlPattern
```

**slice** is similar to chop and striate, in that it's used to slice samples up into bits. The difference is that it allows you to rearrange those bits as a pattern.

```haskell
d1 $ slice 8 "7 6 5 4 3 2 1 0" $ sound "breaks165"
  # legato 1
```

The above slices the sample into eight bits, and then plays them backwards, equivalent of applying rev $ chop 8. Here's a more complex example:

```haskell
d1 $ slice 8 "[<0*8 0*2> 3*4 2 4] [4 .. 7]" $ sound "breaks165"
  # legato 1
```

See also bite, which is similar but slices up patterns, rather than samples.

Note that the order of the first two parameters changed since tidal version 1.0.0

An extended use of slice

## _spin

```haskell
spin :: Pattern Int -> ControlPattern -> ControlPattern
```

**spin** will play the given number of copies of the given control pattern at once. For n copies, each successive copy will be offset in time by an additional 1/n of a cycle, and also panned in space by an additional n1/n. This function works particularly well on multichannel systems.

```haskell
d1 $ slow 3 $ spin 4 $ sound "drum*3 tabla:4 [arpy:2 ~ arpy] [can:2 can:3]"
```

## _striate

```haskell
striate :: Pattern Int -> ControlPattern -> ControlPattern
```

**striate** is a kind of granulator, cutting samples into bits in a similar to chop, but the resulting bits are organised differently. For example:

```haskell
d1 $ slow 4 $ striate 3 $ sound "numbers:0 numbers:1 numbers:2 numbers:3"
```

This plays the loop the given number of times, but triggering progressive portions of each sample. So in this case it plays the loop three times, the first time playing the first third of each sample, then the second time playing the second third of each sample, and then finally the last third of each sample..

Compare this with chop:

```haskell
d1 $ slow 4 $ chop 3 $ sound "numbers:0 numbers:1 numbers:2 numbers:3"
```

You can hear that the striate version 'interlaces' the cut up bits of samples together, whereas the chop version plays the bits from each chopped up sample in turn. It might be worth listening to the samples without granulation, in case that helps understand what’s happening in the above:

```haskell
d1 $ slow 4 $ sound "numbers:0 numbers:1 numbers:2 numbers:3"
```

## _striateBy

```haskell
striateBy :: Pattern Int -> Pattern Double -> ControlPattern -> ControlPattern
```

**striateBy** (formerly called striate') is a variant of striate, with an extra parameter, which specifies the length of each part. striate still scans across the sample over a single cycle, but if each bit is longer, it creates a sort of stuttering effect. For example the following will cut the bev sample into 32 parts, but each will be 1/16th of a sample long:

```haskell
d1 $ slow 32 $ striateBy 32 (1/16) $ sound "bev"
```

Note that striate uses the begin and end parameters internally. This means that if you’re using striate or striateBy you probably shouldn’t also specify begin or end.

See also: chop

## _stut

```haskell
stut :: Pattern Integer -> Pattern Double -> Pattern Rational -> ControlPattern -> ControlPattern
```

**stut** applies a type of delay to a pattern. It has three parameters, which could be called depth, feedback and time. Depth is an integer and the others floating point. This adds a bit of echo:

```haskell
d1 $ stut 4 0.5 0.1 $ sound "bd sn"
```

The above results in 4 repeats (the original plus 3 echoes), each echo 50% (that's the 0.5) quieter than the last, separated by 1/10th (that's the 0.1) of a cycle.

It is possible to reverse the echo:

```haskell
d1 $ stut 4 0.5 (-0.1) $ sound "bd sn"
```

## _stutWith

```haskell
stutWith :: Pattern Int -> Pattern Time -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**stutWith** (formerly known as stut') is similar to stut described above, but instead of just decreasing volume to produce echoes, stutWith applies a function each step and overlays the result delayed by the given time.

```haskell
d1 $ stutWith 3 (1/3) (# vowel "{a e i o u}%2") $ sound "bd sn"
```

In this case there are two overlays delayed by 1/3 of a cycle, where each has the vowel filter applied.

```haskell
d1 $ stutWith 4 (1/6) (|* speed "1.5") $ sound "arpy arpy:2"
```

In the above, three versions are put on top, with each step getting higher in pitch as |* speed "1.5" is successively applied.

## cF

see **_cF**

## cF0

todo

## cF_

see **_cF**

## cI

todo

## cP

todo

## cP_

todo

## cR

todo

## cR0

todo

## cR_

todo

## cS

todo

## cT

todo

## cT0

todo

## cT_

todo

## chop

see **_chop**

## chopArc

todo

## gap

see **_gap**

## hurry

```haskell
hurry :: Pattern Time -> Pattern a -> Pattern a
```

**hurry** is similiar to fast, in that it speeds up a pattern, but it also increases the speed control by the same factor, so if you're triggering samples, the sound gets higher in pitch. For example:

```haskell
d1 $ every 2 (hurry 2) $ sound "bd sn:2 ~ cp"
```

## in0

todo

## in1

todo

## in127

todo

## interlace

```haskell
interlace :: ControlPattern -> ControlPattern -> ControlPattern
```

**interlace** shifts between two patterns, by using SuperDirt's shape distortion control pattern. For example:

```haskell
d1 $ interlace (sound  "bd sn kurt") (every 3 rev $ sound "bd sn:2")
```

Over 16 cycles, the first pattern will slowly rise and fall in waveshaping distortion (and as a result, in volume), and the second pattern will do the opposite, falling and then rising. The resulting effect sounds like the patterns are taking turns coming 'to the front' of the audio scene.

The above is the equivalent of the following:

```haskell
d1 $ sound "bd sn kurt" # shape (slow 16 $ sine * 0.9)
d2 $ every 3 rev $ sound "bd sn:2" # shape (8 <~ (slow 16 $ sine * 0.9))
```

I.e. the distortion will follow a sinewave from 0 to 0.9, over 16 cycles. The second pattern will do the same, but offset by 8 cycles.

## loopAt

```haskell
loopAt :: Pattern Time -> ControlPattern -> ControlPattern
```

**loopAt** makes sample fit the given number of cycles. Internally, it works by setting the unit control to "c", changing the playback speed of the sample with the speed parameter, and setting the density of the pattern to match.

```haskell
d1 $ loopAt 4 $ sound "breaks125"
```

It’s a good idea to use this in conjuction with chop, so the break is chopped into pieces and you don’t have to wait for the whole sample to start/stop.

```haskell
d1 $ loopAt 4 $ chop 32 $ sound "breaks125"
```

Like all tidal functions, you can mess about with this considerably. The below example shows how you can supply a pattern of cycle counts to loopAt:

```haskell
d1 $ juxBy 0.6 (|* speed "2") $ loopAt "<4 6 2 3>" $ chop 12 $ sound "fm:14"
```

See also: chop

## mergePlayRange

todo

## randslice

```haskell
randslice :: Pattern Int -> ControlPattern -> ControlPattern
```

**randslice** chops the sample into the given number of pieces and then plays back a random one each cycle:

```haskell
d1 $ randslice 32 $ sound "bev"
```

Use fast to get more than one per cycle;

```haskell
d1 $ fast 4 $ randslice 32 $ sound "bev"
```

See also slice

## slice

see **_slice**

## smash

```haskell
smash :: Pattern Int -> [Pattern Time] -> ControlPattern -> ControlPattern
```

**smash** is a combination of spread and striate - it cuts the samples into the given number of bits, and then cuts between playing the loop at different speeds according to the values in the list.

So this:

```haskell
d1 $ smash 3 [2,3,4] $ sound "ho ho:2 ho:3 hc"
```

Is a bit like this:

```haskell
d1 $ slow "<2 3 4>" $ striate 3 $ sound "ho ho:2 ho:3 hc"
```

## smash'

```haskell
smash' :: Int -> [Pattern Time] -> ControlPattern -> ControlPattern
```

**smash'** is smash but based on chop instead of striate.

Compare

```haskell
d1 $ smash 6 [2,3,4] $ sound "ho ho:2 ho:3 hc"
```

to

```haskell
d1 $ smash' 6 [2,3,4] $ sound "ho ho:2 ho:3 hc"
```

or

```haskell
d1 $ smash 12 [2,3,4] $ s "bev*4"
```

vs

```haskell
d1 $ smash' 12 [2,3,4] $ s "bev*4"
```

for a dramatic difference

## spin

see **_spin**

## striate

see **_striate**

## striate'

see **_striate**

## striateBy

see **_striateBy**

## stut

see **_stut**

## stut'

see **_stut**

## stutWith

see **_stutWith**

## weave

```haskell
weave :: Time -> ControlPattern -> [ControlPattern] -> ControlPattern
```

**weave** applies one control pattern to a list of other control patterns, with a successive time offset. For example:

```haskell
d1 $ weave 16 (pan sine)
  [sound "bd sn cp",
   sound "casio casio:1",
   sound "[jvbass*2 jvbass:2]/2",
   sound "hc*4"
  ]
```

In the above, the pan sine control pattern is slowed down by the given number of cycles, in particular 16, and applied to all of the given sound patterns. What makes this interesting is that the pan control pattern is successively offset for each of the given sound patterns; because the pan is closed down by 16 cycles, and there are four patterns, they are 'spread out', i.e. with a gap of four cycles. For this reason, the four patterns seem to chase after each other around the stereo field. Try listening on headphones to hear this more clearly.

You can even have it the other way round, and have the effect parameters chasing after each other around a sound parameter, like this:

```haskell
d1 $ weave 16 (sound "arpy" >| n (run 8))
  [vowel "a e i",
   vowel "i [i o] o u",
   vowel "[e o]/3 [i o u]/2",
   speed "1 2 3"
  ]
```

## weave'

see **weaveWith**

## weaveWith

```haskell
weaveWith :: Time -> Pattern a -> [Pattern a -> Pattern a] -> Pattern a
```

**weaveWith** (formerly known as weave') is similar to the above, but weaves with a list of functions, rather than a list of controls. For example:

```haskell
d1 $ weaveWith 3 (sound "bd [sn drum:2*2] bd*2 [sn drum:1]")
  [fast 2,
   (# speed "0.5"),
   chop 16
  ]
```
