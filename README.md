# Zork Trilogy Source Code Collection

Zork was created at the MIT Laboratory for Computer Science, by Tim Anderson, Marc Blank, Bruce Daniels, and Dave Lebling. Severalversions of this have been preserved elsewhere.

The Zork Trilogy, as released by Infocom, is mainly credited to Dave Lebling and Marc Blank. This source code has been extracted from the following repositories:

* https://github.com/historicalsource/zork1
* https://github.com/historicalsource/zork2
* https://github.com/historicalsource/zork3

What we have here are:

| Directory | Description |
| --------- | ----------- |
| zork1-r88 | This probably corresponds to the last published version of Zork I, serial number 840726. |
| zork2-r48 | This probably corresponds to the last published version of Zork II, serial number 840904. |
| zork3-r17 | This probably corresponds to the last published version of Zork III, serial number 840727 |
| zork1-r119 | This is the last known snapshot of the Zork I source code. |
| zork2-r63 | This is the last known snapshot of the Zork II source code. |
| zork3-r25 | This is the last known snapshot of the Zork III source code. |
| zork1 | The latest version of Zork I, with bugfixes and buildable with ZILF. |
| zork2 | The latest version of Zork II, with bugfixes and buildable with ZILF. |
| zork3 | The latest version of Zork II, with bugfixes and buildable with ZILF. |
| zork-common | Common library files for Zork I-III. |

The Makefiles are written for ZILF 1.0 or later, where it is no longer necessary to invoke ZAPF manually. The source code itself should compile with earlier versions.

By "last published version", I mean the versions included on the Masterpieces of Infocom CD and the digital Zork Anthology sold on GOG and Steam. But it's hard to say for certain if they're exact matches to those. The last known snapshots shows that there was an effort to build a common library for all Zork games, with parts of it conditionally compiled depending on `ZORK-NUMBER`. They're known to contain several bugs compared to the official versions, but also some new features. For instance, the Wizard of Frobozz interacts better with dark rooms.

I am aware that some older versions of the source code have also been preserved, but these were the only ones (that I know of) that have been explicitly been relicensed under the MIT license.

I'm only including the ZIL code. Of course the other files, usually generated as part of the build process, have historical value, but they're preserved elsewhere.
