# Zork II

# r200

This version is one I'm working on, trying to fix as many of the known bugs as I reasonably can.

### Restoration of old behavior

* Looking into the palantirs didn't work because the old `ROOM?` routine had been replaced with a macro that did something else entirely. The routine returned `T` if the object was in a room, unless it was carried by the player. The macro was used to compare `HERE` to a set of objects, kind of like how `VERB?` and similar macros work. The macro has been removed and the routine reinstated.
* \[Cree-6\] The sword had been changed from "sword" to "elvish sword", presumably for a better disambiguation message between the sword and the nicked swords. But this opened a can of worms where it was now described as "a elvish sword" instead of "an elvis sword", and we don't have any mechanism for dealing with that. So it's back to a plain "sword" again. The disambiguation problem has been solved in a different way.
* The Fantasize spell should work again. It was broken completely in r48, and while an attempt had been made to restore it in r63 it didn't work correctly. I hope it does now.
* Removed TAKE and HAVE from the "`LIGHT OBJECT`" syntax. Otherwise, "LIGHT FUSE" will auto-pickup the fuse before lighting it, before you can even say "Wile E. Coyote, Genius". Historically, Zork I always did the auto-pickup thing for this syntax. Zork II and III only added it later, presumably when the code bases were unified. But I can't think of any downsides to keeping it in Zork III. Technically it looks like Zork II used to still have the HAVE flag, but maybe it wasn't as strictly enforced back then? Note that in earlier versions you couldn't even "`LIGHT FUSE`". Before r48 you were told to use a match instead.
* Removed "footpad" as a synonym for the sailor when compiling as Zork II, because that game already has a `FOOTPAD` object.This means that you can once again ask the game what a footpad is. This was only broken in r63.

### Changes to game mechanics

* Zork II implements spell casting as `ENCHANT` and `DISENCHANT` actions, perhaps because it allows objects to easily react to spells. Not that this actually gets used much. But it also allows the player to type "`ENCHANT` *object*" and "`DISENCHANT` *object*". This was not a problem for `ENCHANT`, since you can't specify how the object should be enchanted. `V-ENCHANT` can just tell you that nothing happens. But the `DISENCHANT` action is invoked by `I-SPELL` when the spell times out. A lot of the time that won't print a message, and neither would typing "`DISENCHANT` *object*". So I've introduced a hack where the `DISENCHANT` action has a dummy `PRSI` value when called by the timer. So now V-DISENCHANT can print that nothing happens if `PRSI` is `<>` and work as before in the other case. While it's tempting to allow the player to actually disenchant objects, that raises too many issues. Let's just say that the player has mastered magic on a *Sorcerer's Apprentice* level, where he knows how to cast spells he's seen but has no idea how to undo them afterwards.
* When the wizard casts the Freeze spell on you, you are now allowed to quit, restart, restore, or even save. You're allowed to use these commands in the Loud Room in Zork I, so I don't see why you shouldn't be allowed to here. Having to wait for the spell to time out is just annoying.
* The robot now only accepts single commands. While handling multiple commands is undeniably cool (and necessary in Enchanter\!), in Zork II it opens up an enormous can of worms. You can see the result of actions that should be out of sight, you can turn the Low Room deadly while you're standing in it, yet suffer no harm, ...

### Other minor features

* In `WHICH-PRINT`, when printing the name of the `SWORD` object, print "elvish sword" instead of the real object name. This gives a better disambiguation message between it and the nicked swords. This applies to all Zork games, but I only think it's ever noticeable in Zork II.
* It appears someone went through the game and changed a few REMOVE to REMOVE-CAREFULLY. This has the advantage of informing the player if the room goes dark as a result. Unfortunately, in some cases this led to the darkness message being printed before the message describing what happened. I have fixed the ones I've found:
* Giving your only light to the Volcano Gnome
* Objects could react to "`WAVE WAND AT` *object*", but since the wand handles the default response and `PRSI` gets the first shot, it was not possible for them to respond to "`RUB` *object* `WITH WAND`" even though these commands are supposed to do the same thing. I have introduced a `PRE-RUB` routine that rewrites it to a `WAVE` action. This affects grues and Cerberus.
* \[Nathan-51\] WATER-FCN has been adjusted to handle "`TAKE WATER`" more like Zork I. So if there is anything in the teapot, it will now say "`The water slips through your fingers.`" rather than "`The teapot isn't currently empty.`" The old message was particularly confusing if the object inside the teapot was water. "`TAKE WATER`" if the filled teapot was in the room would pick it up without printing any message. Now it tells you that "`It's in the teapot. Perhaps you should take that instead.`" I made the assumption here that Zork I is the better tested game of the two.
* \[Nathan-63\] "`PUT REPELLENT ON` *object*" now acts as a synonym for spraying the repellent on the object. "`PUT REPELLENT IN` *object*" still works too, since that's another way of writing "`APPLY REPELLENT TO` *object*". I have added exceptions for if you're trying to put the repellent on a surface or in a container. In those cases it assumes that you do not want to spray the target.
* The balloon \- the receptacle in particular \- has received a number of fixes to make it work more consistently:
* \[Nathan-78\] Much of the custom behavior for the receptacle (and some other bits) were in BALLOON-FCN to be run in the M-BEG case. But that meant they were only run when the player was inside the balloon, not when standing on the ground outside. E:g. it was possible to put more than one object inside it from outside the balloon.
* "`INFLATE` *object*" now checks that it's the balloon you're trying to inflate before saying that you can't.
* Only objects that can be put in the receptacle are given DESCBIT to hide them from the standard object lister. (They're printed as part of the room description instead.) For instance, before you could try to put your sword in the *closed* receptacle, and if you later dropped it you wouldn't be able to see it in the room.
* After you've poured water on a burning object in the receptacle, trying to pick it up now gives a much clearer message explaining why you can't. (When you put objects in the receptacle, their `TAKEBIT` is cleared, but the default message for taking them didn't make sense.)
* Looking inside the receptacle no longer incorrectly claims that it's empty.
* "`TAKE` *object* `FROM RECEPTACLE`" no longer claims that the object is an integral part of the balloon.
* \[Nathan-76\] When looking through the window in the Dreary Room, check if the Tiny Room is lit and, if not, tell the player that he sees only darkness. Otherwise, the game would tell you that you were likely to be eaten by a grue, and that doesn't make sense since you're in a lit room. This is similar to how the palantirs work.
* \[Nathan-62\] Added a custom response for "`SMELL ROSES`", since the default message is grammatically incorrect. The new message is borrowed from Deadline.
* "`PUT WATER IN WATER`" and "`PUT WATER IN ME`" now gives more sensible responses. The first is rejected, and the second is interpreted as drinking the water.
* "`DRINK FROM TEAPOT`" now works, to be consistent with Zork III's "`DRINK FROM VIAL`".
* \[Nathan-69\] Breaking the flask now mungs the room, i.e. makes it inaccessible. Opening the flask already did that, and surely breaking it should be even worse.
* The `TUNNEL` object now accepts "smokey" as an adjective in addition to "smoky", since the game itself refers to "smokey" and I'd rather not change any text unless there's a clear typo. A similar object appears in Zork III, but there nothing is ever referred to as "smokey" except "Smokey the Bear". Zork I doesn't have a global `TUNNEL` object, perhaps because a sizable chunk is set above ground.
* Give the unicorn TOUCHBIT when you see it. The game would test for it when trying to follow or find the unicorn, but since it would never set it would always respond that "`The unicorn is a mythical beast.`" Now it responds that "`I don't know where it is now.`" instead, once you know it's real.
* It's now possible to put things in the chasm. There was code for it, but it checked if PRSI was PSEUDO-OBJECT, not CHASM. I guess the chasm used to be a pseudo object and was changed to a proper object later. Though it seems it never worked even in the oldest versions of the game.
* Using Filch or Float on an object now sets its TOUCHBIT, so that you won't see potentially misleading `FDESC` descriptions for the object.
* The `FDESC` descriptions of the library books refer to each other, so it's supposed to set their `TOUCHBIT`s whenever that's no longer safe. It did so by intercepting a few commands, most noticeable `TAKE`. But it didn't take automatic taking, burning the books, etc. into account. Monitoring if the books are still present in the `M-END` case of the room's action routine should be more robust.
* \[Nathan-75\] In Path Near Stream and Marble Hall, only refer to the whirring sound if the carousel is still spinning. Everything I've read suggests that it's the spinning that creates the sound. You only hear the whirring in the Carousel Room if it's spinning, in Room 8 the whirring is only mentioned in Zork III, and the InvisiClues clearly say that it's a magnetic field that causes the carousel to spin. So once that field is shifted, the whirring should stop. I also changed "`There is rather annoying whirring sound`" to "`There is a rather annoying whirring sound`".
* Repurposed an unused message in `DRAGON-FCN`. What was meant to be printed when the played walked north in the Dragon Room (but wasn't, because the dragon's action routine has nothign to do with that) is now printed in response to "`WALK AROUND DRAGON`" instead.
* Changed the `LDESC` on `SERPENT` to `FDESC`, so that it will be printed. With `LDESC` you just get the default container contents list.
* Similarly, changed `LDESC` on the clear crystal sphere to `FDESC` so that it will be printed once the aquarium is shattered. That means there is no need to clear `LDESC` when picking it up. (Which wasn't foolproof anyway.)
* The gold key now has `TOOLBIT` to be consistent with the iron key. This allows "`UNLOCK DOOR`" to infer the key, if it's the only tool you're carrying.
* The sword demon now gets put on the clock chain early, like in Zork I. Otherwise the Wizard demon runs after the sword demon, causing the sword to not instantly glow when the wizard appears and hangs around for a bit.
* The blue sphere now starts out on the table instead, like its description suggests. Otherwise the game will describe the table as empty.
* It's only possible to put the mat under the oak door while it's closed. Opening the door dislodges the mat object, same as taking or moving the mat.
* Throwing the flask at the aquarium now mungs the room and removes the flask.
* You can now order the robot to leave the room while you're trapped in the cage. It's not a good idea by any means, but it makes sense that you can do it.

### Bugfixes

* \[Nathan-58\] It's no longer possible to open or close the aquarium.
* \[Nathan-68\] Putting the mat under the door now gives it `TRYTAKEBIT`. Having it be automatically picked up messes up the puzzle.
* \[Nathan-45\] Looking at the flask through itself now prints one of the `YUKS` messages, rather than a double message about the flask magnifying and distorting.
* \[Nathan-38\] When dying, make the match go out. Otherwise, it could become an infinite source of light since `I-MATCH` is disabled in `KILL-INTERRUPTS`. This had already been fixed in Zork I.
* \[Cree-9\] Don't allow the bucket to be opened or closed.
* When closing the lid on the door, check if it is already closed.
* The guarded door has received some bugfixes.
* The key could be dropped or stolen by the guardian even if you weren't carrying it. I don't know why the game allows you to even try in that case, but I didn't want to change that sort of things.
* \[Nathan-61\] It didn't check what you were trying to use to unlock the door. That meant you had a random chance of just teleporting the key to the room. This wasn't exploitable since you can't pick up the key until you rescue the princess, but still obviously wrong.
* Counting the matches can no longer tell you that you have \-1 matches left. This fix has been copied from Zork I.
* \[Nathan-48\] Throwing an object at the robot now moves it to the room.
* \[Nathan-48\] "`DESTROY ROBOT WITH SWORD`" now removes the robot, not your hands.
* Don't allow the dragon to escape his area. He already won't follow you into the rooms bordering it, but since `FIND-TARGET` was trying to find the player from `HERE`, not from the dragon's location, he would follow you into the next room if he was still angry.
* Only describe the dragon as blocking the way to the north when he's in his lair.
* \[Nathan-59\] Don't allow cutting objects with the wooden club, even though it's a weapon.
* Trying to walk the balloon into a room witout `NONLANDBIT` would still set `BLOC` to that room even though the movement would fail. The next time the balloon moved on its own, it would end up in the wrong room. The normal use case for walking the balloon is as an alternative to typing "`LAND`", and that still works.
* The hole in the Safe Room is now a container. It already acted like one, for the most part, but "`LOOK IN HOLE`" only worked while it was empty. Now that it's a container, we also have to make sure it can't be opened or closed.
* When turning the iron key, only lock/unlock the door if the key is in the keyhole.
* Doors can now only be locked or unlocked while they're closed.
* Looking through the window in the oak door now omits the description of the door from both directions. That was the intention, but it didn't work right.
* Looking into another room (by palantir or through the window) no longer marks that room as visited. That appears to have been the intention all along, but the code for it was buggy.
* \[Nathan-65\] Putting an object in the keyhole no longer causes the object in the other keyhole to disappear.
* Taking the mat from under the door now both dislodges the mat object and picks up the mat.
* Destroying the aquarium no longer makes you drop the object you destroy it with. (Minor point, since doing this is fatal.)
* Removed special case for the bomb from `AQUARIUM-FCN`. All it did was to stop the fuse demon, then print no message. Very strange. Now it bounces harmlessly off the glass. The room will be munged by the explosion, as usual.
* `CAKE-CRUMBLE` now only crumbles the cakes. Before it could crumble any object as long as it had `FOODBIT`, like the candy.

### Stylistic fixes

* The copyright years in the newspaper have been updated to match the ones in the game banner. This was done in earlier versions when the copyright years changed.
* `[Nathan-64] WATER` and `SALTY-WATER` now have `TRYTAKEBIT` so that you can't automatically pick up the water without a container. Note that `SALTY-WATER` isn't actually used in the game. It was in older versions, but apparently never worked correctly.
* Added a missing newline when attacking the princess.
* Added a missing newline the first time your compass starts spinning.
* Removed an unnecessary blank line in `JIGS-UP` when someone other than the player dies, e.g. the robot.
* Removed unnecessary newline when reading the Wizard's warning label.
* Lighting the fuse now prints "(with the match)" when appropriate. This is consistent with lighting the candles in Zork I, even though the implementation is a bit different.
* Added missing newlines before `FINISH` when dying permanently.
* `Some fixes for the matchbook` have been brought over from Zork I:
* Lighting a match in a dark room now prints the room description.
* Blowing out a match now tells you if the room goes dark.
* When a light goes out, you are now told if the room went dark.
* Pouring water on a burning match now stops its demon.
* Tell the player if the room goes dark when breaking the lamp.
* Removed unnecessary newline for the sign in `TELLER-ROOM`.
* Tell the player if the room goes dark when the lizard eats an object.
* Tell the player if the room goes dark when you put an object in the well.
* Tell the player if the room goes dark when using the Fry spell.
* Added missing newline when looking through the window in the oak door. Usually you wouldn't notice it was missing because you'd see the blue sphere on the table.
* Removed leading space from the balloon label and safe card texts. Judging by Confusion, these were blank lines in Mainframe Zork. We don't want that any more, and ZIL rendered it as a single space anyway.

# r63

This version was never publicly released, but I have tried to understand what changes were made compared to the r48 sources.

## Gameplay changes

* The response to "`TAKE SAFE`" now properly checks that you're not doing "`TAKE` *object* `FROM SAFE`".
* Things like "`GNOME, HELLO`" would print a double message. (The same fix has been applied to both gnomes.)
* The code for pushing the square button has been modified, but shouldn't have any functional changes.
* Several (but not all\!) cases where talking to the robot while in anything else than `HERE` (e.g. while in the bucket) would print no message have been fixed.
* The Gnome of Zurich is now carrying an actual safety deposit box, as described. Probably as a consequence, he now has `CONTBIT` and `OPENBIT`.
* The dragon can no longer resurrect when you die, because `DRAGON-LEAVES` now checks if the `DEAD-DRAGON` object has been moved into the game.
* If you attack the princess, and the Wizard is already in the room, the game will now say "`Shocked, the Wizard of Frobozz turns toward you.`" instead of "`Just in time, the Wizard of Frobozz appears, seeming to unroll himself out of nothing like a window shade.`"
* Some hyphenation has been removed: "mid-air" is now "midair".
* The serpent now reacts to any attempt at ordering it around.
* If you give the box with the violin to the demon, the game now checks if the box is already open. The demon then "`looks inside the box`" instead of "`opens the box`". The box is then removed.
* The demon will not accept the sword as a treasure. The sword also no longer has a `VALUE` property, so this should be doubly fixed.
* The demon now starts out with the `INVISIBLE` bit. There is a case where the Wizard will run from the room if the demon isn't invisible, so it probably has to do with fixing some bug there.
* Several Wizard messages have been adjusted if they happen in a dark room:
* The message "`The Wizard vanishes.`" isn't shown at all.
* Any message where the Wizard appears becomes "`You feel a slight outrush of air as something moves nearby.`"
* If the Wizard disappears because you have the Black Crystal, the message becomes "`You feel a sudden inrush of air as though something disappeared.`"
* The Wizard muttering something, then disappearing, becomes "`You hear low, confused muttering.`"
* The Wizard using his wand becomes "`Suddenly, illuminated by the faint blue glow of a magic wand pointed in your direction, you see the Wizard!`"
* The "`He cackles gleefully!`" message when the Wizard casts a spell on you has been clarified to "`He then vanishes, cackling gleefully.`"
* I'm a bit uncertain, but I think a bug in `ROB` has been fixed, where it would never steal the first object, but it might try to steal object 0\.
* `GLOBAL-MENHIR` now has an action routine, just so that it can print "`It's not here.`"
* `GLOBAL-CERBERUS` now has an action routine, just so that it can print "`He's not here.`"
* There are now colons (e.g. "`it says:`" instead of "`it says`") before signs in the Bank of Zork.
* An unnecessary blank line has been removed at the end of the balloon instructions.
* An unnecessary blank line has been removed at the end of the robot instructions.
* Two unnecessary blank lines have been removed at the end of the newspaper.
* The safe now accepts "rusty" as an adjective.
* The purple book now has `SEARCHBIT`. This apparently has something to do with how deep the parser looks inside containers. Steve Meretzky called this a "slipper concept", and also a "stupid concept", and that he automatically sets it on all his containers.
* The sword is now an "elvish sword". This was probably to get a better disambiguation message between the sword and the nicked swords, but it opens a can of worms where the sword is now "a elvish sword" rather than "an elvish sword".

## Infrastructure changes

* `JIGS-UP` can now be called without any parameters.
* An unused `BLAST` routine has been commented out. Investigate if this used to be the response to "`BLAST SAFE`".
* The lamp demon seems to have been rewritten, but probably works the same as before, or at least consistent with the other games.

