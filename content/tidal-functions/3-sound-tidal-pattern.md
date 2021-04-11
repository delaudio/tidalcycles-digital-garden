---
title: 'Sound.Tidal.Pattern'
metaTitle: 'Sound.Tidal.Pattern'
metaDescription: 'Sound.Tidal.Pattern'
---

## __compress

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

## __compressTo

todo

## _fastGap

```haskell
fastGap :: Pattern Time -> Pattern a -> Pattern a
```

**fastGap** speeds up a pattern like fast, but rather than it playing multiple times as fast would it instead leaves a gap in the remaining space of the cycle. For example, the following will play the sound pattern "bd sn" only once but compressed into the first half of the cycle, i.e. twice as fast.

```haskell
d1 $ sound (fastGap 2 "bd sn")
```

See also: fast.

## applyFIS

todo

## arcCycles

todo

## arcCyclesZW

todo

## compareDefrag

todo

## cycleArcsInArc

todo

## cyclePos

todo

## cyclesInArc

todo

## defragParts

todo

## empty

todo

## eventHasOnset

todo

## eventPart

todo

## eventValue

todo

## eventWhole

todo

## eventWholeOnset

todo

## fNum2

todo

## filterJust

todo

## filterValues

todo

## filterWhen

todo

## getF

todo

## getI

todo

## getS

todo

## innerJoin

todo

## isAdjacent

todo

## isAnalog

todo

## isDigital

todo

## isIn

todo

## mapArc

todo

## mapCycle

todo

## matchManyToOne

todo

## nextSam

todo

## noOv

todo

## onsetIn

todo

## outerJoin

todo

## playFor

todo

## prettyRat

todo

## queryArc

todo

## rotL

```haskell
rotL :: Time -> Pattern a -> Pattern a -> Pattern a
```

**rotL** Shifts a pattern back in time by the given amount, expressed in cycles.

This will skip to the fourth cycle when evaluated:

```haskell
do
{
  resetCycles;
  d1 $ rotL 4 $ seqP [
    (0, 12, sound "bd bd*2"),
    (4, 12, sound "hh*2 [sn cp] cp future*4"),
    (8, 12, sound (samples "arpy*8" (run 16)))
  ]
}
```

Useful when building and testing out longer sequences.

## rotR

**rotR** is the opposite of rotL as it shifts the pattern forwards in time.

## sam

todo

## showFrac

todo

## showPattern

todo

## splitQueries

todo

## subArc

todo

## tParam

todo

## tParam2

todo

## tParam3

todo

## tParamSqueeze

todo

## timeToCycleArc

todo

## toTime

todo

## unwrap

todo

## unwrapSqueeze

todo

## withEvent

todo

## withEvents

todo

## withPart

todo

## withQueryArc

todo

## withQueryTime

todo

## withResultArc

todo

## withResultTime

todo
