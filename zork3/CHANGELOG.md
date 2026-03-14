# Zork III

# r200

This version is one I'm working on, trying to fix as many of the known bugs as I reasonably can.

### Restoration of old behavior

* Allow "`ENTER SEAT`" to work again to enter the gold machine. I think this had something to do with the seat being a vehicle in earlier versions. It no longer is. The fix was simply to handle the `THROUGH` action the same as `BOARD` and `CLIMB-ON` in `TM-SEAT`.
* Any time `<PICK-ONE ,YUKS>` was called, the game would crash. This was, of course, a regression in the previous version.
* The vial once again responds to attempts at filling it. There is no water in Zork III, and trying to fill it with anything else translates into a `PUT` action, not a `FILL` action.
* Some text in `JIGS-UP` describing the dungeon master has been reverted to the release version. While the new text was consistent with how he's described later, the vagueness of the original text was probably intentional. It's even quoted in the InvisiClues.
* Don't allow the burned-out lantern to be lit again. This was apparently a regression in the unreleased r25, when `LIGHT-INT` was replaced with a newer version. The `LANTERN` routine is now much more like the ones in Zork I and II.
* The `GUARDIAN` routine didn't handle actions properly. This appears to be a regression from r25, caused by the value of `M-END` changing.

### Changes to game mechanics

* \[Cree-8] It's no longer possible to enter the Royal Puzzle from the side room if that part of the puzzle is blocked.
* The shadowy figure is no longer able to block an exit if he's wounded enough. The game already described him as "`probably not capable of hindering your movement`", but previously he would block you anyway.
* The sword demon is now started when the sword appears in your hand. Before, it wasn't started until you picked up the sword which meant that you could play through a large chunk of the game without ever seeing the sword glow. In particular, it's supposed to glow if the man hasn't appeared yet.

### Other minor features

* Throwing the lamp or the staff off the cliff already replaced them with broken versions of themselves. That now also happens when throwing them off the ledge, or pushing a sandstone wall on top of them.
* Pushing a sandstone wall on top of an object bizarrely moved that object to the side room of the Royal Puzzle. This included the slot in the door, so if you put the book in the slot from there you would see an entirely fictitious passage open up to the west. The object was returned to the puzzle again when pushing the sandstone off it, but why move it in the first place\?
* Clear `TOUCHBIT` from `CP` when entering the Royal Puzzle so that you always get the full room description. This was already done (although implemented in a different way) when exiting the puzzle through the side entrance. Now it covers all cases. Even if you leave the puzzle by dying.
* The Royal Museum pedestal had some bugs:
  * The capacity has been increased from 50 to 55 so that it's large enough to actually hold all of the Royal Jewels.
  * \[Cree-9] The pedestal is accessible in the old Jewel Room. There is no cage around it this year, so the old message was wrong. This means that it's now possible to pick up one of the jewels and then put it back.
  * When putting an object on the pedestal, only set its `NDESCBIT` if you are in the old jewel room. If you can access the pedestal elsewhere, the standard object lister should handle it instead.
  * React to `PUT-ON` when putting the Royal Jewels on the pedestal. Previously only `PUT` was handled.
  * React the same way to `LOOK-ON` and `LOOK-INSIDE`. Before it would claim the pedestal was empty for one of them, since the jewels have `NDESCBIT`.
* Travelling in time no longer advances the move count by 2\.
* Call `FINISH` instead of `QUIT` when dying permanently, so that the player can restart or restore. This already happened in some cases, but now it also covers dying too many times or dying by a standard `JIGS-UP` (rather than `REALLY-DEAD`) during time travel.
* \[Cree-10] The pile of coal in Zork I is now a proper object, just so that the game will recognize the synonyms for it. There's probably no way you can interact with it.
* \[Nathan-17] `CHEST-LIFTED` is now set when tying the rope to the chest as instructed. Before it was only set if the rope was already tied when the man appeared. The game checks this flag when trying to talk to the man, so even after he returns it might tell you to tie the rope.
* \[Nathan-16] If you tried to grab the rope before tying it to the chest, the man would point to "`the chest near you on the ledge`", even if you were carrying the chest or had thrown it off the ledge. Now it will say "`the chest in your arms`" if you are carrying it, and if the chest disappears entirely he will leave early.
* `TIME-MACHINE-F` now only handles the `MOVE` action for itself, not for every object while you are inside it.
* The check for if you are trying to manipulate objects outside the time machine from inside it has been made a bit more strict, and you can no longer automatically pick up such objects.
* The jewelled knife now has `WEAPONBIT`.
* Tying the rope to the chest and leaving before the man arrives now has him steal the chest off-screen. You'll find the empty chest at the top of the cliff. Before, the rope would be gone instead which didn't make much sense. Since this was caused by a previously unused `CLIFF-BASE-F` routine, I have no qualms about changing the behavior like this.
* When tying the rope to the chest, check if it's already tied.
* The check to see if you die while holding the key in the area beyond the lake has been made a bit stricter. Now you can no longer trick it by putting the key in the chest.
* If you die while holding the chest, the game now makes sure the chest is untied.
* Enabled a message for climing up the rope from the ledge that was never reached.
* The fixes to the can of repellent made in Zork II (being able to put it on a surface or in a container without spraying it) now also apply to Zork III.
* The sword now glows brightly when the man at the cliff is in the same room as you. Before, it would never glow more than dimly for him.
* The key now has `TOOLBIT`, like all other keys in Zork. This allows "`UNLOCK DOOR`" to infer that you want to use the key.
* The flaming pit is now a bit more consistent about when it's within reach and when it isn't.

### Bugfixes

* Stop the game from reacting to "`PUT` object `IN HOOD`" or "`PUT` object `IN VALUABLES`". I'm not sure why these objects handle the PUT action at all. They may have been needed at some point to keep the player from stealing them, but I don't think that happens any more. Still, let's keep it just to be safe.
* \[Nathan-12] Increased the size of `CELLOBJS` and `CPOBJS`. These tables are used to allow a single room to masquerade as several. They store which objects are in each such fake room, but they only allowed for eight objects in each room. If you dropped more than that, they would spill over to the next room. By my count, there are 16 objects that could coexist. If we still need to change it, there is now a `MAXOBJS` constant for it, instead of a hard-coded value.
* It's no longer possible to throw the chest off the ledge if the rope is tied to it.
* The book now has `BURNBIT`. You could already burn it when you weren't holding it. Now you can while holding it as well.
* \[Nathan-13] Throwing the potion now works the same as dropping it, i.e. it's removed rather than moved to the ground.
* The valuables from the chest now have `TRYTAKEBIT` so that you can't automatically pick them up.
* \[Nathan-19] Drinking from the vial or opening it now checks if it's open or not.
* When examining the man, no longer refer to the chest as "now-open" if it's closed. Instead it's "now-closed".
* There was a custom message for picking up the sand underwater that was never reached. Now it is.
* There was a custom message in `T-BAR-F` for turning the T-bar. Give `T-BAR` the `TURNBIT` so that it can be reached.
* When waking the old man, check that he isn't already awake.
* \[Nathan-18] When moving the manhole cover, check if it has already been moved.
* Fixed some corner cases when interacting with the sailor. It was possible to use "`HELLO SAILOR`" from another room, and it could say that nothing happens yet even after the thing just happened.
* Shaking an open container underwater no longer incorrectly says the contents disappear.
* Check if the chest is open before declaring that the torch, lamp or staff don't fit.
* Print a better message for "`LOOK UNDER SURFACE`" while underwater.
* "`BREAK BEAM WITH BEAM`" no longer says the beam is already breaking the beam.
* The compass rose now has `TURNBIT` to make it easier to trigger the message saying why you can't turn or move it.
* There is a custom message for "`FOLLOW ROBOT`", but the robot was only visible from the old museum, not the one where you actually encounter it. The robot is now visible from all versions of the museum (once you've seen it), and "`FOLLOW ROBOT`" no longer says the door is closed unless it really is.
* The condition for when the game things there are guards present in the Royal Museum has been cleaned up a bit. Before, if you knocket on doors before using the time machine you would be told not to draw attention to yourself.
* You can no longer throw things like your own hands at the Guardians of Zork.
* Throwing yourself at the Guardians of Zork now make them kill you. Before, it only said that they did.
* Attacking the Guardians of Zork now checks if they're close enough, before telling you they're not.
* Telling the dungeon master to kill you no longer kills the dungeon master, only to have him immediately come back to life if he was following you.
* `BRONZE-DOOR-EXIT` only worked in one direction: from the south corridor to the cell. It also didn't check if the door was actually there, only that it was open. And it didn't set the "it" object if you tried to walk through the closed door. Now everything should work as expected, if you jump through the unlikely hoops required for this situation to arise in the first place.
* If you get yourself killed in the endgame, put back the dungeon master at the door. Otherwise, he will not be there when he lets you in another time.

### Stylistic fixes

* The game is supposed to print a blank line before the room description if there is a message before moving you to another room. I added a few of those, and some other missing ones:
  * When pushing a wall in the Royal Puzzle.
  * When being forced back to the surface of the lake.
  * When jumping over the beam.
  * When the dungeon master pushes the button while you're in the cell.
  * When spinning the dial at the parapet.
* An unused `CPSOLVE-FLAG` has been removed. It was set when climbing up the ladder to exit the puzzle, but not otherwise as far as I could tell. And nothing looked at the value of it.
* Removed unnecessary linebreak from JIGS-UP.
* Removed unnecessary line break when using the `FROTZ OZMOO` spell.
* A few typos have been fixed:
  * nonexistance \-\> nonexistence
  * possession \-\> possession
  * inaccessable \-\> inaccessible
  * disappears \-\> disappear
* The "You have died" message has been made more consistent when dying in the past.

# r25

This version was never publicly released, but I have tried to understand what changes were made compared to the r17 sources.

## Text changes

Unlike the other two Zorks, Zork III has seen a significant number of changes to the text. I'm not the person to judge which ones of these are to improve the text, and which were just made to save space. I have no doubt missed some.

I have made a few notes where I think the new text has issues.

| Old text | New text |
| :---- | :---- |
| `The sword is deeply imbedded within the rock. You can't even begin to budge it.` | `The sword is deeply imbedded in the rock. You can't budge it.` |
| `The lamp has smashed into the floor, and the light has gone out.` | `The lamp smashes. The light is now out.` |
| `You'd better have more light than from the` *object*. | `I hope you have more light than from the` *object*. |
| `This is a small square room, in the middle of which is a perfectly round hole` | `This is a small square room, in the middle of which is a round hole` |
| `The place under the hole is dark` | `The area under the hole is dark` |
| `I can't see any ladder here.` | `You can't see any ladder here.` |
| `I can't see any steel door here.` | `You can't see any steel door here.` |
| `I can't see any wooden wall here.` | `You can't see any wooden wall here.` |
| `I can't see any mirror here.` | `You can't see any mirror here.` |
| `I can't see any panel here.` | `You can't see any panel here` |
| `The book drops easily into the slot and vanishes` | `The book drops into the slot and vanishes` |
| `as a previously unseen sign flashes` | `and a sign flashes` (Note that if you put anything else than the book into the slot, the sign is still described as "previously unseen".) |
| `and spews the` *object* `(now atomized)` | `and spews out the` *object* `(now atomized)` |
| `a flight of steps lead up toward the north` | `a flight of steps lead up to the north` |
| `heavily armored warriors standing at ease, hands clasped around formidable bludgeons` | `heavily armored warriors clasping formidable bludgeons` |
| `utterly destroy you with their stone bludgeons` | `pulverize you with their bludgeons` |
| `The` *object* `flies within sight of the guardians` | `The object falls in front of the Guardians` |
| `who, in perfect unison, destroy it utterly` | `who destroy it in perfect unison` |
| `You aren't close enough to fight them and even if you were, the contest would be a bit one-sided` | `You aren't close enough, and even if you were, the fight would be a bit one-sided` |
| `I don't see a way to open the mirror here` | `You don't see a way to open the mirror here` |
| `I don't see a way to open the panel here` | `You don't see a way to open the panel here` |
| `The mirror feels fragile` | `It feels fragile` |
| `but seems rather fragile` | `but seems fragile` |
| `The beam is stopped halfway across the room` | `The beam is blocked` |
| `Suddenly, two identical stone hands holding tremendous bludgeons come through the top of the structure` | `Suddenly, two identical stone bludgeons come through the roof` |
| `The dungeon master is taken momentarily by surprise. He dodges your blow, and then, with a disappointed expression on his face, he raises his staff, and traces a complicated pattern in the air. As it completes you crumble into dust.` | `The dungeon master is taken by surprise. He dodges your blow, and with a disappointed expression on his face, traces a complicated pattern in the air with his staff. You crumble into dust.` |
| `He seems not to take notice of you for for a brief moment` | `He seems not to notice you for a brief moment` |
| `After a moment, he starts to smile broadly. He disappears for an instant and the massive door opens without a sound.` | `He starts to smile broadly and opens the massive door without a sound.` |
| `I have seen you display patience in the puzzle` | `You have displayed patience in the puzzle` |
| `He looks you over with his keen, piercing gaze` | `He looks you over with a piercing gaze` |
| `He starts to leave but turns back briefly and wags his finger in warning` | `He wags his finger in warning` |
| `but I fear my waiting may be in vain. I must not give up hope!` | `but I fear my waiting may be in vain.` |
| `and you seem to have made some progress, albeit slight` | `and you seem to have made slight some progress` (Which seems grammatically incorrect.) |
| `and you are coming nearer the end of your long quest` | `and you are nearing the end of your long quest` |
| `and it will not be long yet before you are ready` | `and it will not be long before you are ready` |
| `which rims a large parapet` | `which rims a parapet` |
| `it seems to be more or less bottomless` | `it seems to be bottomless` |
| `On it are an indicator arrow and (in the center) a large button` | `On it are an indicator arrow surrounding a large button` |
| `outside the open wooden door in front of you` | `outside the cell door` |
| `the small window in the closed door in front of you` | `the small window in the closed door` |
| `Your view also takes in the parapet, and behind, a large, fiery pit` | `Your view also takes in the parapet and a large, fiery pit` |
| `The dungeon master is standing on the parapet, leaning on his wooden staff. His keen gaze is fixed on you and he looks somewhat tense` | `The dungeon master is at the parapet, leaning on his staff. His keen gaze is fixed on you and he looks tense` |
| `a small window barred with iron` | `a small barred window` |
| `An additional passage` | `Another hall` |
| `As you gleefully examine your new-found riches` | `As you examine your new-found riches` |
| `Beyond the once-secret door are dark, forbidding stairs that lead down to a passage below. Dim light, as from torches, can be seen in the passage.` | "`Beyond the secret door are dark, forbidding stairs leading down to a passage below. Flickering torchlight illuminates the passage.` |
| `The outline of a door is barely visible on the surface of the rock` | `The outline of a door is barely visible among the runes` |
| `He seems weak and tired, and nods off frequently` | `He looks weak and tired` |
| `He is snoring loudly. From his appearance, he is weak and frail.` | `He is snoring loudly, and looks quite weak and frail.` |
| `He looks up at you and takes the waybread from you.` | `He looks up at you and takes the waybread.` |
| `When you turn back to the old man, you notice that he has gone` | `When you turn back, the old man is gone` |
| `He has bright eyes, which, when open, appear to see right through your body` | `He has bright eyes, which appear to see right through your body` |
| `Some pictures, among the runes, depict flames, stone statues, and figures of old men` | `Some pictures among the runes depict flames, stone statues, and an old man` (The old text seems to suggest that there have been several dungeon masters in the past, while the new text suggests there is only one. Maybe this is a retcon to make it fit better with Zork Zero, and maybe I'm reading far too much into this.) |
| `You don't have enough leverage to turn the T-bar itself. You might cause the whole structure to turn, however.` | `You don't have enough leverage to turn the T-bar. You might be able to turn the whole structure, however.` |
| `On it is a picture of eight small rooms` | `It shows a picture of eight small rooms` |
| `a magnificent room bathed in golden light` | `a room bathed in golden light` |
| `A legend beneath the picture says only` | `A legend beneath the picture says` |
| `wearing a few simple jewels, carrying something under one arm` | `wearing an amulet and a ring, carrying an old book under one arm` (This may have been to make the puzzle easier, but the old text is quoted in the InvisiClues. I think that alone is enough reason to revert this change.) |
| `Mighty stalagmites form structured shapes of rock, encrusted with crystalline formations` | `Mighty stalagmites form crystalline-encrusted rock formations` |
| `Phosphorescent mosses, fed by a trickle of water from some unseen source above` | `Phosphorescent mosses, fed by a trickle of water from above` |
| `Although the origin or purpose of this room is unclear` | `Although the purpose of this room is unclear` |
| `It consists of a curiously flat-headed figure` | `It shows a curiously flat-headed figure` |
| `The iron door appears to be locked from the outside` | `The iron door is locked from the outside` |
| `loose debris starts to fall from above you` | `loose debris falls from above you` |
| `To the east, to the right of the great iron door, a large cleft opens up` | `To the east, next to the great iron door, a cleft opens up` |
| `At once, the channel directly to the` *direction* `of you collapses with its supporting pillar and falls into the chasm` | `Then the channel just to the` *direction* `collapses and falls into the chasm` |
| `full of dust and debris` | `full of debris` |
| `This seems to be an entrance hall of some sort` | `This is an entrance hall of some sort` |
| `providing a western route away from the museum` | `providing an exit from the museum` |
| `a great iron door, which is rusted shut` | `a great iron door, rusted shut` |
| `You experience a brief period of disorientation. The area around you seems to be solidifying! Rock formations close in on you and before you can react you are engulfed in stone!` | `You experience a period of disorientation. The area around you seems to be solidifying! Rock formations close in on you and you become engulfed in stone!` |
| `He appears somewhat pleased` | `He appears pleased` |
| `You start to feel light-headed and, before you can even think, you become completely disoriented. When you regain your faculties, you realize that your surroundings have changed.` | `You start to feel light-headed and quickly become completely disoriented. When your head clears, you realize that your surroundings have changed.` |
| `One, whose IQ could not possibly be above 15` | `One, whose IQ might be 15` |
| `a goodly number of particularly stupid-looking people` | `many particularly stupid-looking people` |
| `in your general direction` | `in your direction` |
| `you see before you a row of military people` | `you see a row of military people` |
| `the cumulative intelligence of a not yet ripe grapefruit` | `the cumulative intelligence of an unripe grapefruit` |
| `One particularly loud and grating voice can now be heard` | `One particularly loud and grating voice can be heard` |
| `I've been thinking and what we need` | `I've been thinking that what we need` |
| `Yes! I can see it now. We shall call it` | `We shall call it` |
| `Aha! It will be Flood Control Dam #3` | `Aha! Flood Control Dam #3` |
| `the voices trail out into nothingness` | `the voices trail out nothingness` (This sounds wrong to me.) |
| `You shut the door quickly and quietly` | `You shut the door quickly` |
| `He grinds his teeth in a most unpleasant way` | `He grinds his teeth unpleasantly` |
| `It moved very quickly` | `It moved quickly` |
| `An odd robot-like device glides into the room` | `An odd robot-like device glides in` |
| `"Hands off, adventurer!" are its last words` | `"Hands off, adventurer!" it says` |
| `Crown Jewels of the Great Underground Empire` | `Crown Jewels of the Empire` |
| `Interestingly enough, he distrusted museum security enough to place his prized possesion, an incredibly gaudy crown, within a locked safe` | `Interestingly, he placed his most prized possesion, an incredibly gaudy crown, in a locked safe` (But "possesion" is a still misspelled.) |
| `time travel device` | `time machine` |
| `torture as the preferred punishment for thieves` | `torturing thieves` |
| `I don't think you'll succeed at this distance.` | `It's unlikely that you'll succeed at this distance.` |
| `It's quicksand allright!` | `It's quicksand all right!` |
| `The Royal Puzzle is quite dangerous` | `The Royal Puzzle is dangerous` |
| `strange and wondrous pictures` | `strange, wondrous pictures` |
| `The text is in a tongue unknown to you and is penned in many colours` | `The text, penned in many colours, is in a tongue unknown to you` |
| `Some of the words seem to change colour as you read them` | `The words seem to change colour as you read them` |
| `You are standing at the southern end of a long, dimly lit hall` | `You are at the southern end of a long hall` |
| `You can see a crossroad to the west, and two nearly identical passages lead east and northeast. A stone channel, wide and deep, steeply descends into the room from the south. It is covered with moss and lichen, and is far too slippery to climb.` | `You can see the junction to the west, and two similar passages to the east and northeast. A wide stone channel steeply descends into the room from the south. It is covered with slippery moss and lichen.` |
| `The channel opening is blocked by tons of debris` | `The opening is blocked by tons of debris` |
| `There are chests here containing precious jewels` | `There are chests containing precious jewels` |
| `several superior scenic views` | `many superior scenic views` |
| `On a desk at the far end of the room may be found stock certificates` | `On a desk at the far end of the room are stock certificates` |

## Gameplay changes

* Some changes seem to have been made to how "`HELLO SAILOR`" is handled, but I couldn't see any functional differences.
* Examining yourself now either prints "`What you can see looks pretty much as usual, sorry to say.`" or "`A good trick, as you are currently invisible.`"
* A typo has been fixed: "suprisingly" is now "surprisingly".
* A typo has been fixed: "dressed in peculiar uniform" is now "dressed in peculiar uniforms".
* Some hyphenation has been removed: "rightly-earned" is now "rightly earned", "non-working" is now "nonworking", "re-enter" is now "reenter".
* A special case has been added for "`THROW ME AT GUARDIANS`". It's almost the same as throwing any other object at them, but with some minor modifications. (The standard message is also slightly different from earlier versions.) But I don't think you can trigger it, because it gets caught by the default message, "`Why don't you just walk like normal people?`" In fact, throwing any object at them seems to be intercepted by the default action.
* I haven't figured out the exact circumstances, but if you tried to walk inside the mirror box there were cases where there would be no response. A "`There is a wall there.`" message has been added. I think this would at the very least happen immediately after first entering the mirror box.
* Rotating the mirror box until the compass rose indicated north would print the correct message followed by a default failure message. This has been fixed.
* The short pole now responds to `MOVE` in addition to `RAISE`.
* When asking the dungeon master to follow you, the game checks if he's in `<LOC ,PLAYER>` rather than `HERE`. This may have to do with talking to him while you're in the cell and he's at the parapet? It also sets `HERE` to `PARAPET` if you send the dungeon master north to the parapet, so that may be related.
* Similarly, there is a case where it now checks if `<LOC ,PLAYER>` is the prison cell, rather than checking `HERE`. Again, I'm not sure exactly why but it's probably to keep it working even if `HERE` refers to the dungeon master's location.
* In some cases when talking to the dungeon master, the game now checks if you're beyond the dungeon door. I don't know why. Is it possible to talk to him outside? I know there used to be a bug that allowed that to happen.
* The panel in the dungeon door is now a proper (local global) object.
* The royal jewels now have `TRYTAKEBIT` so that you can't auto-take them.
* It's no longer possible to open or close the gold machine.
* `MOVE-JEWELS` now moves the pedestal even if the ring has been stolen. If you hid the ring, then used the machine to go back to present time, the pedestal would no longer be in the jewel room. I think this is a fix for that. Though the pedestal also used to be turned invisible when the ring was successfully stolen, and that no longer happens.
* The `REALLY-DEAD` message has been changed from "`**  You have died **`" to "`****  You have died  ****`".
* `REALLY-DEAD` now calls `FINISH` rather than `QUIT`. This is a particularly welcome change since there are random, unavoidable deaths in this area.
* Some unnecessary blank lines have been removed from (and a necessary ones added to) the jewel plaque.
* "`LOOK ON PEDESTAL`" now works. (However, "`LOOK IN PEDESTAL`" claims that the pedestal is empty even when the jewels are on it.)
* When the shadow attacks you, you will automatically regain your sword if you have lost it.
* `HOOD-F` now handles the PUT action the same as the `TAKE` action. I don't know if this was to keep "`PUT HOOD IN CHEST`" from working, but that shouldn't be possible anyway? At least not with the current codebase. All it does now is to handle things like "`PUT CHEST IN HOOD`", which makes no sense. I think there were old versions of Zork where you could put objects you weren't carrying inside containers, and that this led to bugs.
* Similarly, the valuables now handle `PUT` without checking if it's `PRSO` or `PRSI`.
* `CLIFF-BASE` now has a `CLIFF-BASE-F` action routine. The routine existed before, but it wasn't hooked up to the room. The purpose of it is to untie the chest, restore the rope to the cliff, and dequeue `I-MAN-APPEARS`.
* The lake (both above and under water) now has `NONLANDBIT`. I think all that means is that grues won't find you, and if you shake an open container its contents will be lost.
* The chest is still too small for the staff to fit, but now a message has been added for if you try to put it there: "`It doesn't fit. Awfully peculiar, though, since it's where the staff came from.`" I think the chest used to be larger, but was made smaller to keep players from using it to get the torch safely across the lake.
* There is now a "`What valuables?`" response in VALUABLES-F if the man is gone and he has the valuables. But I'm not sure under which circumstances the valuables would still be in scope. Perhaps when using "it" to refer to them, but is that still a thing that happens?
* "`CLIMB ROPE`" from the top of the cliff appears to be broken now. It used to climb down the rope, but now says the rope doesn't lead downward.
* "`BOARD LAKE`" now works the same as "`ENTER LAKE`".
* The repellent now responds to `PUT-ON` as a synonym for spraying, instead of `PUT`. But there are some issues around this that need to be addressed.
* The vial can now be referred to as "gift".
* The dungeon master can now be referred to as "man".

### Infrastructure changes

* The lamp demon has been rewritten, but presumably works the same as before.
* Apparently what's now ACTORBIT used to be `VICBIT` and `VILLAIN`, though not everyone who used to have `VICBIT` was also a `VILLAIN`. VICBIT seems to imply that an object is alive, while a `VILLAIN` is hostile. The sword only glows for `VILLAINS`. Strange though, because I clearly remember the sword glowing when the dungeon master was nearby, and he shouldn't be one. Either way, the distinction seems rather fuzzy, which is probably why it was changed.
* For whatever reason, `I-CLEFT` is now queued in `MAIN-LOOP-1` instead of `GO`, even though other demons still are set up in `GO`. My only hunch is that the delay for `I-CLEFT` is random, and maybe some interpreters hadn't fully initialized the random number generator when `GO` was called?
* A `FIND-WEAPON` routine has been added for use by the standard `V-STAB` routine.
* There are now `FIXED-FONT-ON` and `FIXED-FONT-OFF` routines for turning fixed font on and off.
* An unused `EG-INFESTED?` routine has been removed. As far as I can see, its functionality is covered by the regular `INFESTED?` routine.
* In addition to `ATTACK`, there used to be a `KILL` action. I'm not sure what the difference between the two was.
* `TM-SEAT` is now a vehicle.
* A synonym on VALUABLES has been changed from `TREASU` to `TREASURE`. Given the dictionary resolution I wouldn't think that makes a difference so maybe it was just for clarity?
* The Flathead Ocean is no longer a vehicle.
* Some rooms used to have a single space as their `LDESC`. I guess that's no longer necessary, assuming it ever was.
