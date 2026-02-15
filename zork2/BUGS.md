Known bugs/oddities that I don't think are worth fixing. At least not yet.

* In the Bank of Zork, if you throw an object through the curtain it will go through it. If you throw a second object, you will hit your head against it.  
* You can pour water on the burning object in the balloon receptacle to put it out, but it remains untakeable and unreadable.  
* When you order the robot to do something (e.g. "`TURN CAGE`") you may get messages that refer to "you", not the robot.  
* There is code in `WIZARD-CASE-FCN` for the demon to smash the Wizard's trophy case, but I don't think there's any way for that to ever happen.  
* There is code in `REPELLENT-FCN` to spray the repellent at nothing in particular, but I don't think that can ever be triggered.  
* The baby sea serpent has an `LDESC`, but it's never used since it's in the aquarium.  
* The baby sea serpent has code for "`PUT SERPENT IN` *object*" that I don't think can ever be triggered.  
* In addition to `WATER`, the game also has `SALTY-WATER` and in early versions you can fill the pot with it in the Pool Room. But it appears to have never been fully implemented. Drinking it printed garbage, and trying to use it for the bucket doesn't seem to work at all. So r48 removed `GLOBAL-WATER` from the Pool Room. Is it worth fixing up the implementation? Water is a bit convoluted already...
* If the dragon is following you, and you are standing in one of the rooms he won't follow you into, you will see the message "`The dragon will follow no further.`" every turn until he loses interest.
