---
title: 'Sound.Tidal.Core'
metaTitle: 'Sound Tidal Core'
metaDescription: 'Sound Tidal Core'
---

## _every

```haskell
every :: Pattern Int -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**every** is function, that allows you to apply another function conditionally. It takes three inputs, how often the function should be applied (e.g. 3 to apply it every 3 cycles), the function to be applied, and the pattern you are applying it to.

For example to reverse a pattern every three cycles (and for the other two play it normally):

```haskell
d1 $ every 3 rev $ n "0 1 [~ 2] 3" # sound "arpy"
```

Note that if the function you're applying requires additional parameters itself (such as fast 2 to make a pattern twice as fast), then you'll need to wrap it in parenthesis, like so:

```haskell
d1 $ every 3 (fast 2) $ n "0 1 [~ 2] 3" # sound "arpy"
```

Otherwise, the every function will think it is being passed too many parameters.

See also every'.

## _every'

```haskell
every' :: Int -> Int -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**every'** is a generalisation of every, taking one additional argument. The additional argument allows you to offset the function you are applying.

For example, every' 3 0 (fast 2) will speed up the cycle on cycles 0,3,6,… whereas every' 3 1 (fast 2) will transform the pattern on cycles 1,4,7,…

With this in mind, setting the second argument of every' to 0 gives the equivalent every function. For example, every 3 is equivalent to every' 3 0.

## _fast

```haskell
# fast 3 (vowel "a e o")
```

The first parameter can be patterned, for example to play the pattern at twice the speed for the first half of each cycle and then four times the speed for the second half:

```haskell
d1 $ fast "2 4" $ sound "bd sn kurt cp"
```

You can also use this function by its older alias, density.

See also: slow.

## _run

```haskell
run :: (Num a, Enum a) => Pattern a -> Pattern a
```

The **run** function generates a pattern representing a cycle of numbers from 0 to n-1 inclusive. Notably used to ‘run’ through a folder of samples in order:

```haskell
d1 $ n (run 8) # sound "amencutup"
```

The first parameter to run can be given as a pattern:

```haskell
d1 $ n (run "<4 8 4 6>") # sound "amencutup"
```

## _scan

```haskell
scan :: (Num a, Enum a) => Pattern a -> Pattern a
```

**scan** is similar to run, but starts at 1 for the first cycle, adding an additional number each cycle until it reaches n

```haskell
d1 $ n (scan 8) # sound "amencutup"
```

## _slow

```haskell
slow:: Pattern Time -> Pattern a -> Pattern a
```

**slow** slows down a pattern. For example, the following will play the sound pattern "bd sn kurt" twice as slow (i.e. so it repeats once every two cycles), and the vowel pattern three times as slow:

```haskell
d1 $ sound (slow 2 "bd sn kurt")
```

```haskell
# slow 3 (vowel "a e o")
```

See also: fast.

## append

```haskell
append :: Pattern a -> Pattern a -> Pattern a
```

**append** combines two patterns into a new pattern, where cycles alternate between the first and second pattern.

```haskell
d1 $ append (sound "bd*2 sn") (sound "arpy jvbass*2")
```

It has the alias **slowAppend**, in sympathy with fastAppend, described below.

## cat

```haskell
cat :: [Pattern a] -> Pattern a
```

**cat**, (also known as slowcat, to match with fastcat defined below) concatenates a list of patterns into a new pattern; each pattern in the list will maintain its original duration. For example:

```haskell
d1 $ cat [sound "bd*2 sn", sound "arpy jvbass*2"
```

```haskell
d1 $ cat [sound "bd*2 sn", sound "arpy jvbass*2", sound "drum*2"]
```

```haskell
d1 $ cat [sound "bd*2 sn", sound "jvbass*3", sound "drum*2", sound "ht mt"]
```

## fastcat

```haskell
fastcat :: [Pattern a] -> Pattern a
```

**fastcat** works like cat above, but squashes all the patterns to fit a single cycle. Examples:

```haskell
d1 $ fastcat [sound "bd*2 sn", sound "arpy jvbass*2"]
```

```haskell
d1 $ fastcat [sound "bd*2 sn", sound "arpy jvbass*2", sound "drum*2"]
```

```haskell
d1 $ fastcat [sound "bd*2 sn", sound "jvbass*3", sound "drum*2", sound "ht mt"]
```

## compress

```haskell
compress :: (Time, Time) -> Pattern a -> Pattern a
```

**compress** takes a pattern and squeezes it within the specified time span (i.e. the 'arc'). The new resulting pattern is a sped up version of the original.

```haskell
d1 $ compress (1/4, 3/4) $ s "[bd sn]!"
```

In the above example, the pattern will play in an arc spanning from 25% to 75% of the duration of a cycle. It is equivalent to:

```haskell
d1 $ s "~ [bd sn]! ~"
```

Another example, where all events are different:

```haskell
d1 $ compress (1/4, 3/4) $ n (run 4) # s "arpy"
```

It differs from zoom in that it preserves the original pattern but it speeds up its events so to match with the new time period.

## compressTo

## cosine

```haskell
cosine :: Fractional a => Pattern a
```

A cosine wave, i.e. a sine shifted in time by a quarter of a cycle.

```haskell
d1 $ sound "bd*8" # pan cosine # speed (sine + 0.5)
```

## density

```haskell
fast :: Pattern Time -> Pattern a -> Pattern a
```

**fast** speeds up a pattern. For example, the following will play the sound pattern "bd sn kurt" twice as fast (i.e. so it repeats twice per cycle), and the vowel pattern three times as fast:

```haskell
d1 $ sound (fast 2 "bd sn kurt")
   # fast 3 (vowel "a e o")
```

The first parameter can be patterned, for example to play the pattern at twice the speed for the first half of each cycle and then four times the speed for the second half:

```haskell
d1 $ fast "2 4" $ sound "bd sn kurt cp"
```

You can also use this function by its older alias, density.

See also: slow.

## densityGap

```haskell
fastGap :: Pattern Time -> Pattern a -> Pattern a
```

**fastGap** speeds up a pattern like fast, but rather than it playing multiple times as fast would it instead leaves a gap in the remaining space of the cycle. For example, the following will play the sound pattern "bd sn" only once but compressed into the first half of the cycle, i.e. twice as fast.

```haskell
d1 $ sound (fastGap 2 "bd sn")
See also: fast.
```

## envEq

## envEqR

## envL

## envLR

## every

```haskell
every :: Pattern Int -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**every** is function, that allows you to apply another function conditionally. It takes three inputs, how often the function should be applied (e.g. 3 to apply it every 3 cycles), the function to be applied, and the pattern you are applying it to.

For example to reverse a pattern every three cycles (and for the other two play it normally):

```haskell
d1 $ every 3 rev $ n "0 1 [~ 2] 3" # sound "arpy"
```

Note that if the function you're applying requires additional parameters itself (such as fast 2 to make a pattern twice as fast), then you'll need to wrap it in parenthesis, like so:

```haskell
d1 $ every 3 (fast 2) $ n "0 1 [~ 2] 3" # sound "arpy"
```

Otherwise, the every function will think it is being passed too many parameters.

See also every'.

## every'

```haskell
every' :: Int -> Int -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**every'** is a generalisation of every, taking one additional argument. The additional argument allows you to offset the function you are applying.

For example, every' 3 0 (fast 2) will speed up the cycle on cycles 0,3,6,… whereas every' 3 1 (fast 2) will transform the pattern on cycles 1,4,7,…

With this in mind, setting the second argument of every' to 0 gives the equivalent every function. For example, every 3 is equivalent to every' 3 0.

## fast

```haskell
fast :: Pattern Time -> Pattern a -> Pattern a
```

**fast** speeds up a pattern. For example, the following will play the sound pattern "bd sn kurt" twice as fast (i.e. so it repeats twice per cycle), and the vowel pattern three times as fast:

```haskell
d1 $ sound (fast 2 "bd sn kurt")
   # fast 3 (vowel "a e o")
```

The first parameter can be patterned, for example to play the pattern at twice the speed for the first half of each cycle and then four times the speed for the second half:

```haskell
d1 $ fast "2 4" $ sound "bd sn kurt cp"
```

You can also use this function by its older alias, density.

See also: slow.

## fastAppend

```haskell
fastAppend :: Pattern a -> Pattern a -> Pattern a
```

**fastAppend** works like append described above, but each pair of cycles from the two patterns are squashed to fit a single cycle.

```haskell
d1 $ fastAppend (sound "bd*2 sn") (sound "arpy jvbass*2")
```

See also: cat, fastcat

## fastCat

```haskell
fastcat :: [Pattern a] -> Pattern a
```

**fastcat** works like cat above, but squashes all the patterns to fit a single cycle. Examples:

```haskell
d1 $ fastcat [sound "bd*2 sn", sound "arpy jvbass*2"]

d1 $ fastcat [sound "bd*2 sn", sound "arpy jvbass*2", sound "drum*2"]

d1 $ fastcat [sound "bd*2 sn", sound "jvbass*3", sound "drum*2", sound "ht mt"]
```

## fastFromList

```haskell
listToPat :: [a] -> Pattern a
```

**listToPat** takes a list of things and turns them into a pattern where each item in the list becomes an event all happening in the same cycle, looping upon subsequent cycles. Can also be called as fastFromList

```haskell
d1 $ n (listToPat [0, 1, 2]) # s "superpiano"
```

is equivalent to

```haskell
d1 $ n "[0 1 2]" # s "superpiano"
```

See also: fromList

## fastGap

```haskell
fastGap :: Pattern Time -> Pattern a -> Pattern a
```

**fastGap** speeds up a pattern like fast, but rather than it playing multiple times as fast would it instead leaves a gap in the remaining space of the cycle. For example, the following will play the sound pattern "bd sn" only once but compressed into the first half of the cycle, i.e. twice as fast.

```haskell
d1 $ sound (fastGap 2 "bd sn")
```

See also: fast.

## fastRepeatCycles

## fastSqueeze

```haskell
fastSqueeze :: Pattern Time -> Pattern a -> Pattern a
```

**fastSqueeze** speeds up a pattern by a time pattern given as input, squeezing the resulting pattern inside one cycle and playing the original pattern at every repetition.

To better understand how it works let's compare it with fast:

```haskell
d1 $ fast "1 2" $ s "bd sn"
```

-- output
(0>½)|s: "bd"
(½>¾)|s: "bd"
(¾>1)|s: "sn"
This will give bd played in the first half cycle and bd sn in the second half.

On the other hand, using fastSqueeze;

```haskell
fastSqueeze "1 2" $ s "bd sn"
```

--output
(0>¼)|s: "bd"
(¼>½)|s: "sn"
(½>⅝)|s: "bd"
(⅝>¾)|s: "sn"
(¾>⅞)|s: "bd"
(⅞>1)|s: "sn"

the original pattern will play in the first half and two repetitions of the original pattern will play in the second half. That is, every repetition contains the whole pattern.

If the time pattern has a single value, it becomes equivalent to fast:

```haskell
d1 $ fastSqueeze 2 $ s "bd sn"
```

-- is equal to

```haskell
d1 $ fast 2 $ s "bd sn"
```

-- and equivalent to

```haskell
d1 $ s "[bd sn]*2"
```

## foldEvery

```haskell
foldEvery :: [Int] -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

**foldEvery** transforms a pattern with a function, once per any of the given number of cycles. If a particular cycle is the start of more than one of the given cycle periods, then it it applied more than once. It is similar to chaining multiple every functions together.

Example:

```haskell
d1 $ foldEvery [3, 4, 5] (fast 2) $ sound "bd sn kurt"
```

this is equal to:

```haskell
d1 $ every 3 (fast 2) $ every 4 (fast 2) $ every 5 (fast 2) $ sound "bd sn kurt"
```

## fromList

```haskell
fromList :: [a] -> Pattern a
```

**fromList** takes a list of things and turns them into a pattern where each item in the list has a duration of one cycle, looping back around at the end of the list.

```haskell
d1 $ n (fromList [0, 1, 2]) # s "superpiano"
```

is equivalent to

```haskell
d1 $ n "<0 1 2>" # s "superpiano"
```

See also: listToPat

## fromMaybes

```haskell
fromMaybes :: [Maybe a] -> Pattern a
```

**fromMaybes** is much like listToPat but when it encounters a Nothing it puts a gap in the pattern and when it encounters Just x puts x in the pattern.

```haskell
d1 $ n (fromMaybes [Just 0, Nothing, Just 2]) # s "superpiano"
```

is equivalent to

```haskell
d1 $ n "0 ~ 2" # s "superpiano"
```

See also: listToPat,fromList

## isaw

```haskell
saw :: Fractional a => Pattern a
```

An inverted sawtooth, starting at 1, then linearly falling to 0 over one cycle, then jumping back to 1.

```haskell
d1 $ sound "bd*8" # pan (slow 2 isaw)
```

## listToPat

```haskell
listToPat :: [a] -> Pattern a
```

**listToPat** takes a list of things and turns them into a pattern where each item in the list becomes an event all happening in the same cycle, looping upon subsequent cycles. Can also be called as fastFromList

```haskell
d1 $ n (listToPat [0, 1, 2]) # s "superpiano"
```

is equivalent to

```haskell
d1 $ n "[0 1 2]" # s "superpiano"
```

See also: fromList

## overlay

```haskell
overlay :: Pattern a -> Pattern a -> Pattern a
```

The overlay function is similar to cat described above, but combines two patterns, rather than a list of patterns. For example:

```haskell
d1 $ sound (overlay "bd sn:2" "cp*3")
```

...is the same as...

```haskell
d1 $ sound "[bd sn:2, cp*3]"
```

## repeatCycles

```haskell
repeatCycles :: Int -> Pattern a -> Pattern
```

**repeatCycles** is a function that repeats each cycle of a given pattern a given number of times.. It takes two inputs, the number of repeats, and the pattern you are transforming.

For example, to repeat each random cycle of notes three times:

```haskell
d1 $ repeatCycles 3 $ sound "arpy(5,8)" # n (irand 8)
```

## rev

```haskell
rev :: Pattern a -> Pattern a
```

**rev** returns a 'reversed' version of the given pattern.

For example

```haskell
rev "1 [~ 2] ~ 3"
```

is equivalent to

```haskell
rev "3 ~ [2 ~] 1"
```

Note that rev reverses on a cycle-by-cycle basis. This means that rev (slow 2 "1 2 3 4") would actually result in (slow 2 "2 1 4 3"). This is because the slow 2 makes the repeating pattern last two cycles, each of which is reversed independently.

In practice rev is generally used with conditionals, for example with every:

```haskell
d1 $ every 3 rev $ n "0 1 [~ 2] 3" # sound "arpy"
```

or jux:

```haskell
d1 $ jux rev $ n (iter 4 "0 1 [~ 2] 3") # sound "arpy"
```

## run

```haskell
run :: (Num a, Enum a) => Pattern a -> Pattern a
```

The **run** function generates a pattern representing a cycle of numbers from 0 to n-1 inclusive. Notably used to ‘run’ through a folder of samples in order:

```haskell
d1 $ n (run 8) # sound "amencutup"
```

The first parameter to run can be given as a pattern:

```haskell
d1 $ n (run "<4 8 4 6>") # sound "amencutup"
```

## scan

```haskell
scan :: (Num a, Enum a) => Pattern a -> Pattern a
```

**scan** is similar to run, but starts at 1 for the first cycle, adding an additional number each cycle until it reaches n

```haskell
d1 $ n (scan 8) # sound "amencutup"
```

## saw

```haskell
saw :: Fractional a => Pattern a
```

A sawtooth wave starting at 0, then linearly rising to 1 over one cycle, then jumping back to 0.

```haskell
d1 $ sound "bd*8" # pan (slow 2 saw)
```

## sig

```haskell
sig :: (Time -> a) -> Pattern a
```

**sig** takes a function of time and turns it into a pattern. It's very useful for creating continuous patterns such as sine or perlin. For example, saw is defined as

```haskell
saw = sig $ \t -> mod' (fromRational t) 1
```

## silence

```haskell
silence :: Pattern a
```

**silence** is the empty pattern, it contains nothing, nada. It's still useful, though!

```haskell
d1 $ sound "bd(3,8)" -- make a sound`

d1 $ silence -- stop it again

d1 $ cat [sound "bd(3,8)", silence] -- silence every other pattern
```

## sine

```haskell
sine :: Fractional a => Pattern a
```

A sine wave.

```haskell
d1 $ sound "bd*8" # pan sine
```

## slow

```haskell
slow :: Pattern Time -> Pattern a -> Pattern a
```

**slow** slows down a pattern. For example, the following will play the sound pattern "bd sn kurt" twice as slow (i.e. so it repeats once every two cycles), and the vowel pattern three times as slow:

```haskell
d1 $ sound (slow 2 "bd sn kurt")
   # slow 3 (vowel "a e o")
```

See also: fast.

## slowAppend

see append

## slowCat

see cat

## slowSqueeze

```haskell
slowSqueeze :: Pattern Time -> Pattern a -> Pattern a
```

**slowSqueeze** slows down a pattern according to the given time pattern. It is the slow analogue to fastSqueeze.

If the time pattern only has a single value in a cycle, slowSqueeze becomes equivalent to slow:

```haskell
d1 $ slow "<2 4>" $ s "bd*8"
```

is the same as

```haskell
d1 $ slowSqueeze "<2 4>" $ s "bd*8"
```

but when the time pattern has multiple values in it the behavior is a little different! Instead, a slowed version of the pattern will be made for each value in the time pattern and then they're all combined together in a cycle, according to the structure of the time pattern. For example,

```haskell
d1 $ slowSqueeze "2 4 8 16" $ s "bd*8"
```

is equivalent to

```haskell
d1 $ s "bd*4 bd*2 bd bd/2"
```

and

```haskell
d1 $ slowSqueeze "2 4 [8 16]" $ s "bd*8"
```

is equivalent to

```haskell
d1 $ s "bd*4 bd*2 [bd bd/2]"
```

## slowcat

see cat

## sparsity

see slow

## square

```haskell
square :: Fractional a => Pattern a
```

A squarewave, starting at 0, then going up to 1 halfway through a cycle.

```haskell
d1 $ sound "bd*8" # pan (cat [square, sine])
```

## stack

```haskell
stack :: [Pattern a] -> Pattern a
```

**stack** takes a list of patterns and combines them into a new pattern by layering them up - effectively playing all of the patterns in the list simultaneously.

```haskell
d1 $ stack [
  sound "bd bd*2",
  sound "hh*2 [sn cp] cp future*4",
  sound "arpy" +| n "0 .. 15"
]
```

This is particularly useful if you want to apply a function or synth control pattern to multiple patterns at once:

```haskell
d1 $ whenmod 5 3 (striate 3) $ stack [
  sound "bd bd*2",
  sound "hh*2 [sn cp] cp future*4",
  sound "arpy" +| n "0 .. 15"
] # speed "[[1 0.8], [1.5 2]*2]/3"
```

## timeCat

```haskell
timeCat :: [(Time, Pattern a)] -> Pattern a
```

**timeCat** is like fastcat except that you provide proportionate sizes of the patterns to each other for when they're concatenated into one cycle. The larger the value in the list, the larger relative size the pattern takes in the final loop. If all values are equal then this is equivalent to fastcat, e.g. the following two code fragments are equivalent.

```haskell
d1 $ fastcat [s "bd*4", s "hh27*8", s "superpiano" # n 0]

d1 $ timeCat [(1, s "bd*4"),
              (1, s "hh27*8"),
              (1, s "superpiano" # n 0)]
```

## tri

```haskell
tri :: Fractional a => Pattern a
```

A triangle wave, starting at 0, then linearly rising to 1 halfway through a cycle, then down again.

```haskell
d1 $ sound "bd*16" # speed (slow 2 $ range 0.5 2 tri)
```

## when

```haskell
when :: (Int -> Bool) -> (Pattern a -> Pattern a) -> Pattern a -> Pattern a
```

Only when the given test function returns True the given pattern transformation is applied. The test function will be called with the current cycle as a number.

```haskell
d1 $ when ((elem '4').show) (striate 4) $ sound "hh hc"
```

The above will only apply striate 4 to the pattern if the current cycle number contains the number 4. So the fourth cycle will be striated and the fourteenth and so on. Expect lots of striates after cycle number 399.

## whenT

todo

## zoom

```haskell
zoom :: (Time, Time) -> Pattern a -> Pattern a
```

Plays a portion of a pattern, specified by the beginning and end of a time span (known as an 'arc'). The new resulting pattern is played over the time period of the original pattern:

```haskell
d1 $ zoom (0.25, 0.75) $ sound "bd*2 hh*3 [sn bd]*2 drum"
```

In the pattern above, zoom is used with an arc from 25% to 75%. It is equivalent to this pattern:

```haskell
d1 $ sound "hh*3 [sn bd]*2"
```

Here’s an example of it being used with a conditional:

```haskell
d1 $ every 4 (zoom (0.25, 0.75)) $ sound "bd*2 hh*3 [sn bd]*2 drum"
```
