# [OWB - The Think Tank](https://steamcommunity.com/sharedfiles/filedetails/?id=3342313594)
*A submod for [Old World Blues](https://steamcommunity.com/sharedfiles/filedetails/?id=2265420196)*

![Steam Subscriptions](https://img.shields.io/steam/subscriptions/3342313594)
![Steam Favorites](https://img.shields.io/steam/favorites/3342313594)
![Steam Update Date](https://img.shields.io/steam/update-date/3342313594)

## Update process

1. Get [WinMerge](https://winmerge.org/)
2. Go through every file in the mod and compare to OWB files, updating mod files.
   Don't worry about being too thorough here, just check every file for obvious easy things to
   update, smaller thhings can be fixed in play testing.
  1. Set 1st folder as `C:\Program Files (x86)\Steam\steamapps\workshop\content\394360\2265420196\`
  2. Set 2nd folder as `tnkd\mod_folder\tnkd\`
  3. Untick `View > Show Left Unique Items`.
  4. **Optional:** Right click each item, and click `Hide Item` as you go through them to keep track of progress.
  5. **Optional:** If you need to move a file, copy the contents, delete the old file, create the new file in the mod, then right click and `Refresh Selected` the subdirectory with the file in winmerge.
  6. Delete the `.bak` files after.
3. Play game to test.
4. Done.

## Plan

### Very High Priority

- add decisions to buff popularity of each political party (probably lock them behind some focuses).
- need to do another playthrough to re-balance things
- balance `tnkd_next_steps` stability and war support requirements so they are difficult to meet and take some time and focus to get
- focus icons for attacking tar walkers and ouroboros are duplicated and wrong. Fix this.
- tnk and lul when ai controlled seem to never train new units likely bug in base mod, this should be fixed in recent update, test this.

### High Priority

- add focuses that give civ/mils in capital (maybe some missions that allow economic raids with neighbours to steal civs/mils)
- re-industrializing the Mojave should be similar to convergance in that it should be a mission and various events that give buffs.
- balance robo-scorpions and other units so think tank is forced to have a varied army e.g. also using infantry (lobotomites).

### Medium Priority

- add these guns https://fallout.fandom.com/wiki/Category:Old_World_Blues_weapons
- change convergance to be a decision category with a bunch of missions that spawn negative events related to the convergance (e.g. lobotomites escapes, robots self destruct, factories deconstructs itself, etc.) to give a more impactful feeling of things falling apart.
- allow same usage of archimedes as Mojave Chapter has
- G.E.V should have a nice decision category image
- the carrot focus should trigger an event for each neighbour where we can decide if we want to give them the offer (it doesn't make sense to give an evil faction the offer)
- update ncr and ces peace deal decisions to instead be missions which start when capturing capitols then when timing out trigger the peace deal event (where you choose if peace)
- should add 1 more focus and/or mission before giving claim against each small neighbor that allows a mission to destabilize them so you can beat them early and early war is more viable
- vegas platinum chip focus should require that vegas has completed their platinum chip focus (or has it via some other check)
- need to add more motivation to engage in earlier wars and have fun as tnk should add extra expeditions which need wars and gives clear good rewards
- add content that is works with the enclave mod
  - if player is playing enclave have think tank spawn, take-over mojave from whoever (brotherhood, ncr, ceasar, vegas) and basically be a very strong turtley vegas that blocks enclave from hoover dam making their life a bit harder, maybe also add fun decisions category.
  - if player is think tank they can follow a focus sub-tree that decides whether to fight or work with enclave

### Low Priority

- when triggering the broken mojave event which the "Almost makes you wish for a nuclear winter" it would be fun to play an audio clip of the npc saying "patrolling the mojave almost makes you wish for a nuclear winter".
- Make AI Think Tank more interesting
  - Always beats Mojave Brotherhood
  - Spawn with more units
  - Code AI path through focus tree
- add a blowing up the hoover damn decisions/focus (bc whole point is for the think tank is that they fck around and find out)
- add decisions that allow temporarily occupying territory for expeditions without declaring war
- add more NCR/CES targeted buffs for TNK to help win but not be op
- a new countries (aliens, dwemer and vikings) for expeditions paths
- add NCR and CES war reparation as a mission that decrease industry/military given to TNK over time and they can take decisions to decrease it more.
- use better decisions/mission icons
- should add idea to NCR and CES that prevents them manually declaring war on TNK, should remove after mission timer runs out
- should add later focus after allying vegas that allows backstabbing them
- add more narrative events, could add a diplomatic tree where you send crazy robots to annoy other people and give them small debuffs or buffs could be funny
- allying Vegas should give them robotics buff due to think tank knowledge
- when ces or ncr no longer exists the decisions category for each shouldn't appear
