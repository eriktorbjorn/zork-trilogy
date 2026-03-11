# Common changes

## Changes made while preparing the r200 versions

### Restoration of old behavior

* Zork II: The Fantasize spell should work again. It was broken completely in r48, and while an attempt had been made to restore it in r63 it didn't work correctly. I hope it does now.
* Zork III: "`THROW ROPE OVER CLIFF`" works again. The parser has gotten more strict about enforcing the `HAVE` syntax token, but since the pre-action checks that you are holding the object (with some exceptions) removing it from the `THROW-OFF` syntax should be safe.

### Larger features

* Climbing ("`CLIMB UP/DOWN"`, "`CLIMB UP/DOWN` *object*") has been rewritten a bit to eliminate Zork III special cases, and fix some regressions. Hopefully it didn't introduce any new ones.
* Restored old `V-OPEN` / `V-CLOSED` behavior where objects need `CONTBIT` to be openable or closable by default. This behavior goes back all the way to Mainframe Zork, and is necessary for Zork II and III where certain objects are "doors" but not meant to be opened or closed. The `CLEFT` in Zork III is one such example. I assume it won't do any harm to Zork I either.
* Restored old `GWIM` behavior so that it never matches `ME`. The syntax for `THROW` tells it to pick an actor as the indirect object, if none is specified. The ME object is an actor, and in Zork I and III it's often the only actor present. (In Zork II, there's `GLOBAL-PRINCESS`.) That meant that "`THROW` *object*" was often lethal, which seemed unfair.
* \[Zork II Nathan-27] Demons now always run with the player as the `WINNER`. Otherwise strange things could happen, e.g. in Zork II if you talked to the robot on the move a timed event was supposed to kill you but instead it killed the robot. I think some other Infocom games did this too.

### Minor features

* `X` is now accepted as a synonym for `EXAMINE`.
* The `OPEN-CLOSE` routine from Zork I and II is now in the common code.
* `POINT` and `AIM` have been reinstated as synonyms for WAVE. I can't be the only one who chinks "`POINT WAND AT` *object*" is the natural syntax.
* There is now a `NOW-DARK?` routine, as documented in Learning ZIL.
* Inform the player if pouring water on an object causes the room to go dark.
* \[Zork I Nathan-47, Nathan-56] The `OVERBOARD` and `THROW-OFF` actions now use `PRE-PUT` as their pre-action to prevent the user from doing stupid things like throwing away his own hands.
* You can now get the old "You can't take it; thus, you can't shake it!" message for trying to shake a non-takeable object again.
* "`TURN` *object*" and "`TURN` *object* `WITH HANDS`" now work the same.
* \[Zork I Nathan-46] "`PLAY ME`" no longer kills the player. I couldn't resist adding a response of my own for this case.
* "`TIE` *object* `TO ME`" now prints the intended response.
* "`SIT ON`" / "`SIT IN`" now has its own syntax. Otherwise, "`SIT`" gets interpreted as "`CLIMB`", which gets interpreted as "`UP`".
* Zork II: Don't require a tool to turn the key. This is the same kind of exception that was already made for turning the page in the black book in Zork I.
* Zork II: Don't allow objects to be automatically taken from the bucket when you're outside.
* Zork II: Don't allow enlargened objects (in the Alice area) to be automatically picked up.
* `PRE-MUNG` now requires you to be holding the weapon. Before you could destroy the robot in Zork II with a sword he was holding.
* Zork II: Allow trying to break `GLOBAL-PALANTIR` with your hands, since there's a custom message for it. (These are the spheres you pass through before resurrection.)
* Zork II: Casting Float on an object now moves it to the room, i.e. out of containers or your inventory.
* Instead of checking `TRYTAKEBIT` to see if an object can't be automatically picked up, the parser now calls `INHIBIT-AUTO-PICKUP?`. The default implementation is to check `TRYTAKEBIT`, but this will make some other changes so much cleaner.
* Zork II: Removed the `HAVE` bit from the "`LIGHT` *object*" syntax. We want to keep the automatic taking for consistency with the other games, but we also want to be able the light the fuse without holding it.
* Zork II: The "Fluoresce" spell now only sets `ONBIT`, not `LIGHTBIT`, and if it's used on the lamp it loses the `LIGHTBIT`. Custom messages have been added for trying to turn enchanted objects on or off.

### Bugfixes

* \[Zork II Nathan-23] Hopefully fixed some misleading indentation when listing objects in a room. There was some code there with a comment that it was not in Zork III, and removing seems to have done the trick.
* \[Zork I Nathan-75] Pouring water on a burning object no longer clears its `FLAMEBIT`. It's still a potential source of fire, it's just no longer turned on. An exception is made for the Zork I and II matchbooks.
* \[Zork I Cree-23] It's absolutely not allowed to put object A inside object B if object B is already inside object A. This could happen in Zork I with the boat and the coffin.
* Replaced `HELD?` (with one parameter) with `ULTIMATELY-IN?` (with two parameters), as documented in Learning ZIL. `HELD?` is still available, with the same syntax as before, but now it's a macro.
* Don't allow putting an object that's inside a closed container into anything. We do this in `PRE-PUT` but not in `PRE-GIVE`, because we still want "`GIVE WATER TO CYCLOPS`" to work in Zork I.
* Removed unnecessary linebreak when trying to drink food that you weren't holding.
* A "`Huh?`" message when failing to throw an object has been removed.
* Fixed incorrect message when drying to drop an object from a closed container. It would say that the object was closed, rather than the container. But I can't reproduce this in any compiled version, only when compiling it myself.
* Fixed "`SWIM`" printing garbage in Zork I and II.
* Check if you're using your hands in `PRE-MUNG`, so that it can print "your bare hands" instead of the name of the object.
* `ACCESSIBLE?` now treats object 0 as not accessible. This could happen when referring to "it" after `REMOVE-CAREFULLY` had removed the object.
* When an actor leaves the room, print the message as long as the player is enclosed by the same room. E.g. in Zork II you will see the robot leave the room even if you order him to leave from within the bucket.

### Stylistic changes

* Added newline to the message when the balloon lands.

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
* Some debug commands have been added, e.g. recording and replaying commands, seeding random numbers, ...

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

