Known bugs/oddities that I don't think are worth fixing. At least not yet.

* You can cut the waybread with "your" sword, even if the sword is still stuck in the rock. I guess you're just holding the bread up to the sword.
* You can't drink from the lake or the Flathead Ocean. It seems `GLOBAL-WATER` isn't used at all.
* `VALUABLES-F` have a custom message for when the man has left, but since you can't get to the valuables at that point it's never seen.
* It seems odd that neither the Flathead Ocean nor the Lake have `GLOBAL-WATER`.
* The vial is described as transparent, but doesn't have `TRANSBIT`. Perhaps so that you won't "see" the potion until you open it? Maybe not a bug after all.
* \[Andrew Plotkin] If you die with the mirror box at a 45 degree angle, the description outside will still indicate that the corridor is completely blocked.
