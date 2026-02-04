# Common changes

## Changes made while preparing the r200 versions

### Larger features

* Climbing ("`CLIMB UP/DOWN"`, "`CLIMB UP/DOWN` *object*") has been rewritten a bit to eliminate Zork III special cases, and fix some regressions. Hopefully it didn't introduce any new ones.
* Restored old `V-OPEN` / `V-CLOSED` behavior where objects need `CONTBIT` to be openable or closable by default. This behavior goes back all the way to Mainframe Zork, and is necessary for Zork II and III where certain objects are "doors" but not meant to be opened or closed. The `CLEFT` in Zork III is one such example. I assume it won't do any harm to Zork I either.
* Restored old `GWIM` behavior so that it never matches `ME`. The syntax for `THROW` tells it to pick an actor as the indirect object, if none is specified. The ME object is an actor, and in Zork I and III it's often the only actor present. (In Zork II, there's `GLOBAL-PRINCESS`.) That meant that "`THROW` *object*" was often lethal, which seemed unfair.

### Minor features

* `X` is now accepted as a synonym for `EXAMINE`.
* The `OPEN-CLOSE` routine from Zork I and II is now in the common code.
* `POINT` and `AIM` have been reinstated as synonyms for WAVE. I can't be the only one who chinks "`POINT WAND AT` *object*" is the natural syntax.
* There is now a `NOW-DARK?` routine, as documented in Learning ZIL.
* Inform the player if pouring water on an object causes the room to go dark.
* \[Zork I Nathan-47, Nathan-56\] The `OVERBOARD` and `THROW-OFF` actions now use `PRE-PUT` as their pre-action to prevent the user from doing stupid things like throwing away his own hands.
* You can now get the old "You can't take it; thus, you can't shake it\!" message for trying to shake a non-takeable object again.
* "`TURN` *object*" and "`TURN` *object* `WITH HANDS`" now work the same.
* \[Zork I Nathan-46\] "`PLAY ME`" no longer kills the player. I couldn't resist adding a response of my own for this case.
* "`TIE` *object* `TO ME`" now prints the intended response.
* "`SIT ON`" / "`SIT IN`" now has its own syntax. Otherwise, "`SIT`" gets interpreted as "`CLIMB`", which gets interpreted as "`UP`".

### Bugfixes

* \[Zork I Nathan-75\] Pouring water on a burning object no longer clears its `FLAMEBIT`. It's still a potential source of fire, it's just no longer turned on. An exception is made for the Zork I and II matchbooks.
* \[Zork I Cree-23\] It's absolutely not allowed to put object A inside object B if object B is already inside object A. This could happen in Zork I with the boat and the coffin.
* Replaced `HELD?` (with one parameter) with `ULTIMATELY-IN?` (with two parameters), as documented in Learning ZIL. `HELD?` is still available, with the same syntax as before, but now it's a macro.
* Don't allow putting an object that's inside a closed container into anything. We do this in `PRE-PUT` but not in `PRE-GIVE`, because we still want "`GIVE WATER TO CYCLOPS`" to work in Zork I.
* Removed unnecessary linebreak when trying to drink food that you weren't holding.
* A "`Huh?`" message when failing to throw an object has been removed.
* Fixed incorrect message when drying to drop an object from a closed container. It would say that the object was closed, rather than the container. But I can't reproduce this in any compiled version, only when compiling it myself. 
* Fixed "`SWIM`" printing garbage in Zork I and II.
* Check if you're using your hands in `PRE-MUNG`, so that it can print "your bare hands" instead of the name of the object.
* `ACCESSIBLE?` now treats object 0 as not accessible. This could happen when referring to "it" after `REMOVE-CAREFULLY` had removed the object.

## Changes in the final unreleased versions

### Infrastructure changes

This is going to be a partial list, since I only have the vaguest of ideas how the infrastructure works.

* AGAIN is now baked into the parser, rather than an action.
* The "`OOPS`" command has been added.
* The unused routine `DEMON` has been removed, but the `DEMON` parameter to `INT` still remains. Probably the remains of some now obsolete feature?
* The move counter no longer wraps around at 999 moves.
* `NOT-HERE-PRINT` no longer prints a leading space.
* The lines `<ZSTR-OFF>` and `<ZSTR-ON>` have been removed from gmacros.zil. It's unclear what \- if anything \- these did, as ZILF ignores them.
* `MAIN-LOOP` has been refactored into `MAIN-LOOP` and `MAIN-LOOP1`. I'm not sure why.
* The way "it" works has changed to some extent. For instance, "`TAKE LEAFLET. SOUTH. READ IT`" works, while r88 would print "`I don't see what you are referring to.`" This is presumably what's meant by "The New ITness" in the "Misc ZIL" Infocom Cabinet.
* Some debug commands have been added, e.g. recording and replaying commands, seeding random numbers, …

## Standard verb changes

* Some hyphenation has been removed
* super-brief \-\> superbrief
* mid-air \-\> midair
* Some debug verbs have been added: `#RANDOM`, `#COMMAND`, `#RECORD`, `#UNRECORD`
* Boarding water is now interpreted as swimming.
* In addition to killing you when burning an object you're holding, you now die if you burn an object that you are inside.
* Some special cases have been added to the climbing verbs. Some of these are probably responsible for a regression in Zork III, where "`CLIMB DOWN`" in a room with a downward exit would print nonsense.
* You can no longer cut an object that you are inside.
* There are some special cases for the various Zorks, but I suspect they were in the individual games in earlier versions.
* "`DISEMBARK`" or "EXIT" while you're in a vehicle now silently assume you mean that vehicle.
* You now must be carrying things you want to eat or drink.
* Some changes to water. I think some may involve handling the closed bottle in Zork I better, but I'm not sure.
* A "`Huh?`" message has been added to `V-OVERBOARD` as a last resort.
* A feature from Mainframe Zork where "PLAY *object*" may kill you has been added.
* The "`Swimming isn't usually allowed in the dungeon.`" message can now refer to an object instead.
* `V-THROUGH` now checks if there is an other side to a door before walking through it.
* The `INDENTS` table is now `PURE`, i.e. resides in read-only memory.
* For some reason, `POINT` and `AIM` were removed as synonyms for `WAVE`.

