---
title: 'Sound.Tidal.UI'
metaTitle: 'Sound.Tidal.UI'
metaDescription: 'Sound.Tidal.UI'
---

## _degradeBy

```haskell
degradeBy :: Double -> Pattern a -> Pattern a
```

Similarly to degrade, degradeBy allows you to control the percentage of events that are removed. For example, to remove events 90% of the time:

```haskell
d1 $ slow 2 $ degradeBy 0.9 $ sound "[[[feel:5*8,feel*3] feel:3*8], feel*4]"
   # accelerate "-6"
   # speed "2"
```

## _distrib

todo

## _euclid

```haskell
euclid :: Pattern Int -> Pattern Int -> Pattern a -> Pattern a
```

**euclid** creates a Euclidean rhythmic structure. It produces the same output as the Euclidean pattern string.

For example:

```haskell
d1 $ euclid 3 8 $ sound "cp"
```

is the same as

```haskell
d1 $ sound "cp(3,8)"
```

euclid accepts two parameters that can be patterns:

```haskell
d1 $ euclid "<3 5>" "[8 16]/4" $ s "bd"
```

## _euclid'

todo

## _euclidBool

todo

## _euclidFull

```haskell
euclidFull :: Pattern Int -> Pattern Int -> Pattern a -> Pattern a ->Pattern a
```

**euclidFull** is a convenience function for playing one pattern on the euclidean rhythm and a different pattern on the off-beat.

```haskell
euclidFull 5 8 (s "bd") (s "hh27")
```

is equivalent to our above example.

## _euclidInv

## _euclidOff

todo

## _euclidOffBool

todo

## _iter

```haskell
iter :: Pattern Int -> Pattern a -> Pattern a
```

**iter** divides a pattern into a given number of subdivisions, plays the subdivisions in order, but increments the starting subdivision each cycle. The pattern wraps to the first subdivision after the last subdivision is played.

Example:

```haskell
d1 $ iter 4 $ sound "bd hh sn cp"
```

This will produce the following over four cycles:

```code
bd hh sn cp
hh sn cp bd
sn cp bd hh
cp bd hh sn
```

## _iter'

```haskell
iter' :: Pattern Int -> Pattern a -> Pattern a
```

**iter'** does the same as iter but in the other direction. So this:

```haskell
d1 $ iter' 4 $ sound "bd hh sn cp"
```

Produces this pattern:

```code
bd hh sn cp
cp bd hh sn
sn cp bd hh
hh sn cp bd
```

## _linger

```haskell
linger :: Pattern Time -> Pattern a -> Pattern a
```

**linger** is similar to trunc, in that it truncates a pattern so that only the first fraction of the pattern is played. However unlike trunk, linger repeats that part to fill the remainder of the cycle.

For example this repeats the first quarter, so you only hear a single repeating note:

```haskell
d1 $ linger 0.25 $ n "0 2 [3 4] 2" # sound "arpy"
```

or slightly more interesting, applied only every fourth cycle:

```haskell
d1 $ every 4 (linger 0.25) $ n "0 2 [3 4] 2" # sound "arpy"
```

or to a chopped-up sample:

```haskell
d1 $ every 2 (linger 0.25) $ loopAt 2 $ chop 8 $ sound "breaks125"
```

You can also pattern the first parameter, for example to cycle through three values, one per cycle:

```haskell
d1 $ linger "<0.75 0.25 1>" $ sound "bd sn:2 [mt rs] hc"
d1 $ linger "<0.25 0.5 1>" $ loopAt 2 $ chop 8 $ sound "breaks125"
```

## _off

```haskell
off :: Pattern Time -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**off** is similar to superimpose, in that it applies a function to a pattern, and layers up the results on top of the original pattern. The difference is that off takes an extra pattern being a time (in cycles) to shift the transformed version of the pattern by.

The following plays a pattern on top of itself, but offset by an eighth of a cycle, with a distorting bitcrush effect applied.

```haskell
d1 $ off 0.125 (# crush 2) $ sound "bd [~ sn:2] mt lt*2"
```

The following makes arpeggios by adding offset patterns that are shifted up the scale:

```haskell
d1 $ slow 2 $
  n (off 0.25 (+12) $ off 0.125 (+7) $ slow 2 "c(3,8) a(3,8,2) f(3,8) e(3,8,4)")
  # sound "superpiano"
```

## _ply

```haskell
ply :: Pattern Int -> Pattern a -> Pattern a
```

The **ply** function repeats each event the given number of times. For example

```haskell
ply 3 $ s "bd ~ sn cp"
```

... is equivalent to ...

```haskell
s "[bd bd bd] ~ [sn sn sn] [cp cp cp]"
```

The first parameter may be given as a pattern, so that:

```haskell
ply "2 3" $ s "bd ~ sn cp"
```

... is equivalent to ...

```haskell
s "[bd bd] ~ [sn sn sn] [cp cp cp]"
```

Here is an example of it being used conditionally:

```haskell
d1 $ every 3 (ply 4) $ s "bd ~ sn cp"
```

## _range

```haskell
range :: Num a => Pattern a -> Pattern a -> Pattern a -> Pattern a
```

range will take a pattern which goes from 0 to 1 (such as sine), and scale it to a different range - between the first and second arguments. In the below example, range 1 1.5 shifts the range of sine from 0 - 1 to 1 - 1.5.

```haskell
d1 $ jux (iter 4) $ sound "arpy arpy:2*2"
  |+ speed (slow 4 $ range 1 1.5 sine)
```

The above is equivalent to the following:

```haskell
d1 $ jux (iter 4) $ sound "arpy arpy:2*2"
  |+ speed (slow 4 $ sine * 0.5 + 1)
```

## _rot

```haskell
rot :: Ord a => Pattern Int -> Pattern a -> Pattern a
```

The **rot** function 'rotates' the values in a pattern, while preserving its structure. For example in the following, each value will shift to its neighbour's position one step to the left, so that b takes the place of a, a of c, and c of b:

```haskell
rot 1 "a ~ b c"
```

The result is equivalent of

```code
"b ~ c a"
```

The first parameter is the number of steps, and may be given as a pattern, for example:

```haskell
d1 $ rot "<0 0 1 3>" $ n "0 ~ 1 2 0 2 ~ 3*2" # sound "drum"
```

The above will not rotate the pattern for the first two cycles, will rotate it by one the third cycle, and by three the fourth cycle.

## _segment

```haskell
segment :: Pattern Time -> Pattern a -> Pattern a
```

**segment** 'samples' the pattern at a rate of n events per cycle. Useful for turning a continuous pattern into a discrete one.

In this example, the pattern originates from the shape of a sine wave, a continuous pattern. Without segment the samples will get triggered at an undefined frequency which may be very high.

```haskell
d1 $ n (slow 2 $ segment 16 $ range 0 32 $ sine) # sound "amencutup"
```

## _select

```haskell
select :: Pattern Double -> [Pattern a] -> Pattern a
```

chooses between a list of patterns, using a pattern of floats (from 0-1).

see also: selectF, squeeze, pickF

## _stripe

```haskell
stripe :: Pattern Int -> Pattern a -> Pattern a
```

The **stripe** function repeats a pattern at random speeds. The first parameter gives the number of cycles to operate over, for example stripe 2 will repeat a pattern twice, over two cycles. Each cycle will be played at a random speed, but in such a way that the total duration will be the same.

For example in the following example, the start of every third repetition of the d1 pattern will match with the clap on the d2 pattern.

```haskell
d1 $ stripe 3 $ sound "bd sd ~ [mt ht]"
```

```haskell
d2 $ sound "cp"
```

## _trunc

```haskell
trunc :: Pattern Time -> Pattern a -> Pattern a
```

**trunc** truncates a pattern so that only a fraction of the pattern is played. The following example plays only the first three quarters of the pattern:

```haskell
d1 $ trunc 0.75 $ sound "bd sn*2 cp hh*4 arpy bd*2 cp bd*2"
```

You can also pattern the first parameter, for example to cycle through three values, one per cycle:

```haskell
d1 $ trunc "<0.75 0.25 1>" $ sound "bd sn:2 [mt rs] hc"
```

See also linger.

## _unDegradeBy

```haskell
unDegradeBy :: Double -> Pattern a -> Pattern a
```

**unDegradeBy** is degradeBy but with the percentage describing how many events to keep on average not remove.

## almostAlways

see **sometimes**

## almostNever

see **sometimes**

## always

see **sometimes**

## arpeggiate

```haskell
arpeggiate :: Pattern a -> Pattern a
```

The **arpeggiate** (alias arpg) function spreads chords of note numbers over time.

For example, using the 1.0 chord name notation & older list notation:

```haskell
d1 $ n (arpg "'major7 [0,4,7,11]") # sound "superpiano"
```

## arpg

see **arpeggiate**

## brak

```haskell
brak :: Pattern a -> Pattern a
```

**brak** makes a pattern sound a bit like a breakbeat. It does this by every other cycle, squashing the pattern to fit half a cycle, and offsetting it by a quarter of a cycle.

```haskell
d1 $ brak $ sound "[feel feel:3, hc:3 hc:2 hc:4 ho:1]"
```

## choose

```haskell
choose :: [a] -> Pattern a
```

The **choose** function emits a stream of randomly choosen values from the given list, as a continuous pattern.

```haskell
d1 $ sound "drum ~ drum drum" # n (choose [0,2,3])
```

As with all continuous patterns, you have to be careful to give them structure; in this case choose gives you an infinitely detailed stream of random choices.

## chooseBy

```haskell
chooseBy :: Pattern Double -> [a] -> Pattern a
```

The **chooseBy** function is like choose but instead of selecting elements of the list randomly, it uses the given pattern to select elements.

```haskell
chooseBy "0 0.25 0.5" ["a","b","c","d"]
```

will result in the pattern "a b c"

## chunk

```haskell
chunk :: Int -> (Pattern b -> Pattern b) -> Pattern b -> Pattern b
```

**chunk** divides a pattern into a given number of parts, then cycles through those parts in turn, applying the given function to each part in turn (one part per cycle).

Example:

```haskell
d1 $ chunk 4 (# speed 2) $ sound "bd hh sn cp"
```

The below highlights in bold which part of the above pattern has the (# speed 2) function applied to it over four cycles:

```code
bd hh sn cp
bd hh sn cp
bd hh sn cp
bd hh sn cp
```

Another example:

```haskell
d1 $ chunk 4 (hurry 2) $ sound "bd sn:2 [~ bd] sn:2"
```

## chunk'

**chunk'** does the same as chunk but cycles through the parts in the reverse direction.

## contrast

```haskell
contrast :: (ControlPattern -> ControlPattern) -> (ControlPattern -> ControlPattern) -> ControlPattern -> ControlPattern -> ControlPattern
```

**contrast** is like a if-else-statement over patterns. For contrast f g p you can think of f as the true-branch, g as the false branch, and p as the test.

For contrast, you can use any control pattern as a test of equality: n "<0 1>", speed "0.5", or things like that. This lets you choose specific properties of the pattern you're transforming for testing, like in the following example

```haskell
d1 $ contrast (|+ n 12) (|- n 12) (n "c") $ n (run 4) # s "superpiano"
```

where every note that isn't middle-c will be shifted down an octave but middle-c will be shifted up to c5.

Since the test given to contrast is also a pattern, you can do things like have it alternate between options

```haskell
d1 $ contrast (|+ n 12) (|- n 12) (s "<superpiano superchip>") $ s "superpiano superchip" # n 0
```

If you listen to this you'll hear that which instrument is shifted up and which instrument is shifted down alternates between cycles.

See also: **fix**,**unfix**

## contrastBy

todo

## contrastRange

todo

## cycleChoose

```haskell
cycleChoose :: [a] -> Pattern a
```

similar to choose, but only picks once per cycle

```haskell
d1 $ sound "drum ~ drum drum" # n (cycleChoose [0,2,3])
```

## degrade

```haskell
degrade :: Pattern a -> Pattern a
```

**degrade** randomly removes events from a pattern, 50% of the time. Example usage:

```haskell
d1 $ slow 2 $ degrade $ sound "[[[feel:5*8,feel*3] feel:3*8], feel*4]"
   # accelerate "-6"
   # speed "2"
```

## degradeBy

```haskell
degradeBy :: Double -> Pattern a -> Pattern a
```

Similarly to degrade, **degradeBy** allows you to control the percentage of events that are removed. For example, to remove events 90% of the time:

```haskell
d1 $ slow 2 $ degradeBy 0.9 $ sound "[[[feel:5*8,feel*3] feel:3*8], feel*4]"
   # accelerate "-6"
   # speed "2"
```

## degradeOverBy

todo

## discretise

segment used to be known as discretise. The old name remains as an alias and will still work, but may be removed or repurposed in a future version of tidal.

## distrib

todo

## double

see **stutter**

## echo

see **stutter**

## enclosingArc

todo

## euclid

```haskell
euclid :: Pattern Int -> Pattern Int -> Pattern a -> Pattern a
```

**euclid** creates a Euclidean rhythmic structure. It produces the same output as the Euclidean pattern string.

For example:

```haskell
d1 $ euclid 3 8 $ sound "cp"
```

is the same as

```haskell
d1 $ sound "cp(3,8)"
```

euclid accepts two parameters that can be patterns:

```haskell
d1 $ euclid "<3 5>" "[8 16]/4" $ s "bd"
```

## euclidFull

```haskell
euclidFull :: Pattern Int -> Pattern Int -> Pattern a -> Pattern a ->Pattern a
```

**euclidFull** is a convenience function for playing one pattern on the euclidean rhythm and a different pattern on the off-beat.

```haskell
euclidFull 5 8 (s "bd") (s "hh27")
```

is equivalent to our above example.

## euclidInv

```haskell
euclidInv :: Pattern Int -> Pattern Int -> Pattern a -> Pattern a
```

Inverts the pattern given by euclid. For example,

```haskell
d1 $ stack [euclid 5 8 $ s "bd",
            euclidInv 5 8 $ s "hh27"]
```

to hear that the hi-hat event fires on every one of the eight even beats that the bass drum does not.

## euclidOff

todo

## euclidOffBool

todo

## fadeIn

todo

## fadeInFrom

todo

## fadeOut

todo

## fadeOutFrom

todo

## fastspread

```haskell
fastspread :: (a -> t -> Pattern b) -> [a] -> t -> Pattern b
```

**fastspread** works the same as spread, but the result is squashed into a single cycle. If you gave four values to spread, then the result would seem to speed up by a factor of four. Compare these two:

```haskell
d1 $ spread ($) [gap 4, striate 4] $ sound "ho ho:2 ho:3 hc"
```

```haskell
d1 $ fastspread ($) [gap 4, striate 4] $ sound "ho ho:2 ho:3 hc"
```

## fit

```haskell
fit :: Int -> [a] -> Pattern Int -> Pattern a
```

The **fit** function takes a pattern of integer numbers, which are used to select values from the given list. What makes this a bit strange is that only a given number of values are selected each cycle. For example:

```haskell
d1 $ sound (fit 3 ["bd", "sn", "arpy", "arpy:1", "casio"] "0 [~ 1] 2 1")
```

The above fits samples into the pattern by number, i.e. for the first cycle this will be "bd", "sn" and "arpy", giving the result "bd [~ sn] arpy sn" (note that we start counting at zero, so that 0 picks the first value). For the following cycle, the same is done again, but offset by the number given in the first parameter. In the above case it's 3, which is effectively added to all the numbers, giving "3 [~ 4] 5 4", giving giving the pattern "arpy:1 [~ casio] bd casio" (note that the list wraps round here, so 5 goes back to choose the first element in the list). The next cycle, 6 is added to all the numbers, then 9, and so on.

## fit'

```haskell
fit' :: Time -> Int -> Pattern Int -> Pattern Int -> Pattern a -> Pattern a
```

**fit'** is a generalization of fit, where the list is instead constructed by using another integer pattern to slice up a given pattern. The first argument is the number of cycles of that latter pattern to use when slicing. It’s easier to understand this with a few examples:

```haskell
d1 $ sound (fit' 1 2 "0 1" "1 0" "bd sn")
```

So what does this do? The first 1 just tells it to slice up a single cycle of "bd sn". The 2 tells it to select two values each cycle, just like the first argument to fit. The next pattern "0 1" is the “from” pattern which tells it how to slice, which in this case means "0" maps to "bd", and "1" maps to "sn". The next pattern "1 0" is the “to” pattern, which tells it how to rearrange those slices. So the final result is the pattern "sn bd".

A more useful example might be something like

```haskell
d1 $ fit' 1 4 (run 4) "[0 3*2 2 1 0 3*2 2 [1*8 ~]]/2" $ chop 4 $ (sound "breaks152" # unit "c")
```

which uses chop to break a single sample into individual pieces, which fit' then puts into a list (using the run 4 pattern) and reassembles according to the complicated integer pattern.

## fix

```haskell
fix :: (ControlPattern -> ControlPattern) -> ControlPattern -> ControlPattern -> ControlPattern
```

The **fix** function applies another function to matching events in a pattern of controls. fix is contrast where the false-branching function is set to the identity id.

For example:

```haskell
d1 $ slow 2 $ fix (# crush 3) (n "[1,4]") $ n "0 1 2 3 4 5 6" # sound "arpy"
```

The above only adds the crush control when the n control is set to either 1 or 4.

You can be quite specific, for example fix (hurry 2) (s "drum" # n "1") to apply the function hurry 2 to sample 1 of the drum sampleset, and leave the rest as they are.

## fixRange

todo

## flatpat

```haskell
flatpat :: Pattern [a] -> Pattern a
```

**flatpat** takes a pattern of lists and flattens it into a pattern where all the events in each list happen simultaneously.

For example, the following code uses flatpat in combination with listToPat to create an alternating pattern of chords.

```haskell
d1 $ n (flatpat $ listToPat [[0,4,7],[(-12),(-8),(-5)]]) # s "superpiano" # sustain 2
```

This code is equivalent to

```haskell
d1 $ n ("[0,4,7] [-12,-8,-5]") # s "superpiano" # sustain 2
```

## ghost

```haskell
ghost :: Pattern ControlMap -> Pattern ControlMap
```

**ghost** adds quieter, pitch-shifted, copies of an event after the event, emulating ghost notes that are common in drumming patterns.

```haskell
d1 $ stack [ ghost $ sound "~ sn", sound "bd*2 [~ bd]" ]
```

The example above creates a kick snare pattern with ghost notes applied to the snare hit.

## ghost'

todo

## ghost''

todo

## ifp

```haskell
ifp :: (Int -> Bool) -> (Pattern a -> Pattern a) -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**ifp** decides whether to apply one or another function depending on the result of a test function, which is passed the current cycle as a number. For example:

```haskell
d1 $ ifp ((== 0).(flip mod 2))
  (striate 4)
  (# coarse "24 48") $
  sound "hh hc"
```

This will apply striate 4 for every even cycle, and # coarse "24 48" for every odd one.

Detail: The test function does not rely on anything tidal-specific, it uses plain Haskell functionality for operating on numbers. That is, it calculates the modulo of 2 of the current cycle which is either 0 (for even cycles) or 1. It then compares this value against 0 and returns the result, which is either True or False. This is what the first part of ifp's type signature signifies (Int -> Bool), a function that takes a whole number and returns either True or False.

## index

todo

## inhabit

todo

## inside

```haskell
inside :: Pattern Time -> (Pattern a -> Pattern b) -> Pattern a -> Pattern b
```

**inside** carries out an operation 'inside' a cycle.

For example, while rev "0 1 2 3 4 5 6 7" is the same as "7 6 5 4 3 2 1 0", inside 2 rev "0 1 2 3 4 5 6 7" gives "3 2 1 0 7 6 5 4".

What this function is really doing is 'slowing down' the pattern by a given factor, applying the given function to it, and then 'speeding it up' by the same factor. In other words, this:

```haskell
inside 2 rev "0 1 2 3 4 5 6 7"
```

Is doing this:

```haskell
fast 2 $ rev $ slow 2 "0 1 2 3 4 5 6 7"
```

.. so rather than whole cycles, each half of a cycle is reversed.

## inv

```haskell
inv :: Functor f => f Bool -> f Bool
```

**inv** inverts a collection of Boolean values, such as Pattern Bool, thus turning True into False and visa-versa.

This is particularly useful when using functions such as struct that use a pattern of Booleans to control another pattern.

The following example gives a bass drum and a hi-hat on alternate beats by using inv along with struct

```haskell
let pat = "[t f]*4"

d1 $ struct pat $ s "bd"

d2 $ struct (inv pat) $ s "hh27"
```

## irand

```haskell
irand :: Num a => Int -> Pattern a
```

**irand** is similar to rand, but generates a continuous oscillator of (pseudo-)random integers between 0 to n-1 inclusive. Notably used to pick random samples from a folder.

```haskell
d1 $ sound "amencutup*8" # n (irand 8)
```

## iter

```haskell
iter :: Pattern Int -> Pattern a -> Pattern a
```

**iter** divides a pattern into a given number of subdivisions, plays the subdivisions in order, but increments the starting subdivision each cycle. The pattern wraps to the first subdivision after the last subdivision is played.

Example:

```haskell
d1 $ iter 4 $ sound "bd hh sn cp"
```

This will produce the following over four cycles:

```code
bd hh sn cp
hh sn cp bd
sn cp bd hh
cp bd hh sn
```

## iter'

**iter'** does the same as iter but in the other direction. So this:

```haskell
d1 $ iter' 4 $ sound "bd hh sn cp"
```

Produces this pattern:

```code
bd hh sn cp
cp bd hh sn
sn cp bd hh
hh sn cp bd
```

## jux

```haskell
jux :: (ControlPattern -> ControlPattern) -> ControlPattern -> ControlPattern
```

The **jux** function creates strange stereo effects, by applying a function to a pattern, but only in the right-hand channel. For example, the following reverses the pattern on the righthand side:

```haskell
d1 $ slow 32 $ jux (rev) $ striate' 32 (1/16) $ sound "bev"
```

When passing functions to functions like jux and every, it’s possible to chain multiple transforms together with ., for example this both reverses and halves the playback speed of the pattern in the righthand channel:

```haskell
d1 $ slow 32 $ jux ((# speed "0.5") . rev) $ striate' 32 (1/16) $ sound "bev"
```

## jux'

todo

## jux4

todo

## juxBy

```haskell
juxBy :: Pattern Double -> (ControlPattern -> ControlPattern) -> ControlPattern -> ControlPattern
```

With **jux**, the original and effected versions of the pattern are panned hard left and right (i.e., panned at 0 and 1). This can be a bit much, especially when listening on headphones. The variant juxBy has an additional parameter, which brings the channel closer to the centre. For example:

```haskell
d1 $ juxBy 0.5 (fast 2) $ sound "bd sn:1"
```

In the above, the two versions of the pattern would be panned at 0.25 and 0.75, rather than 0 and 1.

See also: superimpose and off

## juxcut

todo

## juxcut'

todo

## layer

```haskell
layer :: [a -> Pattern b] -> a -> Pattern b
```

The **layer** function allows you to layer up multiple functions on one pattern.

For example the following will play two versions of the pattern at the same time, one reversed and one at twice the speed.

```haskell
d1 $ layer [rev, fast 2] $ sound "arpy [~ arpy:4]"
```

If you want to include the original version of the pattern in the layering, use the id function:

```haskell
d1 $ layer [id, rev, fast 2] $ sound "arpy [~ arpy:4]"
```

See also this function is related to superimpose, in particular layer [id, rev] is the same as superimpose rev.

## lindenmayer

```haskell
lindenmayer :: Num b => Int -> String -> String -> [b]
```

**lindenmayer** takes an integer b, a Lindenmayer system rule set and an initiating string as input in order to generate an L-system tree string of b iterations. It can be used in conjunction with a step function to convert the generated string into a playable pattern.

For example:

```haskell
d1 $ slow 16 $ sound $ step' ["feel:0", "sn:1", "bd:0"]
(take 512 $ lindenmayer 5 "0:1~~~,1:0~~~2~~~~~0~~~2~,2:2~1~,~:~~1~"
"0")
```

... generates an L-system with initiating string "0" and maps it onto a list of samples.

Complex L-system trees with many rules and iterations can sometimes result in unwieldy strings. Using take n to only use the first n elements of the string, along with a slow function, can make the generated values more manageable.

## lindenmayerl

todo

## linger

```haskell
linger :: Pattern Time -> Pattern a -> Pattern a
```

**linger** is similar to trunc, in that it truncates a pattern so that only the first fraction of the pattern is played. However unlike trunk, linger repeats that part to fill the remainder of the cycle.

For example this repeats the first quarter, so you only hear a single repeating note:

```haskell
d1 $ linger 0.25 $ n "0 2 [3 4] 2" # sound "arpy"
```

or slightly more interesting, applied only every fourth cycle:

```haskell
d1 $ every 4 (linger 0.25) $ n "0 2 [3 4] 2" # sound "arpy"
```

or to a chopped-up sample:

```haskell
d1 $ every 2 (linger 0.25) $ loopAt 2 $ chop 8 $ sound "breaks125"
```

You can also pattern the first parameter, for example to cycle through three values, one per cycle:

```haskell
d1 $ linger "<0.75 0.25 1>" $ sound "bd sn:2 [mt rs] hc"
d1 $ linger "<0.25 0.5 1>" $ loopAt 2 $ chop 8 $ sound "breaks125"
```

## loopFirst

```haskell
loopFirst :: Pattern a -> Pattern a
```

**loopFirst** is a function that takes a pattern and loops only the first cycle of the pattern. For example, in the following code will only play the bass drum sample.

```haskell
d1 $ loopFirst $ s "<<bd*4 ht*8> cp*4>"
```

This function combines with sometimes to insert events from the first cycle randomly into subsequent cycles of the pattern:

```haskell
d1 $ sometimes loopFirst $ s "<<bd*4 ht*8> cp*4>"
```

## mask

```haskell
mask :: Pattern Bool -> Pattern a -> Pattern a
```

**mask** takes a boolean (aka binary) pattern and 'masks' another pattern with it. That is, events are only carried over if they match within a 'true' event in the binary pattern.

For example consider this kind of messy rhythm without any rests.

```haskell
d1 $ sound (cat ["sn*8", "[cp*4 bd*4, hc*5]"]) # n (run 8)
```

If we apply a mask to it

```haskell
d1 $ mask "t t t ~ t t ~ t"
  $ s (cat ["sn*8", "[cp*4 bd*4, bass*5]"])
  # n (run 8)
```

Due to the use of cat here, the same mask is first applied to "sn*8" and in the next cycle to "[cp4 bd4, hc*5]".

You could achieve the same effect by adding rests within the cat patterns, but mask allows you to do this more easily. It kind of keeps the rhythmic structure and you can change the used samples independently, e.g.

```haskell
d1 $ mask "1 ~ 1 ~ 1 1 ~ 1"
  $ s (cat ["can*8", "[cp*4 sn*4, jvbass*16]"])
  # n (run 8)
```

See struct for more examples for dealing with binary patterns.

## never

see **sometimes**

## off

```haskell
off :: Pattern Time -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**off** is similar to superimpose, in that it applies a function to a pattern, and layers up the results on top of the original pattern. The difference is that off takes an extra pattern being a time (in cycles) to shift the transformed version of the pattern by.

The following plays a pattern on top of itself, but offset by an eighth of a cycle, with a distorting bitcrush effect applied.

```haskell
d1 $ off 0.125 (# crush 2) $ sound "bd [~ sn:2] mt lt*2"
```

The following makes arpeggios by adding offset patterns that are shifted up the scale:

```haskell
d1 $ slow 2 $
  n (off 0.25 (+12) $ off 0.125 (+7) $ slow 2 "c(3,8) a(3,8,2) f(3,8) e(3,8,4)")
  # sound "superpiano"
```

## offadd

todo

## often

see **sometimes**

## outside

```haskell
outside :: Pattern Time -> (Pattern a -> Pattern b) -> Pattern a -> Pattern b
```

**outside** is the inverse of the inside function. outside applies its function outside the cycle.

Say you have a pattern that takes 4 cycles to repeat and apply the `rev` function.

```haskell
d1 $ rev $ cat [s "bd bd sn",s "sn sn bd", s"lt lt sd", s "sd sd bd"]
```

The above generates:

```haskell
d1 $ rev $ cat [s "sn bd bd",s "bd sn sn", s "sd lt lt", s "bd sd sd"]
```

However if you apply `outside`:

```haskell
d1 $ outside 4 (rev) $ cat [s "bd bd sn",s "sn sn bd", s"lt lt sd", s "sd sd bd"]
```

The result is :

```haskell
d1 $ rev $ cat [s "bd sd sd", s "sd lt lt", s "sn sn bd", s "bd bd sn"]
```

Notice the whole idea has been reversed.

What this function is really doing is 'speeding up' the pattern by a given factor, applying the given function to it, and then 'slowing it down' by the same factor. In other words, this:

```haskell
d1 $ slow 4 $ rev $ fast 4 $ cat [s "bd bd sn",s "sn sn bd", s"lt lt sd", s "sd sd bd"]
```

This compresses the idea into a single cycle before `rev` operates and then slows it back to the original speed.

## palindrome

```haskell
palindrome :: Pattern a -> Pattern a
```

The **palindrome** function applies rev to a pattern every other cycle, so that the pattern alternates between forwards and backwards.

For example this:

```haskell
d1 $ palindrome $ sound "arpy:0 arpy:1 arpy:2 arpy:3"
```

... is the same as this:

```haskell
d1 $ slow 2 $ sound "arpy:0 arpy:1 arpy:2 arpy:3 arpy:3 arpy:2 arpy:1 arpy:0"
```

... and indeed this:

```haskell
d1 $ every 2 rev $ sound "arpy:0 arpy:1 arpy:2 arpy:3"
```

## parseLMRule

todo

## parseLMRule'

todo

## permstep

todo

## pick

todo

## ply

```haskell
ply :: Pattern Int -> Pattern a -> Pattern a
```

The **ply** function repeats each event the given number of times. For example

```haskell
ply 3 $ s "bd ~ sn cp"
```

... is equivalent to ...

```haskell
s "[bd bd bd] ~ [sn sn sn] [cp cp cp]"
```

The first parameter may be given as a pattern, so that:

```haskell
ply "2 3" $ s "bd ~ sn cp"
```

... is equivalent to ...

```haskell
s "[bd bd] ~ [sn sn sn] [cp cp cp]"
```

Here is an example of it being used conditionally:

```haskell
d1 $ every 3 (ply 4) $ s "bd ~ sn cp"
```

## quad

see **stutter**

## quantise

```haskell
quantise :: (Functor f, RealFrac b) => b -> f b -> f b
```

**quantise** is useful for rounding a collection of numbers to some particular base fraction. For example,

```haskell
quantise 5 [0, 1.3 ,2.6,3.2,4.7,5]
```

it will round all the values to the nearest (1/5)=0.2 and thus will output the list [0.0,1.2,2.6,3.2,4.8,5.0].

You can use this function to force a continuous pattern like sine into specific values. In the following example

```haskell
d1 $ s "superchip*8" # n (quantise 1 $ range (-10) (10) $ slow 8 $ cosine)
                     # release (quantise 5 $ slow 8 $ sine + 0.1)
```

all the releases selected be rounded to the nearest 0.1 and the notes selected to the nearest 1

quantise with fractional inputs does the consistent thing: quantise 0.5 rounds values to the nearest 2, quantise 0.25 rounds the nearest 4 &c.

## rand

```haskell
rand :: Fractional a => Pattern a
```

**rand** is an oscillator that generates a pattern of (pseudo-)random, floating point numbers between 0 and 1. For example to randomly pan around the stereo field you can:

```haskell
d1 $ sound "bd*8" # pan rand
```

Or to enjoy a randomised speed from 0.5 to 1.5, you can add 0.5 to it.

```haskell
d1 $ sound "arpy*4" # speed (rand + 0.5)
```

## randArcs

todo

## randStruct

todo

## randcat

```haskell
randcat :: [Pattern a] -> Pattern a
```

**randcat** is similar to cat, but rather than playing the given patterns in order, it picks them at random. For example:

```haskell
d1 $ randcat [sound "bd*2 sn", sound "jvbass*3", sound "drum*2", sound "ht mt"]
```

## range

```haskell
range :: Num a => Pattern a -> Pattern a -> Pattern a -> Pattern a
```

**range** will take a pattern which goes from 0 to 1 (such as sine), and scale it to a different range - between the first and second arguments. In the below example, range 1 1.5 shifts the range of sine from 0 - 1 to 1 - 1.5.

```haskell
d1 $ jux (iter 4) $ sound "arpy arpy:2*2"
  |+ speed (slow 4 $ range 1 1.5 sine)
```

The above is equivalent to the following:

```haskell
d1 $ jux (iter 4) $ sound "arpy arpy:2*2"
  |+ speed (slow 4 $ sine * 0.5 + 1)
```

## rangex

```haskell
rangex :: (Floating b, Functor f) => b -> b -> f b -> f b
```

**rangex** is an exponential version of range described above, good to use for frequencies. For example, range 20 2000 "0.5" will give 1010 - halfway between 20 and 2000. But rangex 20 2000 0.5 will give 200 - halfway between on a logarithmic scale. This usually sounds better if you’re using the numbers as pitch frequencies. Since rangex uses logarithms, don’t try to scale things to zero or less!

## rarely

see **sometimes**

## revArc

todo

## rot

```haskell
rot :: Ord a => Pattern Int -> Pattern a -> Pattern a
```

The **rot** function 'rotates' the values in a pattern, while preserving its structure. For example in the following, each value will shift to its neighbour's position one step to the left, so that b takes the place of a, a of c, and c of b:

```haskell
rot 1 "a ~ b c"
```

The result is equivalent of

```haskell
"b ~ c a"
```

The first parameter is the number of steps, and may be given as a pattern, for example:

```haskell
d1 $ rot "<0 0 1 3>" $ n "0 ~ 1 2 0 2 ~ 3*2" # sound "drum"
```

The above will not rotate the pattern for the first two cycles, will rotate it by one the third cycle, and by three the fourth cycle.

## runWith

see **chunk**

## runWith'

see **chunk'**

## samples

todo

## samples'

todo

## scramble

```haskell
scramble :: Int -> Pattern a -> Pattern a
```

**scramble** takes a number and a pattern as input, divides the pattern into the given number of parts, and returns a new pattern by randomly selecting from the parts. This could also be called "sampling with replacement". For example:

```haskell
d1 $ sound $ scramble 3 "bd sn hh"
```

... will sometimes play "sn bd hh" or "hh sn bd", but can also play "bd sn bd" or "hh hh hh", because it can make any random combination of the three parts.

See also: **shuffle**

## segment

```haskell
segment :: Pattern Time -> Pattern a -> Pattern a
```

**segment** 'samples' the pattern at a rate of n events per cycle. Useful for turning a continuous pattern into a discrete one.

In this example, the pattern originates from the shape of a sine wave, a continuous pattern. Without segment the samples will get triggered at an undefined frequency which may be very high.

```haskell
d1 $ n (slow 2 $ segment 16 $ range 0 32 $ sine) # sound "amencutup"
```

## select

```haskell
select :: Pattern Double -> [Pattern a] -> Pattern a
```

chooses between a list of patterns, using a pattern of floats (from 0-1).

see also: **selectF**, **squeeze**, **pickF**

## seqP

```haskell
seqP :: [(Time, Time, Pattern a)] -> Pattern a
```

**seqP** allows you sequence patterns, with start and end times. The code below contains three separate patterns in a “stack”, but each has different start times (zero cycles, four cycles, and eight cycles, respectively). In the example, all patterns stop after 12 cycles:

```haskell
d1 $ seqP [
  (0, 12, sound "bd bd*2"),
  (4, 12, sound "hh*2 [sn cp] cp future*4"),
  (8, 12, sound (samples "arpy*8" (run 16)))
]
```

If you run the above, you probably won’t hear anything. This is because cycles start ticking up as soon as you start Tidal, and you have probably already gone past cycle 12.

You can reset the cycle clock back to zero by running setcps (-1) followed by setcps 1 (nb: at the time of writing, this doesn't yet work in version 1.0.0 of tidal, but you can instead run resetCycles), or whatever tempo you want to restart at. Alternatively, you can shift time for the seqP pattern back to zero like this:

```haskell
d1 $ qtrigger 1 $ seqP [
  (0, 12, sound "bd bd*2"),
  (4, 12, sound "hh*2 [sn cp] cp future*4"),
  (8, 12, sound (samples "arpy*8" (run 16)))
]
```

## seqPLoop

```haskell
seqPLoop :: [(Time, Time, Pattern a)] -> Pattern a
```

A third option is to use seqPLoop instead, which will keep looping the sequence when it gets to the end:

```haskell
d1 $ qtrigger 1 $ seqPLoop [
  (0, 12, sound "bd bd*2"),
  (4, 12, sound "hh*2 [sn cp] cp future*4"),
  (8, 12, sound (samples "arpy*8" (run 16)))
]
```

For building and testing out longer sequences, it may be helpful to skip cycles with rotL.

## sew

```haskell
sew :: Pattern Bool -> Pattern a -> Pattern a -> Pattern a
```

**sew** Uses a pattern of boolean (true or false) values to switch between two other patterns. For example the following will play the first pattern for the first half of a cycle, and the second pattern for the other half.

```haskell
d1 $ sound (sew "t f" "bd*8" "cp*8")
```

The above combines two patterns of strings, and passes the result to the sound function. It's also possible to sew together two control patterns, for example:

```haskell
d1 $ sew "t <f t> <f [f t] t>" (n "0 .. 15" # s "future") (s "cp:3*16" # speed saw + 1.2)
```

You can also use Euclidean rhythm syntax in the boolean sequence:

```haskell
d1 $ sew "t(11,16)" (n "0 .. 15" # s "future") (s "cp:3*16" # speed sine + 1.5)
```

See also stitch

## stitch

```haskell
stitch :: Pattern Bool -> Pattern a -> Pattern a -> Pattern a
```

**stitch** uses the first (binary) pattern to switch between the following two patterns. The resulting structure comes from the binary pattern, not the source patterns. This differs from sew where the resulting structure comes from the source patterns.

For example, the following uses a euclidean pattern to control CC0:

```haskell
d1 $ ccv (stitch "t(7,16)" 127 0) # ccn 0  # "midi"
```

See also **sew**

## shuffle

```haskell
shuffle :: Int -> Pattern a -> Pattern a
```

**shuffle** takes a number and a pattern as input, divides the pattern into the given number of parts, and returns a new pattern as a random permutation of the parts, picking one of each per cycle. This could also be called "sampling without replacement". For example:

```haskell
d1 $ sound $ shuffle 3 "bd sn hh"
```

... will sometimes play "sn bd hh" or "hh sn bd", but will never play "bd sn bd" or "hh hh hh", because that isn't a permutation of the three parts.

See also: **scramble**

## slowspread

see **spread**

## slowstripe

```haskell
slowstripe :: Pattern Int -> Pattern a -> Pattern a
```

The **slowstripe** function is the same as stripe but the result is also slowed down by n time (where n is the first parameter of the function. This means that the mean average duration of the stripes is exactly one cycle, and every nth stripe starts on a cycle boundary (in indian classical terms, the sam).

usage:

```haskell
d1 $ slowstripe 3 $ sound "bd sd ~ [mt ht]"

d2 $ sound "cp"
```

## someCycles

```haskell
someCycles :: (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**someCycles** is similar to sometimes, but instead of applying the given function to random events, it applies it to random cycles. For example the following will either distort all of the events in a cycle, or none of them:

```haskell
d1 $ someCycles (# crush 2) $ n "0 1 [~ 2] 3" # sound "arpy"
```

## someCyclesBy

As with sometimesBy, if you want to be specific, you can use someCyclesBy and a number. For example

```haskell
someCyclesBy 0.93 (# speed 2)
```

will apply the speed control on average 93 cycles out of a hundred.

## somecycles

```haskell
someCycles :: (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**someCycles** is similar to sometimes, but instead of applying the given function to random events, it applies it to random cycles. For example the following will either distort all of the events in a cycle, or none of them:

```haskell
d1 $ someCycles (# crush 2) $ n "0 1 [~ 2] 3" # sound "arpy"
```

## sometimes

```haskell
sometimes :: (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**sometimes** is function, that applies another function to a pattern, around 50% of the time, at random. It takes two inputs, the function to be applied, and the pattern you are applying it to.

For example to distort half the events in a pattern:

```haskell
d1 $ sometimes (# crush 2) $ n "0 1 [~ 2] 3" # sound "arpy"
```

sometimes has a number of variants, which apply the function with different likelihood.

function	likelihood
always	100%
almostAlways	90%
often	75%
sometimes	50%
rarely	25%
almostNever	10%
never	0%

## sometimesBy

If you want to be specific, you can use sometimesBy and a number, for example

```haskell
sometimesBy 0.93 (# speed 2)
```

to apply the speed control on average 93 times out of a hundred.

## spaceOut

todo

## spread

```haskell
spread :: (a -> t -> Pattern b) -> [a] -> t -> Pattern b
```

The **spread** function allows you to take a pattern transformation which takes a parameter, such as `slow`, and provide several parameters which are switched between. In other words it 'spreads' a function across several values.

Taking a simple high hat loop as an example:

```haskell
d1 $ sound "ho ho:2 ho:3 hc"
```

We can speed it up by different amounts, such as by 2x:

```haskell
d1 $ fast 2 $ sound "ho ho:2 ho:3 hc"
```

Or by 3x:

```haskell
d1 $ fast 3 $ sound "ho ho:2 ho:3 hc"
```

But if we use spread, we can make a pattern which alternates between the two speeds:

```haskell
d1 $ spread fast[2,3] $ sound "ho ho:2 ho:3 hc"
```

Note that many functions now allow pattern input. This is equivalent to the above

```haskell
d1 $ fast "<2 3>" $ sound "ho ho:2 ho:3 hc"
```

Note that if you pass ($) as the function to spread values over, you can put different functions as the list of values. For example:

```haskell
d1 $ spread ($) [density 2, rev, slow 2, striate 3, (# speed "0.8")] $ sound "[bd*2 [~ bd]] [sn future]*2 cp jvbass*4"
```

Above, the pattern will have these transforms applied to it, one at a time, per cycle:

- cycle 1: density 2 - pattern will increase in speed
- cycle 2: rev - pattern will be reversed
- cycle 3: slow 2 - pattern will decrease in speed
- cycle 4: striate 3 - pattern will be granualized
- cycle 5: (# speed "0.8") - pattern samples will be played back more slowly

After (# speed "0.8"), the transforms will repeat and start at density 2 again.

## spread'

todo

## spreadChoose

**spreadChoose** (alias spreadr) works the same as spread, but the values are selected at random, one cycle at a time. For example:

```haskell
d1 $ spreadChoose ($) [gap 4, striate 4] $ sound "ho ho:2 ho:3 hc"
```

## spreadr

todo

## spreadf

```haskell
A convenient shorthand for spread ($)
```

## stackwith

todo

## step

```haskell
step :: String -> String -> Pattern String
```

**step** acts as a kind of simple step-sequencer using strings. For example, step "sn" "x x 12 " is equivalent to the pattern of strings given by "sn ~ sn ~ sn:1 sn:2 ~". step substitutes the given string for each x, for each number it substitutes the string followed by a colon and the number, and for everything else it puts in a rest.

In other words, step generates a pattern of strings in exactly the syntax you'd want for selecting samples and that can be fed directly into the s function.

```haskell
d1 $ s (step "sn" "x x 12 ")
```

## step'

```haskell
step' :: [String] -> String -> Pattern String
```

**step'** is like step but more general, using the numbers in the step-sequencing string as indexes into the list of strings you give it.

```haskell
d1 $ s (step' ["superpiano","supermandolin"] "0 1 000 1") # sustain 4 # n 0
```

is equivalent to

```haskell
d1 $ s "superpiano ~ supermandolin ~ superpiano!3 ~ supermandolin" # sustain 4 # n 0
```

## steps

```haskell
steps :: [(String,String)] -> Pattern String
```

**steps** is like step but it takes a list of pairs like step would and it plays them all simultaneously.

```haskell
d1 $ s (steps [("cp","x  x x  x x  x"),("bd", "xxxx")])
```

## stretch

todo

## stripe

```haskell
stripe :: Pattern Int -> Pattern a -> Pattern a
```

The **stripe** function repeats a pattern at random speeds. The first parameter gives the number of cycles to operate over, for example stripe 2 will repeat a pattern twice, over two cycles. Each cycle will be played at a random speed, but in such a way that the total duration will be the same.

For example in the following example, the start of every third repetition of the d1 pattern will match with the clap on the d2 pattern.

```haskell
d1 $ stripe 3 $ sound "bd sd ~ [mt ht]"

d2 $ sound "cp"
```

## struct

```haskell
struct :: Pattern Bool -> Pattern a -> Pattern a
```

**struct** places a rhythmic 'boolean' structure on the pattern you give it.

For example:

```haskell
d1 $ struct ("t ~ t*2 ~") $ sound "cp"
```

... is the same as ...

```haskell
d1 $ sound "cp ~ cp*2 ~"
```

The structure comes from a boolean pattern, i.e. a binary one containing true or false values. Above we only used true values, denoted by t. It's also possible to include false values with f, which struct will simply treat as silience. For example, this would have the same outcome as the above:

```haskell
d1 $ struct ("t f t*2 f") $ sound "cp"
```

These true/false binary patterns become useful when you conditionally manipulate them, for example 'inverting' the values using every and inv:

```haskell
d1 $ struct (every 3 inv "t f t*2 f") $ sound "cp"
```

In the above, the boolean values will be 'inverted' every third cycle, so that the structure comes from the fs rather than t.

Note that euclidean patterns also create true/false values, for example:

```haskell
d1 $ struct (every 3 inv "t(3,8)") $ sound "cp"
```

In the above, the euclidean pattern creates "t f t f t f f t" which gets inverted to "f t f t f t t f" every third cycle.

Note that if you prefer you can use 1 and 0 instead of t and f.

See also mask.

## stutter

```haskell
stutter :: Integral i => i -> Time -> Pattern a -> Pattern a
```

**stutter** is like stut that doesn't reduce the volume or ply if you controlled the timing. stutter n t repeats each event in the pattern n times, separated by t time (in fractions of a cycle).

The code

```haskell
d1 $ stutter 4 (1/16) $ s "bd cp"
```

should be functionally equivalent to

```haskell
d1 $ stut 4 1 (1/16) $ s "bd cp"
```

Specific conveniences functions that use stutter are:

```code
echo   = stutter (2 :: Int)
triple = stutter (3 :: Int)
quad   = stutter (4 :: Int)
double = echo
```

## substruct

todo

## substruct'

todo

## superimpose

```haskell
superimpose :: (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**superimpose** plays a modified version of a pattern 'on top of' the original pattern, resulting in the modified and original version of the patterns being played at the same time. For example this:

```haskell
d1 $ superimpose (fast 2) $ sound "bd sn [cp ht] hh"
```

...is the same as this:

```haskell
d1 $ stack [sound "bd sn [cp ht] hh",
            fast 2 $ sound "bd sn [cp ht] hh"
           ]
```

See also
Compare this function with jux which works similarly but pans the two versions of the pattern left and right, off which offsets the modified pattern in time, and layer which works like superimpose but allows you to layer up the results of more than one function.

## swing

```haskell
swing :: Pattern Time -> Pattern a -> Pattern a
```

**swing** is an alias for swingBy (1/3)

## swingBy

```haskell
swingBy :: Pattern Time -> Pattern Time -> Pattern a -> Pattern a
```

The function swingBy x n breaks each cycle into n slices, and then delays events in the second half of each slice by the amount x, which is relative to the size of the (half) slice. So if x is 0 it does nothing, 0.5 delays for half the note duration, and 1 will wrap around to doing nothing again. The end result is a shuffle or swing-like rhythm. For example:

```haskell
d1 $ swingBy (1/3) 4 $ sound "hh*8"
```

will delay every other "hh" 1/3 of the way to the next "hh".

## tabby

todo

## timeLoop

todo

## timeToRand

todo

## toScale

```haskell
toScale :: Num a => [a] -> Pattern Int -> Pattern a
```

**toScale** allows you to quickly apply a scale without naming it. For example:

```haskell
d1 $ n (toScale [0,2,3,5,7,8,10] "0 1 2 3 4 5 6 7") # sound "superpiano"
```

## toScale'

todo

## triple

see **stutter**

## trunc

```haskell
trunc :: Pattern Time -> Pattern a -> Pattern a
```

**trunc** truncates a pattern so that only a fraction of the pattern is played. The following example plays only the first three quarters of the pattern:

```haskell
d1 $ trunc 0.75 $ sound "bd sn*2 cp hh*4 arpy bd*2 cp bd*2"
```

You can also pattern the first parameter, for example to cycle through three values, one per cycle:

```haskell
d1 $ trunc "<0.75 0.25 1>" $ sound "bd sn:2 [mt rs] hc"
```

See also **linger**.

## undegradeBy

```haskell
unDegradeBy :: Double -> Pattern a -> Pattern a
```

**unDegradeBy** is degradeBy but with the percentage describing how many events to keep on average not remove.

## unfix

```haskell
unfix :: (ControlPattern -> ControlPattern) -> ControlPattern -> ControlPattern -> ControlPattern
```

**unfix** is fix but only applies when the testing pattern is not a match.

See also: **contrast**

## unfixRange

todo

## unwrap'

todo

## ur

```haskell
ur :: Time -> Pattern String -> [(String, Pattern a)] -> [(String, Pattern a -> Pattern a)] -> Pattern a
```

The ur function is designed for longer form composition, by allowing you to create 'patterns of patterns' in a repeating loop. It takes three parameters -- how long the loop will take, a pattern giving the structure of the composition, a lookup table for named patterns to feed into that structure, and a second lookup table for named transformations/fx.

Lets say you had three patterns (called 'a', 'b' and 'c'), and that you wanted to play them four cycles each, over twelve cycles in total. Here is one way to do it:

```haskell
let pats =
      [
        ("a", stack [n "c4 c5 g4 f4 f5 g4 e5 g4" # s "superpiano" # gain "0.7",
                     n "[c3,g4,c4]" # s "superpiano"# gain "0.7"
                    ]
        ),
        ("b", stack [n "d4 c5 g4 f4 f5 g4 e5 g4" # s "superpiano" # gain "0.7",
                     n "[d3,a4,d4]" # s "superpiano"# gain "0.7"
                    ]
        ),
        ("c", stack [n "f4 c5 g4 f4 f5 g4 e5 g4" # s "superpiano" # gain "0.7",
                     n "[f4,c5,f4]" # s "superpiano"# gain "0.7"
                    ]
        )
      ]
in
d1 $ ur 12 "a b c" pats []
```

In ur 12 "a b c" pats [], the 12 is the duration of the loop (in cycles), the "a b c" is the structure of named patterns, and pats is the lookup table, defined above. So the "a b c" pattern happens over the 12 cycles, with the a, b and c standing in for each of the three patterns given in the lookup table. Because there are three events in this pattern, and it happens over 12 cycles. then each event is four cycles long.

In the above, the fourth parameter is given as an empty list, but that is where you can put another lookup table, of functions rather than patterns this time. Here's an example:

```haskell
let pats =
      [
        ("a", stack [n "c4 c5 g4 f4 f5 g4 e5 g4" # s "superpiano" # gain "0.7",
                     n "[c3,g4,c4]" # s "superpiano"# gain "0.7"
                    ]
        ),
        ("b", stack [n "d4 c5 g4 f4 f5 g4 e5 g4" # s "superpiano" # gain "0.7",
                     n "[d3,a4,d4]" # s "superpiano"# gain "0.7"
                    ]
        ),
        ("c", stack [n "f4 c5 g4 f4 f5 g4 e5 g4" # s "superpiano" # gain "0.7",
                     n "[f4,c5,f4]" # s "superpiano"# gain "0.7"
                    ]
        )
      ]
    fx = [("reverb", (# (room 0.8 # sz 0.99 # orbit 1))),
          ("faster", fast 2)
         ]
in
d1 $ ur 12 "a b:reverb c:faster" pats fx
```

In the above, b has the function applied that's named as reverb, while c is made to go faster.

It's also possible to schedule multiple patterns at once, like in the following:

```haskell
let pats = [("drums", s "drum cp*2"),
            ("melody", s "arpy:2 arpy:3 arpy:5"),
            ("craziness", s "cp:4*8" # speed ( sine + 0.5))
           ]
    fx = [("higher", (# speed 2))]
in
d1 $ ur 8 "[drums, melody] [drums,craziness,melody] melody:higher" pats fx
```

## wchoose

see **choose**

## wchooseBy

see **chooseBy**

## wedge

```haskell
wedge :: Time -> Pattern a -> Pattern a -> Pattern a
```

**wedge** combines two patterns by squashing them into a single cycle. It takes a ratio as the first argument. The ratio determines what percentage of the pattern cycle is taken up by the first pattern. The second pattern fills in the remainder of the pattern cycle. For example:

```haskell
d1 $ wedge (1/4) (sound "bd*2 arpy*3 cp sn*2") (sound "odx [feel future]*2 hh hh")
```

## whenmod

```haskell
whenmod :: Int -> Int -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**whenmod** has a similar form and behavior to every, but requires an additional number. It applies the function to the pattern, when the remainder of the current loop number divided by the first parameter, is greater or equal than the second parameter.

For example the following makes every other block of four loops twice as fast:

```haskell
d1 $ whenmod 8 4 (fast 2) (sound "bd sn kurt")
```

## within

```haskell
within :: Arc -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

Use **within** to apply a function to only a part of a pattern. within takes two arguments: a start time and an end time, specified as floats between 0 and 1, which are applied to the relevant pattern. Note that the second argument must be greater than the first for the function to have any effect.

For example, to apply fast 2 to only the first half of a pattern:

```haskell
d1 $ within (0, 0.5) (fast 2) $ sound "bd*2 sn lt mt hh hh hh hh"
```

Or, to apply (# speed "0.5") to only the last quarter of a pattern:

```haskell
d1 $ within (0.75, 1) (# speed "0.5") $ sound "bd*2 sn lt mt hh hh hh hh"
```

## within'

todo
