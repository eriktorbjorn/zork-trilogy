Known bugs/oddities that I don't think are worth fixing. At least not yet.

* \[Cree-28] You can refer to the teeth as "overboard". That's just how "`THROW` *object* `OVERBOARD`" is implemented.
* \[Nathan-52] "`EXAMINE WALL`" will ask if you mean the granite wall or the surrounding wall. This is because the granite wall is a global object, even though it's only used in a few rooms. Changing this would change the default message for interacting with the granite wall. Is it still worth doing that?
* \[Nathan-55] While I have addressed a couple of cases where you could interact with things inside the closed, transparent bottle, I'm sure there are cases where you still can do it.
* \[Nathan-58] There are places where jumping will kill you, even though it makes little sense. The game checks if there is a non-exit down, and assumes it's a hole to jump into.
* \[Nathan-61] Objects left in the reservoir are not removed when you refill it. Only the trunk is hidden. We don't have any mechanism to tell which objects float and which do not. Actually, `RIVER-FUNCTION` does use `BURNBIT` to indicate that an object floats a little better than others, but...
* \[Nathan-64] You can shatter the bottle against a wall, even if there are no walls. Mainframe Zork had an `RNWALLBIT`, but Zork I does not.
* \[Nathan-66] Destroying a container destroys its contents. In some cases that makes sense, in others... I don't know.
* There is some unreachable code in `TREE-ROOM` that would kill you if you jump. But there is also code in `V-LEAP` that tells you that you survive the jump. So which is the correct behavior? Probably the current one, but it might be fun to enable the unreachable code if `LUCKY` is false.
* There is a custom message for `EXORCISE` in `GHOSTS-F`, but it probably can't be reached because the action is handled by `LLD-ROOM` instead.
* `GAS-PSEUDO` handles the `BREATHE` action, which means that you can "`BLOW IN GAS`" for a "There is too much gas to blow away." response. Is there any more sensible way to trigger that?
* The forest area is a bit inconsistent about which rooms have `FOREST`, `TREE`, and `SONGBIRD` among their global objects.
* You can't carry the inflated boat from `WHITE-CLIFFS-NORTH` to `DAMP-CAVE` because the passage is too narrow for it. But you have no problem carrying it from `DAMP-CAVE` to `WHITE-CLIFFS-NORTH`. I don't want to change that because of how unspecific the failure message is.
* There is a whole set of combat messages for the cyclops, but there is no way to see them. (*Very* early versions of Zork I would show your combat messages if you tried to fight the cyclops, but that was probably a bug.)
* `CHIMNEY-FUNCTION` is a bit strange in that you apparently *must* carry the lamp to be able to climb up. The torch won't do.
* `CLEARING` (the one without the grating) has `FOREST-ROOM` as its `ACTION`, so it will start the `I-FOREST-ROOM` demon. But it's not a forest room according to the `FOREST-ROOM?` routine, so it won't run. I guess the action routine is only used for its response to climbing a tree.
* There is a custom death for "`PUT ME IN RIVER`" that's never triggered. Then again, if you try to swim it says you don't.
* Some objects break when thrown, but they generally don’t do that if the object you throw them at handles the action first.
* \[Monica Morse] There are oddities in the fighting mechanics. Using the best weapon against an opponent seems to give an unreasonable advantage, while the `THIEF-ENGROSSED` mechanic seems to give no advantage at all. I thought I had a fix for the latter, but then it gives too much of an advantage.

