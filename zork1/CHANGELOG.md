# Zork I

# r200

This version is one I'm working on, trying to fix as many of the known bugs as I reasonably can.

### Restoration of old behavior

* The message "`You notice something funny about the feel of the buoy.`" was not printed when you picked it up. This is done in the `M-END` case of `RIVR4-ROOM`. But `M-END` is handled by `<LOC ,WINNER>` rather than `HERE`, so it should be handled by the boat rather than the room. That it used to work hints at some change in how vehicles are handled.
* Allowed `ROPE-FUNCTION` to run its custom actions outside of Dome Room. Otherwise "`UNTIE ROPE`" would tell you that the rope couldn't be tied, and "`TIE UP` *object* `WITH ROPE`" wouldn't work at all. The "`TIE UP`" command used to work in earlier versions, but I think it may have been handled by `V-TIE-UP` there instead of `ROPE-F`.
* Burning the leaves did not work as expected. The original behavior was that it was only safe to burn them when they were on the ground. But since they were removed before that check was made, that never happened. But that introduced another problem, where you would die if you put the leaves in a container and burned them there, even if you weren't holding the container. So now it checks that you're holding them before killing you.
* When oiling the bolt, check if you were trying to use the glue before printing the glue response. This appears to be a regression in r75.
* When you enter the Dome Room as a ghost, and are drawn over the railing, you now get the room description for the Torch Room. The code here would move the player object and set `HERE`, rather than using `GOTO`. I'm guessing that in older versions, using `GOTO` would print the room description twice, and when that was fixed the room description was suppressed completely.
* `OTVAL-FROB` is the method used to calculate the value of the treasures in the trophy case. It traverses the contents recursively. However, it only counts the value of the top level objects. That's different from how Mainframe Zork worked, so I have fixed it. On a suggestion from Andrew Plotkin, there is an exception for the egg. You only get points for whatever is inside if the egg is open.
* The leak in the Maintenance Room would kill you when the water was up to your neck. Now it doesn't happen until it's high in your lungs, like in Mainframe Zork. This means it takes a little longer to drown.
* \[Nathan-71] When climbing the tree, always print the objects on the ground below. The old behavior, carried over from Mainframe Zork, was to check if there were two objects below. I think the tree itself was one of them, but that's no longer the case in Zork I.
* The thief once again picks up objects dropped in the maze, and taunts you about it. At some point, the game started clearing `TOUCHBIT` from maze rooms so that you couldn't tell if you had already visited them just by their description in brief mode. But that also prevented the thief from robbing them.
* Fixed "`SWIM`" printing garbage near water. (Also happened in Zork II.)
* In Mainframe Zork, it was possible to sail the boat downstream at will \- in fact, it was the only way to sail the river \- while Zork I only allowed you to go with the flow. Now you can do either. This is more consistent with the instructions on the boat label, I think.
* Turning into a ghost while at the Entrance to Hades will now print the room description, as before. I think this was a regression introduced when fixing a case where the room description would be printed twice. (The only way I ever found to trigger that was entering the Bat Room without a light and without garlic, and getting killed when the bat dumped you in another room.)
* \[Monica Morse] In Mainframe Zork, the second parameter to `PROB` was originally supposed to be the probability when your luck is bad. That was changed in Zork I at some point so that when it was `T`, it was a N in 300 chance instead of a N in 100 chance. That made some things that were supposed to be more likely to happen when your luck was bad less likely. Now it uses the N in 300 chance only when the second parameter is `T`, otherwise it uses the old way.

### Changes to game mechanics

* \[Cree-27] Deflating or puncturing the magic boat would make it act as a "bag of holding". All the objects inside would remain in the inflated boat, while you were carrying around the deflated or punctured version. Now the `SIZE` of the deflated or punctured boat is updated to be the same as the total `SIZE` of the inflated boat. The deflated boat now starts out as `SIZE` 22 rather than 20, to include the weight of the label inside.
* \[Nathan-48] When destroying the painting, its `VALUE` is now set to 0\. That means that if you do it before picking it up, you won't get any points for it. (You still keep your points if you destroy it later.)
* If you carry the punctured or deflated boat into the Gas Room, the game will now check if there is a flaming object in the inflated boat. Not that it makes much sense that the object would still be burning inside the folds of plastic, but any player who tries this is clearly trying to break the game and deserves to be punished for it!

### Other minor features

* `RUB` is now handled the same way as `TAKE` as a ghost, i.e. your hand will pass through it. This change was made in r119, but had no effect since `RUB` was still intercepted before it could reach that line.
* You now only get the message about something strange about the feel of the buoy if you haven't already seen the emerald inside it.
* The command "`LAUNCH BOAT`" while not inside now prints "`You're not in the boat!`" The message was always there, but was incorrectly handled in the `M-BEG` case, where it was never reached because the `LAUNCH` action was already handled.
* There are several times when you may lose your only light source, either temporarily or permanently. At these points the game should tell you that the room goes dark, and in a lot of cases it does. Other had to be fixed:
  * When putting things in the river, the game would tell you the room went dark before telling you the object is gone. The order has been fixed.
  * When raising the basket from below. It was already done for lowering the basket from above.
  * Closing the lid in the Machine Room.
  * When a match burns out.
  * Turning off the lights in the Maintenance Room.
  * Putting objects in the slide.
* \[Nathan-65] "`PUT WATER IN WATER`" and "`PUT WATER IN ME`" now give more sensible responses. The first is rejected, and the second is interpreted as drinking the water.
* In r88, the message when you try to pick up the rusty knife while carrying the sword was changed from "`As you pick up...`" to "`As you touch...`". Probably to deal with the case where you were already carrying too much to be able to pick it up. So now it will also react to "`TOUCH RUSTY KNIFE`".
* "`DRINK FROM BOTTLE`" now works, to be consistent with Zork III's "`DRINK FROM VIAL`".
* \[Nathan-67] When turning the coal into diamond, the game would only check if the coal was inside the machine, and any other object would be left untouched. Now the diamond is only created if the coal is the *only* object inside the machine. My reasoning is that other objects introduce impurities that will prevent the diamond from forming. You can still use the machine to destroy the diamond, because that's just how powerful it is.
* Made it easier to brush your teeth with the glue. Before, unless you were holding the glue in your hands, it would imply that you couldn't do it at all. Now it's enough that you're carrying the glue, and the failure message if you don't is clearer.
* "`WALK AROUND HOUSE`" now works inside the house as well. Most of the code for it was already in place, but the house wasn't in scope inside the house. I had to add a special case for the Kitchen, since there are two possible rooms to go to from there.
* Water can no longer be poured out of a closed bottle. But that transparent bottle is not always well handled.
* Throwing objects at the sleeping cyclops now wakes him up. Otherwise you would get the default message, which is that he ducks out of the way.
* When killing the thief in his lair, don't count his stiletto as a treasure. It isn't when you kill him outside of his lair (see `DEPOSIT-BOOTY`) so I think it makes sense to not do it here either.
* The ZORK owner's manual now has BURNBIT, since it's made of paper.
* Burning the book by touching it to the red-hot bell is now just as deadly as burning the book in any other way.
* The bat now checks every move to see if the garlic is present. Not only does that seem to eliminate the need for a hack to prevent double room descriptions if the bat dropped you off at the Entrance to Hades (presumably only if you were eaten by a grue as a result of the drop), it means that you no longer get the discrepancy where eating the garlic no longer causes the bat to be described as swooping down at you, while leaving you alone. Garlic breath alone isn't enough to keep it at bay.
* The bat no longer swoops down at you if you're a ghost.

### Bugfixes

* The basket is implemented as two objects: One in the room with you, and one on the other end of the chain. These are swapped when you raise or lower the basket, and "it" is changed to refer to the correct one. But this only worked from the top of the well. Now it works from the bottom as well.
* \[Nathan-74] Throwing the nasty knife at the thief now wakes him up if he's unconscious. He will then accept it as a gift, just as if you threw it at him during a fight. It's only when you're not fighting him that he will refuse to take it, or even flee.
* In some circumstances the game was using `ROB` to move all objects out of `ON-RAINBOW` or `INFLATED-BOAT`. In most games, `ROB` does move all objects so this would be fine. In Zork I, ROB is only intended for moving valuables so it ignores most objects. I've added a new `MOVE-CONTENTS` routine to use instead.
* The sword is a treasure while it's glowing, which is why the thief can steal it from you. That means it's worth points if you manage to put it in the trophy case while it's still glowing. And it turns out that you can, because if you put the sword in a container (the boat is the easiest) that will "freeze" its value until you pick it up. And you can put an object in the trophy case without taking it out of its container. This meant that you could get 352 points out of 350\. But that breaks the game, because the endgame doesn't happen unless you hit *exactly* 350 points. So now `OTVAL-FROB` makes an exception for the sword when calculating your score. (A similar bug was already fixed in the unreleased version of Zork II.)
* If you destroy a treasure inside the trophy case (the painting may be the only possible one), your score is now immediately updated. Before it was only recalculated on `TAKE` or `PUT`, but now it's also done on `MUNG`.
* When destroying the painting, check if it has already been destroyed.
* \[Nathan-50] Don't allow the bird's nest to be opened or closed.
* \[Nathan-59] It was possible to get both the working and the broken clockwork canary by dropping the nest, with the egg inside, from the tree. That's now handled by calling `BAD-EGG` like in the other places. That means the message you get will be different, but I think it's consistent. It was not possible to exploit this for points, because you lose more points from breaking the egg than you gain for the broken canary.
* \[Nathan-72] Going up from the Torch Room now checks if there is a rope before saying you cannot reach it. Otherwise, it will say that you can't go that way instead.
* When inflating the boat, check that the label is still inside before saying there is a label inside. Before, it would only check the labels `TOUCHBIT` so you'd still see the message if you had burned the label or cut it to pieces.
* \[Nathan-62] Dropping the bird's nest with the egg inside while climbing the tree would only move the broken egg to the ground below. Now the nest is moved as well.
* If you had knocked out the troll and dropped his axe in a nearby room, it was possible for the troll to pick it up when the player entered that room. That's apparently because the routine for it is called at least once after leaving the Troll Room, and it only checked if the axe was in your current location, not the troll's. Now it checks that the troll's and the axe's location are the same. I guess someone must have spotted this phenomenon because you only see the message about the troll picking up the axe if the troll is present. But they didn't consider the implications. (And frankly, neither did I. This comes from Nathan's list of Infocom bugs.)
* \[Nathan-57] Give the inflated boat `TRYTAKEBIT`. Because allowing the boat to be automatically picked up while you are inside it causes the game to hang!
* Always disable I-CYCLOPS when you're not in the Cyclops Room. Before it would be left running while the cyclops is asleep. Since the demon is restarted on entering, if he's enraged, it should still work just the same way as before.
* When entering the thief's lair, only say that his treasures vanish if anything actually vanishes. The game already tried to do this, but it did so by checking if there were at least two objects in the room so it was almost always wrong. Now it explicitly checks for any object in the room that isn't the thief, the chalice, or the player.
* \[Nathan-73] When you kill the thief in his lair, only declare the chalice safe to take if the chalice is still there. (You could have already picked the chalice up while the thief was unconscious.)
* Check that rope is `PRSO` when dropping it in the Dome Room. Otherwise, nonsensical commands like "`POUR SWORD FROM ROPE`" would still drop the rope down to the room below.
* Pouring water on the burning candles or match now stop their respective demons. Otherwise they would still burn out eventually, even though extinguished. This also gives the candles `TOUCHBIT` so that they won't be described as burning on the altar if you haven't yet picked them up. This wasn't as important for the match, since they burn out on the next turn anyway. But doing it will allow me to print a consistent message if the room goes dark.
* \[Nathan-70] When locking or unlocking the grate, the game will now check if it's already locked or unlocked, and you can only lock the grate when it's closed. You can also unlock lock the gate with the key, not with any random object.
* \[Nathan-69] In a few cases, the grate would check if you were in `CLEARING`. That was wrong, because the grate is in `GRATING-CLEARING`. You could see this if opening the grate from above, because it would tell you that there were trees above rather than darkness below.
* \[Nathan-75] Putting an object in the grate from below would tell you that it fell into darkness. Now that only happens if you do it from above.
* When putting objects in the grate, it will now only check their size if the grate is closed. When it's open, anything will fall through. After all, if you open the grate from below when the leaves haven't been moved, the leaves will fall through without problem.
* Fixed double messages when talking to the bat or the spirits. It would print both the custom message and the default message.
* Rubbing the red-hot bell without specifying with what now assumes that you're using your hands. Otherwise, it would try to test for `BURNBIT` on object 0\.
* \[Nathan-53] When pouring something on the red-hot bell, check that it's actually the water. Otherwise you could use it to remove just about anything in scope, including grues, your hands, etc.
* When pouring something on the torch, check that it's the water before saying that the water evaporates.
* When the reservoir drains, only make the trunk visible if it's still there. I don't know if this could be exploited, but it seemed safer in case the thief had it.
* Digging in the sand would reveal the scarab if it was invisible, even if it wasn't there any more (e.g. the thief has it). Also, digging in the sand when the scarab wasn't invisible (after being resurrected) would print the default message on the turn the scarab would have been revealed. Now it just collapses the hole one move earlier instead.
* When waving the sceptre at the rainbow, only say the pot is there if it's still there and invisible. Waving the sceptre a second time doesn't make the opt go away. (That would have been a possible fix, but seemed more invasive.)
* Jumping in the Dome Room now prints the intended message. The old behavior was still to kill you, but the custom message for it was in the `M-ENTER` case rather than `M-BEG`, so it was never shown. I've fixed the check a bit so that you don't die by jumping over any other objects in the room. This fix is somewhat based on `CANYON-VIEW-F`.
* Crumble the rusty tool chests when trying to close them, not when trying to open them, since they're already open. (And they're presumably meant to be, since you can tell they're empty.)
* You can no longer put objects in the slide from the Cellar, since the message you got made no sense. (In Mainframe Zork the slide wasn't accessible from the cellar anyway.)
* \[Nathan-63] Fixed closing the trap door from the cellar. Before, you could only close it if it was already closed.
* "`CLIMB DOWN STAIRS`" printed the wrong message in the Kitchen. There was a custom message for it, but it wasn't triggered.
* When destroying the painting, set its `TOUCHBIT` so that it doesn't print the wrong description if you haven't yet picked it up.
* Fixed a double message when typing "`WALK AROUND FOREST`" near the house.
* Throwing water overboard now prints a more appropriate message.
* When examining yourself near the mirror, check if the mirror is broken.
* When giving the axe to the troll, and the axe was in a carried container, it would tell you that it was unlikely that you would be able to get the axe. Now it uses `HELD?` instead of `IN?` though in all likelihood this check is no longer needed. Older versions apparently allowed you to give away objects you weren't carrying, but current versions don't.
* When listening to the troll, only mention what he's saying if he's conscious. Otherwise, use the default message that he makes no sound. I was tempted to suppress the response completely if the troll is unarmed, but it isn't guaranteed that he will babble and plead for his life. He may recover his axe instead.
* When listening to the thief, check if he's unconscious.
* Don't allow the boat to be launched from `RIVER-5`. Previously this would take you to the sandy beach (not the room you would get to by landing the boat), because `RBOAT-FUNCTION` didn't consider `RIVER-5` to be in the water.
* Dropping or putting a weapon in the boat while you are inside now moves the weapon to the room, along with the rest of the contents of the boat.
* Entering the Bat Room without garlic now honors brief and superbrief mode. Before it would always print the full room description.
* In the unlikely event that entering the thief's lair causes him to hide the last light source, update `LIT` on entry.

### Stylistic fixes

* If a message is printed before entering a new room, it's customary to put a newline before the room description. Earlier versions of Zork did not do this, perhaps to cater to smaller screens, and it was not fixed for all cases. I've fixed it in a few places:
  * When `I-RFILL` causes you to flee from the Loud Room.
  * When lighting a match in a dark room. (This was already done when lighting the lamp in a dark room.)
  * When going down the slide.
* Added a missing line break when trying to tie up an actor with the rope.
* Added missing newline before `FINISH` when winning the game. Strangely enough, this was already done for the case where the Tandy bit was set.
* Added missing newline before `FINISH` when dying permanently.
* If you reveal the grate by burning the leaves, the message now states that the leaves are gone, not moved.
* Replaced tabs with spaces in the text for the guidebook and the tube. At least the DOS version would print a garbage character instead of the TAB when I tried it in DOSBox.
* Removed unnecessary blank line from the matchbook description.

# r119

This version was never publicly released, but I have tried to understand what changes were made compared to the r88 sources.

## Gameplay changes

* Responses have been added for `RAISE` and `LOWER` to `GRANITE-WALL-F`.
* "`WALK AROUND FOREST`" has been implemented. As a consequence, `FOREST` has been added as a global to `WEST-OF-HOUSE`, `NORTH-OF-HOUSE`, `SOUTH-OF-HOUSE`, and `EAST-OF-HOUSE`.
* `WATER-F` now treats the `BOARD` action as swimming.
* "`PUT` *object* `IN WATER`" is now treated as putting it in the river, if the river is present.
* "`DROP WATER`" now checks if the bottle is open.
* An unnecessary blank line has been removed before "`The bat grabs you by the scruff of your neck and lifts you away....`"
* After the bat drops you, the game only prints the room description if you're *not* in `ENTRANCE-TO-HADES`. This is to prevent the room description from being printed twice if being dropped by the bat turns you into a ghost. (This can happen if you walk into the Bat Room without a light, but I don't know if that's the only way.)
* Winning the game now calls `FINISH`, not `V-QUIT`, so that you get the option to play again. This would be beneficial for the digital releases, since they clear the screen when the game ends. (I suggested to GOG that they should add a `pause` to their BAT file, but they claimed this would somehow be less user-friendly than robbing the player of their victory message.)
* Fixed double message when trying to order the troll around. This was caused by `TROLL-FCN` accidentally returning false.
* The unused global variables `LEAVES-GONE` and `EGYPT-FLAG` have been removed.
* BOLT-F now checks if there is a `PRSI` when turning it. This could prevent the game from printing garbage, but I don't see how there couldn't be any `PRSI`. And on the other hand, there was such a `PRSI` check in `MSWITCH-FUNCTION` that has now been removed.
* Lots of string tables have been made `PURE`, that is to say they now reside in read-only memory.
* The Loud Room behaves slightly better in superbrief mode. The fake command prompt doesn't have a blank line before it, and there is no blank line before the room description after typing "`ECHO`".
* A special message for if the player has somehow managed to get the thief's stiletto while he's still alive has been removed. I guess this was added as an insurance against a bug that has since been fixed? For reference, the message was "`You feel a light finger-touch, and turning, notice a grinning figure holding a large bag in one hand and a stiletto in the other.`"
* Similarly, a special message for the player giving the stiletto to the thief has been removed: "`The thief takes his stiletto and salutes you with a small nod of his head.`"
* Possibly related to this, if you scare the thief off by throwing the nasty knife at him, the game makes sure to remove the stiletto from his inventory. Also, throwing the knife at the thief now moves it to the room.
* The message about the thief passing through has been adjusted a bit. Before, it would always say that he "`quietly abstracted some valuables from the room and from your possessions`" even though he only robs the room *or* the player, not both. A message about the thief leaving has been similarly adjusted so that it can either say "`robbed you blind`" or "`appropriated the valuables in the room`". (That message appears twice, and had already been fixed in one of the cases.)
* Some misleading indentation in `TREASURE-ROOM-FCN` has been fixed, but I think the new code is still functionally equivalent.
* When entering the thief's lair, the check for which objects to turn invisible has been rewritten but I think it's still functionally equivalent.
* Blowing out the candles now set their `TOUCHBIT`. This is so that they won't still show up as burning in the room description if you didn't take them first.
* `MACHINE-F` now checks that the machine is `PRSO` when handling `TAKE`, presumably to allow "`TAKE` *object* `FROM MACHINE`" to work.
* When waving the sceptre a second time to turn the rainbow back to normal, the game no longer prints "`Waving the sceptre has no effect.`" at the end.
* Some hyphenations have been removed: "mid-air" is now "midair", "non-descript" is "nondescript", "super-brief" is "superbrief"
* When Up a Tree, "`CLIMB DOWN`" now works without printing "`(down the tree)`". A similar fix has been added to `ROPE-FUNCTION`, but I don't see how it could ever work there. In fact, "`CLIMB DOWN`" in the Dome Room now prints "`Thedoesn't lead downward.`"
* `DEAD-FUNCTION` now tries to handle `RUB` (e.g. touching) an object the same as `TAKE`, but it doesn't work since `RUB` is already handled earlier.
* Some lines in the combat code that appear to not do anything have been commented out.
* Killing an unconscious monster now says it's unconscious, rather than unarmed.
* The second parameter to `ROB` has been renamed from `THIEF` to `WHERE`. I guess the idea was that sometimes it was used to transfer things to rooms, not beings.
* The diagnosis message "`You can strong enough to take several wounds..`" has been fixed to "`You can survive several wounds.`"
* `JIGS-UP` now always sets `WINNER` to be the `ADVENTURER`. I don't know when this wasn't already the case. It also always ends with `RFATAL`, but I'm not sure what difference that makes either. I thought it might be something about dying in the middle of several commands being issued at once, but clearing `P-CONT` should already take care of that.
* The size of the `SANDWICH-BAG` ("brown sack") has been increased from 3 to 9, and its capacity has been reduced from 15 to 9\. This may have been to prevent it from being used to sneak light sources into the drafty room.
* The size of the `GARLIC` is now 4\. I'm not sure if it was weightless before, or if it had a default size.
* `BARROW-DOOR` now has `OPENBIT` so that it won't ever be described as closed.
* An unnecessary line break has been removed from the text of the `ADVERTISEMENT`.
* An unnecessary line break has been removed from the text of the `MATCH`.
* An unnecessary line break has been removed from the text of the `BOAT-LABEL`.
* Unnecessary line breaks have been removed from the text of the `OWNERS-MANUAL`.
* A tab has been removed from the text of the `BOAT-LABEL`. Though this probably removed some of the intended indentation.
* A paragraph from Mainframe Zork has been re-added to the text of the `GUIDE` with minor modifications.
* The `SAILOR` has some new responses:
  * "`SAILOR, HELLO`" now prints "`You can't talk to the sailor that way.`"
  * "`EXAMINE SAILOR`" now prints "`There is no sailor to be seen.`"
  * "`THROW ME AT` *object*" now responds with "`Why don't you just walk like normal people.`"
* `PATHOBJ` has changed its name from "way" to "passage", and lost the synonyms `PASSAGE` and `CRAWLWAY`.

