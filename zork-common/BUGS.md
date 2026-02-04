Known bugs that I don't think are worth fixing. At least not yet.

* "`GIVE` *object* `TO ME`" is supposed to be a synonym for "`TAKE` *object*". This doesn't work, because you can only give objects that you are holding. This also makes it hard to trigger the "That's easy for you to say..." message in `PRE-GIVE` though "`GIVE HANDS TO` *object*" will still do that, in a somewhat nonsensical way.  
* `V-SPRAY` simply calls `V-SQUEEZE`. The purpose may be to reuse "How singularly useless.", but it also means that "`SPRAY ME ON` *object*" or "`SPRAY` *object* `WITH ME`" will print the nonsensical message "The you does not understand this."  
* `V-OVERBOARD` is a bit weird for Zork II and III.  
* Objects inside closed, transparent containers are sometimes reachable when they shouldn't be. `V-PUT`, `V-BURN`, ... This is probably only a problem in Zork I, which has a glass bottle.  
* It seems that earlier versions of Zork didn't enforce the `HAVE` grammar token, e.g. you in Zork III you could "`PUT SHORT ROD IN CHANNEL`" in the mirror box or, much easier to test, "`SWING` *object*" even when it's on the ground. I think this is handled by `ITAKE-CHECK`, which used to only check the `STAKE` bit but now additionally checks the `SHAVE` bit. Even if some of these rules would still be enforced by pre-checks, I'm not comfortable changing this back. (I found this while looking at the Zork III special case in `PRE-PUT`.)  
* There are cases where the game assumes that the `DROP` verb only has a `PRSO`, but "`POUR` *object* `IN` *object*" and "`POUR` *object* `FROM` *object*" are also implemented as `DROP`. I may fix some of these when I see them.

