# Zork II

# r200

This version is one I'm working on, trying to fix as many of the known bugs as I reasonably can.

### Restoration of old behavior

* Looking into the palantirs didn't work because the old `ROOM?` routine had been replaced with a macro that did something else entirely. The routine returned `T` if the object was in a room, unless it was carried by the player. The macro was used to compare `HERE` to a set of objects, kind of like how `VERB?` and similar macros work. The macro has been removed and the routine reinstated.
* \[Cree-6] The sword had been changed from "sword" to "elvish sword", presumably for a better disambiguation message between the sword and the nicked swords. But this opened a can of worms where it was now described as "a elvish sword" instead of "an elvis sword", and we don't have any mechanism for dealing with that. So it's back to a plain "sword" again. The disambiguation problem has been solved in a different way.
* Removed "footpad" as a synonym for the sailor when compiling as Zork II, because that game already has a `FOOTPAD` object. This means that you can once again ask the game what a footpad is. This was only broken in r63.
* The "Fierce" spell now makes the sword glow dull red again.
* The sword is once again listed before the lamp in the starting room.
* The sword now only glows for dangerous actors, i.e. the dragon, Cerberus, the demon, and the Wizard. Older versions of Zork II used to have a separate attribute for this, while newer checked for all actors but made an exception for the robot. Rather than reintroducing an attribute, I've just added a `VILLAIN?` routine to test if an object is a villain.
* The sword now stops glowing if you die. This appears to be a regression from when `GLOW-STATE` was introduced to keep track of the sword's state. Before that it used its `VALUE` property, which accidentally turned the sword into a treasure.

### Changes to game mechanics

* Zork II implements spell casting as `ENCHANT` and `DISENCHANT` actions, perhaps because it allows objects to easily react to spells. Not that this actually gets used much. But it also allows the player to type "`ENCHANT` *object*" and "`DISENCHANT` *object*". This was not a problem for `ENCHANT`, since you can't specify how the object should be enchanted. `V-ENCHANT` can just tell you that nothing happens. But the `DISENCHANT` action is invoked by `I-SPELL` when the spell times out. A lot of the time that won't print a message, and neither would typing "`DISENCHANT` *object*". So I've introduced a hack where the `DISENCHANT` action has a dummy `PRSI` value when called by the timer. So now V-DISENCHANT can print that nothing happens if `PRSI` is `<>` and work as before in the other case. While it's tempting to allow the player to actually disenchant objects, that raises too many issues. Let's just say that the player has mastered magic on a *Sorcerer's Apprentice* level, where he knows how to cast spells he's seen but has no idea how to undo them afterwards.
* When the wizard casts the "Freeze" spell on you, you are now allowed to quit, restart, restore, or even save. You're allowed to use these commands in the Loud Room in Zork I, so I don't see why you shouldn't be allowed to here. Having to wait for the spell to time out is just annoying.
* The robot now only accepts single commands. While handling multiple commands is undeniably cool (and necessary in Enchanter!), in Zork II it opens up an enormous can of worms. You can see the result of actions that should be out of sight, you can turn the Low Room deadly while you're standing in it, yet suffer no harm, ...
* \[Nathan-50] The balloon and bucket do not interact well with the concept of munged rooms, so the rooms where these vehicles move automatically can no longer be munged. (The ledges alongside the volcano shaft can still be, though.) Rather than adding a hard-coded list of non-mungable rooms, I have repurposed the `SACREDBIT` to indicate that a room is safe from munging.
* The Bank of Zork has been cleaned up a bit:
  * You could always throw objects through the curtain and walls, but there was code that suggested that you should also be able to put objects in them. That has been fixed.
  * The north wall in the Safety Depository now acts as the curtain, both for walking through it (already the case) and for putting objects through it (that has been fixed).
  * Putting objects through the wall after going through the curtain didn't work right: It would say the object went through the curtain, the object would stay in the room, and it would turn off the `I-CURTAIN` timer so the curtain door never closed. All of that has been fixed.
  * Trying to put an object through the curtain after it is closed now provides feedback similar to when trying to walk through it.
  * You can't go through the wall or curtain if the room on the other side has been munged.

### Other minor features

* In `WHICH-PRINT`, when printing the name of the `SWORD` object, print "elvish sword" instead of the real object name. This gives a better disambiguation message between it and the nicked swords. This applies to all Zork games, but I only think it's ever noticeable in Zork II.
* It appears someone went through the game and changed a few REMOVE to REMOVE-CAREFULLY. This has the advantage of informing the player if the room goes dark as a result. Unfortunately, in some cases this led to the darkness message being printed before the message describing what happened, or two different darkness messages to be printed. I have fixed the ones I've found:
  * Giving your only light to the Volcano Gnome.
  * Giving your only light to the Wizard.
* Objects could react to "`WAVE WAND AT` *object*", but since the wand handles the default response and `PRSI` gets the first shot, it was not possible for them to respond to "`RUB` *object* `WITH WAND`" even though these commands are supposed to do the same thing. I have introduced a `PRE-RUB` routine that rewrites it to a `WAVE` action. This affects grues and Cerberus.
* \[Nathan-52] WATER-FCN has been adjusted to handle "`TAKE WATER`" more like Zork I. So if there is anything in the teapot, it will now say "`The water slips through your fingers.`" rather than "`The teapot isn't currently empty.`" The old message was particularly confusing if the object inside the teapot was water. "`TAKE WATER`" if the filled teapot was in the room would pick it up without printing any message. Now it tells you that "`It's in the teapot. Perhaps you should take that instead.`" I made the assumption here that Zork I is the better tested game of the two.
* \[Nathan-63] "`PUT REPELLENT ON` *object*" now acts as a synonym for spraying the repellent on the object. "`PUT REPELLENT IN` *object*" still works too, since that's another way of writing "`APPLY REPELLENT TO` *object*". I have added exceptions for if you're trying to put the repellent on a surface or in a container. In those cases it assumes that you do not want to spray the target.
* The balloon has received a number of fixes to make it work more consistently:
  * \[Nathan-78] Much of the custom behavior for the receptacle (and some other bits) were in BALLOON-FCN to be run in the M-BEG case. But that meant they were only run when the player was inside the balloon, not when standing on the ground outside. E:g. it was possible to put more than one object inside it from outside the balloon.
  * "`INFLATE` *object*" now checks that it's the balloon you're trying to inflate before saying that you can't.
  * Only objects that can be put in the receptacle are given DESCBIT to hide them from the standard object lister. (They're printed as part of the room description instead.) For instance, before you could try to put your sword in the *closed* receptacle, and if you later dropped it you wouldn't be able to see it in the room.
  * After you've poured water on a burning object in the receptacle, trying to pick it up now gives a much clearer message explaining why you can't. (When you put objects in the receptacle, their `TAKEBIT` is cleared, but the default message for taking them didn't make sense.)
  * Looking inside the receptacle no longer incorrectly claims that it's empty.
  * "`TAKE` *object* `FROM RECEPTACLE`" no longer claims that the object is an integral part of the balloon.
* \[Nathan-76] When looking through the window in the Dreary Room, check if the Tiny Room is lit and, if not, tell the player that he sees only darkness. Otherwise, the game would tell you that you were likely to be eaten by a grue, and that doesn't make sense since you're in a lit room. This is similar to how the palantirs work.
* \[Nathan-62] Added a custom response for "`SMELL ROSES`", since the default message is grammatically incorrect. The new message is borrowed from Deadline.
* "`PUT WATER IN WATER`" and "`PUT WATER IN ME`" now gives more sensible responses. The first is rejected, and the second is interpreted as drinking the water.
* "`DRINK FROM TEAPOT`" now works, to be consistent with Zork III's "`DRINK FROM VIAL`".
* \[Nathan-69] Breaking the flask now mungs the room, i.e. makes it inaccessible. Opening the flask already did that, and surely breaking it should be even worse.
* The `TUNNEL` object now accepts "smokey" as an adjective in addition to "smoky", since the game itself refers to "smokey" and I'd rather not change any text unless there's a clear typo. A similar object appears in Zork III, but there nothing is ever referred to as "smokey" except "Smokey the Bear". Zork I doesn't have a global `TUNNEL` object, perhaps because a sizable chunk is set above ground.
* Give the unicorn `TOUCHBIT` when you see it. The game would test for it when trying to follow or find the unicorn, but since it would never set it would always respond that "`The unicorn is a mythical beast.`" Now it responds that "`I don't know where it is now.`" instead, once you know it's real.
* It's now possible to put things in the chasm. There was code for it, but it checked if `PRSI` was `PSEUDO-OBJECT`, not `CHASM`. I guess the chasm used to be a pseudo object and was changed to a proper object later. Though it seems it never worked even in the oldest versions of the game.
* Using "Filch" or "Float" on an object now sets its `TOUCHBIT`, so that you won't see potentially misleading `FDESC` descriptions for the object.
* The `FDESC` descriptions of the library books refer to each other, so it's supposed to set their `TOUCHBIT`s whenever that's no longer safe. It did so by intercepting a few commands, most noticeable `TAKE`. But it didn't take automatic taking, burning the books, etc. into account. Monitoring if the books are still present in the `M-END` case of the room's action routine should be more robust.
* \[Nathan-75] In Path Near Stream and Marble Hall, only refer to the whirring sound if the carousel is still spinning. Everything I've read suggests that it's the spinning that creates the sound. You only hear the whirring in the Carousel Room if it's spinning, in Room 8 the whirring is only mentioned in Zork III, and the InvisiClues clearly say that it's a magnetic field that causes the carousel to spin. So once that field is shifted, the whirring should stop. I also changed "`There is rather annoying whirring sound`" to "`There is a rather annoying whirring sound`".
* Repurposed an unused message in `DRAGON-FCN`. What was meant to be printed when the played walked north in the Dragon Room (but wasn't, because the dragon's action routine has nothign to do with that) is now printed in response to "`WALK AROUND DRAGON`" instead.
* Changed the `LDESC` on `SERPENT` to `FDESC`, so that it will be printed. With `LDESC` you just get the default container contents list.
* \[Nathan-34] Changed the `LDESC` on the clear crystal sphere to `FDESC` so that it will be printed once the aquarium is shattered. That means there is no need to clear `LDESC` when picking it up. (Which wasn't foolproof anyway.)
* The gold key now has `TOOLBIT` to be consistent with the iron key. This allows "`UNLOCK DOOR`" to infer the key, if it's the only tool you're carrying.
* The sword demon now gets put on the clock chain early, like in Zork I. Otherwise the Wizard demon runs after the sword demon, causing the sword to not instantly glow when the Wizard appears and hangs around for a bit.
* The blue sphere now starts out on the table instead, like its description suggests. Otherwise the game will describe the table as empty.
* \[Nathan-33] It's only possible to put the mat under the oak door while it's closed. Opening the door dislodges the mat object, same as taking or moving the mat.
* Throwing the flask at the aquarium now mungs the room and removes the flask.
* You can now order the robot to leave the room while you're trapped in the cage. It's not a good idea by any means, but it makes sense that you can do it.
* It's no longer possible to fill the pot with the water in the bucket while you're outside the bucket. You're not allowed to take objects from the bucket while you're outside, and taking the water should arguably be even harder.
* The growing/shrinking mechanics in the Alice area have been slightly updated:
  * It no longer sets/clears `TRYTAKEBIT` on objects, because that may remove the bit from objects that should still have it, e.g. the sword. Inhibiting automatic taking is handled elsewhere.
  * The `NONLANDBIT` is applied recursively to objects in the room, so that objects inside containers also grow or shrink.
  * Eating an enlargened cake no longer removes it, unless it's the exploding one. A mouthful should be enough.
* The sword will now glow in the Topiary, because it's a dangerous place. This is a new feature, but I think it's well within the spirit of the game. Especially now that the sword doesn't glow as often any more.
* \[Nathan-17] A number of commands given to the robot checked if the player was in the same room. That should no longer be necessary (if it ever was), and at least in one case it was harmful because it printed no message at all e.g. if the player was giving the order from the bucket.
* A number of changes have been made to the robot/cage puzzle to improve the illusion that the robot is outside the cage, not inside with you. Most noticeably, his inventory is no longer shown and interactions with him are limited.
* \[Nathan-60] If you have dropped objects while trapped in the cage, they are moved out of the cage along with you when the cage is lifted.
* The Wizard was supposed to respond to requests that he give you something, presumably his wand, but that code was never reached. Now it is, and I've added a couple of alternate ways to ask him to give up his wand that are now all handled the same way.
* \[Nathan-43] If the princess enters the gazebo after it's been munged, the message for why she can and you can't is now tailored to how the gazebo was munged.
* Remove the gnomes if the player dies. Their timed events were already disabled, and it makes no sense that they should just stick around forever.
* Before calling `RANDOM-WALK`, check that there is a random direction to run to. It still won't check if the room on the other side is munged, but I think we can live with that. At the same time, I made a special case for being scared by the Wizard, so that he's referred to as "he" rather than "it".
* \[Nathan-54, Nathan-55] Frying or Filching the dog collar while Cerberus is wearing it is now treated the same as removing it by hand.
* Giving the chest to the demon is now handled the same as giving the iron box to him, i.e. he will look in it for the treasure that was originally there but not for any other treasures. It's a bit strange, but at least it's more consistent now.
* Instead of relying solely on `TRYTAKEBIT` to inhibit automatic taking, we now use `INHIBIT-AUTO-TAKE?` to help with some cases. The basic philosophy is that obects that we know in advance are affected are handled as before, ut cases where any random object could be affected needed the extra assistance:
  * Objects in the Alice area that didn't shrink alongside yourself.
  * Objects inside the bucket, while you yourself are outside.
  * Any objects not in your inventory while you are under the "Float" spell.
  * Any object you have enchanted with the "Float" or "Freeze spell". For the "Float" spell this also applies to the contents of the object.
* A few things in `MAGIC-ACTOR` have been improved, partly because it gets called before pre-actions are applied:
  * When trying to board an object while under the "Fall" spell, check that it's a vehicle.
  * \[Nathan-66] When trying to drop an object while under the "Float" spell, check that it can actually be dropped, e.g. that it's not your hands.
  * \[Nathan-57] When trying to pick up an object while under the "Float" spell, don't just block objects in the room from being taken. Block everything the player isn't holding. Otherwise, you could still pick up stuff from a container on the floor.
* \[Nathan-57] When casting the "Float" spell on an object, move it to the room. Otherwise it could be both inside a container (or your inventory) and floating above your head at the same time.
* \[Nathan-57] When using the "Float" spell on a container, prevent the player from picking up its contents as well as the container itself.
* Some tweaks have been made to the "Fluoresce" spell, so that it doesn't work on everything. (There is no failure message for it. I think it's enough that the wand glows, and then nothing else happens.)
  * It only works on objects that are physically inside the room. I.e. you can't make the floor glow. (Which didn't work anyway.)
  * \[Andrew Plotkin] It only sets `ONBIT`, not `LIGHTBIT`. In other words, it makes objects glow but does not give them a power switch.
  * It can't be used on the matchbook. I don't want to have to deal with a magically glowing object that's also sometimes on fire.
  * When used on the lamp, the lamp demon is turned off and it loses its `LIGHTBIT`, so that you are no longer able to turn it on or off.
* Removed `TRYTAKEBIT` from the fuse. Otherwise, "`LIGHT FUSE`" would take the fuse out of the brick, leaving you with no time to put it back. (Wile E. Coyote would have been proud.)

### Bugfixes

* \[Nathan-58] It's no longer possible to open or close the aquarium.
* \[Nathan-68] Putting the mat under the door now gives it `TRYTAKEBIT`. Having it be automatically picked up messes up the puzzle.
* \[Nathan-45] Looking at the flask through itself now prints one of the `YUKS` messages, rather than a double message about the flask magnifying and distorting.
* \[Nathan-38] When dying, make the match go out. Otherwise, it could become an infinite source of light since `I-MATCH` is disabled in `KILL-INTERRUPTS`. This had already been fixed in Zork I.
* \[Cree-9] Don't allow the bucket to be opened or closed.
* \[Nathan-32, Nathan-71] When opening or closing the lid on the door, check if already is.
* The guarded door has received some bugfixes:
  * The key could be dropped or stolen by the guardian even if you weren't carrying it. I don't know why the game allows you to even try in that case, but I didn't want to change that sort of things.
  * \[Nathan-61] It didn't check what you were trying to use to unlock the door. That meant you had a random chance of just teleporting the key to the room. This wasn't exploitable since you can't pick up the key until you rescue the princess, but still obviously wrong.
* \[Nathan-28] Counting the matches can no longer tell you that you have \-1 matches left. This fix has been copied from Zork I.
* \[Nathan-48] Throwing an object at the robot now moves it to the room.
* \[Nathan-49] "`DESTROY ROBOT WITH SWORD`" now removes the robot, not your hands.
* Don't allow the dragon to escape his area. He already won't follow you into the rooms bordering it, but since `FIND-TARGET` was trying to find the player from `HERE`, not from the dragon's location, he would follow you into the next room if he was still angry.
* Only describe the dragon as blocking the way to the north when he's in his lair.
* \[Nathan-59] Don't allow cutting objects with the wooden club, even though it's a weapon.
* Trying to walk the balloon into a room witout `NONLANDBIT` would still set `BLOC` to that room even though the movement would fail. The next time the balloon moved on its own, it would end up in the wrong room. The normal use case for walking the balloon is as an alternative to typing "`LAND`", and that still works.
* The hole in the Safe Room is now a container. It already acted like one, for the most part, but "`LOOK IN HOLE`" only worked while it was empty. Now that it's a container, we also have to make sure it can't be opened or closed.
* When turning the iron key, only lock/unlock the door if the key is in the keyhole.
* \[Nathan-29, Nathan-70] Doors can now only be locked or unlocked while they're closed.
* Looking through the window in the oak door now omits the description of the door from both directions. That was the intention, but it didn't work right.
* Looking into another room (by palantir or through the window) no longer marks that room as visited. That appears to have been the intention all along, but the code for it was buggy.
* \[Nathan-65] Putting an object in the keyhole no longer causes the object in the other keyhole to disappear.
* Taking the mat from under the door now both dislodges the mat object and picks up the mat.
* Destroying the aquarium no longer makes you drop the object you destroy it with. (Minor point, since doing this is fatal.)
* \[Nathan-79] Removed special case for the bomb from `AQUARIUM-FCN`. All it did was to stop the fuse demon, then print no message. Very strange. Now it bounces harmlessly off the glass. The room will be munged by the explosion, as usual.
* `CAKE-CRUMBLE` now only crumbles the cakes. Before it could crumble any object as long as it had `FOODBIT`, like the candy.
* Restructured `BUCKET-FCN` to make sure that actions are only handled when directed at the bucket. Several of them were in `M-BEG` for no apparent reason.
* Don't make the Gnome of Zurich leave a few moves later, if the wand has already scared him off as soon as he entered.
* When the balloon is about to take off from a ledge, check that it's actually inflated first.
* Allow the empty balloon to take off from a ledge, even if the player isn't there to witness it. (It was checking `HERE`, where it should have checked `BLOC`.) The balloon is light enough to take off even with the receptacle closed.
* \[Nathan-47] Levitating the dog collar now only attempts to levitate Cerberus if he's wearing it.
* The Wizard is now only seen tearing his beard if he's present.
* \[Cree-10] The demon won't taunt the Wizard if you've already set him free before the Wizard arrives. This can actually happen several times, but the manual does say he's bordering on senility.
* `I-WAND` checks if takeable objects are still accessible before printing that they stop glowing. Before you could still see the message e.g. if you tossed the object down a chasm.
* Fixed the menhir description:
  * `DESCRIBE-MENHIR` is called when printing the full description, even if the menhir is no longer there. Otherwise, the passage behind it isn't described. That means it must not print that the menhir is affected by the explosion unless it's still there.
  * The game now uses the same `MENHIR-POSITION` (2) for both when the demon as taken the menhir and for when he's moved it to the Pentagram Room. Before, he used 2 and 3, and 3 is supposed to mean that the menhir is levitating.
  * The case in `DESCRIBE-MENHIR` where it would print "`There is a huge menhir here.`" has been removed. It doesn't correspond to any scenario in the Menhir Room, and when it's in the Pentagram Room it gets printed as a regular object.
  * When the menhir is levitating, the proper description is printed.
* If the menhir is moved from the Menhir Room, you can no longer interact with it from there or from the kennel.
* \[Nathan-16] While floating, if you drop an object the game now checks that you can drop the object.
* \[Nathan-56] Removed `TAKEBIT` from the dead sea serpent. You don't need it for the custom take message, and this way you can't Filch it either.
* \[Nathan-64] `WATER` and `SALTY-WATER` now have `TRYTAKEBIT` so that you can't automatically pick up the water without a container. Note that `SALTY-WATER` isn't actually used in the game. It was in older versions, but apparently never worked correctly.

### Stylistic fixes

* The copyright years in the newspaper have been updated to match the ones in the game banner. This was done in earlier versions when the copyright years changed.
* Some missing newlines have been added:
  * When attacking the princess.
  * The first time your compass starts spinning.
  * When the Gnome of Zurich escorts you to the Bank entrance.
  * When dying permanentaly, before `FINISH` is called.
  * When looking through the window in the oak door. Usually you wouldn't notice it was missing because you'd see the blue sphere on the table and that just looked like part of the room description. But it was very noticeable if the sphere had been removed.
  * When solving the baseball maze.
  * When the Wizard taunts you in the baseball maze.
* Some unnecessary newlines have been removed:
  * When someone other than the player dies, e.g. the robot. There is no perfect solution here, because if a message is printed right after that it would be nice with an extra newline.
  * When reading the Wizard's warning label.
  * When reading the safe card.
  * The signs in the Bank of Zork.
  * When winning the game, before `FINISH` is called.
* Removed a stray space when being floated out of a vehicle.
* Removed leading space from the balloon label and safe card texts. Judging by Confusion, these were blank lines in Mainframe Zork. We don't want that any more, and ZIL rendered it as a single space anyway.
* Lighting the fuse now prints "(with the match)" when appropriate. This is consistent with lighting the candles in Zork I, even though the implementation is a bit different.
* Some fixes for the matchbook have been brought over from Zork I:
  * Lighting a match in a dark room now prints the room description.
  * Blowing out a match now tells you if the room goes dark.
  * When a light goes out, you are now told if the room went dark.
  * Pouring water on a burning match now stops its demon.
* The player is now informed if certain actions made the room go dark:
  * When breaking the lamp.
  * When the lizard eats an object.
  * When you put an object in the well.
  * When using the "Fry" spell on your ligth source.

# r63

This version was never publicly released, but I have tried to understand what changes were made compared to the r48 sources.

## Gameplay changes

* The response to "`TAKE SAFE`" now properly checks that you're not doing "`TAKE` *object* `FROM SAFE`".
* Things like "`GNOME, HELLO`" would print a double message. (The same fix has been applied to both gnomes.)
* The code for pushing the square button has been modified, but shouldn't have any functional changes.
* Several (but not all) cases where talking to the robot while in anything else than `HERE` (e.g. while in the bucket) would print no message hav been fixed.
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
