# BountyBot

## Description

**BountyBot** is a [Slack](https://torpedodelivery.slack.com/messages) integration which is used for keeping track of the specific and generic wormhole orders. You can interact with *@bountybot* by issuing commands in the `#wormhole-sales` channel or by opening up a direct channel. Opening a direct channel is the preferred way of executing commands.

The commands may start with: `!bb`, `!bounty` or `!bountybot`. The arguments within angle brackets `<>` are mandatory. The arguments within square brackets `[]` are optional. Note: you don't have to type the brackets; those are there to signify something has to be inserted instead.

Additionally, BountyBot periodically checks for kills in bounty wormholes and reports them in `#bountybot-report` channel on Slack as soon as the killmails are retrieved from *Zkillboard*.

Specific and generic bounty systems adjacent to Thera are also reported using Eve Scout API (credits to [Signal Cartel](http://www.eve-scout.com/)). Specific orders are reported in `#bountybot-report`, while generic orders are reported in `#wormhole-sales`.

**NOTE**: Adding, editing or removing specific wormholes as an admin will automatically modify Tripwire comments as needed using the name [BountyBot](https://gate.eveonline.com/Profile/BountyBot). Please do not modify or delete automatically generated comments made by *BountyBot* in Tripwire.

## Commands

Typing `!bb help` in a *@bountybot* public or direct channel will output:
```no-highlight
!bountybot help  -- displays this message
!bountybot about  -- displays information about BountyBot
!bountybot hello  -- simple verification which tests if BountyBot is up and running
!bountybot check <jcode>  -- verify if J-code is in the specific or generic orders list
!bountybot list [generic/jcode/jcode+] [id]  -- displays current bounty systems (systems with the ~ symbal have kill reports disabled)
!bountybot generic <id>  -- displays J-codes associated with generic of specified ID
!bountybot info <jcode>  -- displays characteristics of a wormhole
!bountybot search <description>  -- displays J-codes which match the description
!bountybot static <code>  -- displays information on a static code (ex. D382)
```

Typing `!bb help` in a *@bountybot* configuration channel (only for admins) will output additional commands:
```no-highlight
!bountybot add <jcode> <watchlist> <comments>  -- add new bounty system to database; <watchlist> must be either true or false
!bountybot add generic <description>  -- add new generic bounty system
!bountybot remove <jcode>  -- remove bounty system from Watch List
!bountybot remove generic <id>  -- remove generic bounty system
!bountybot edit <jcode> <watchlist> [new_comments]  -- modify the comments of a specific wormhole; <watchlist> must be either true or false
!bountybot edit generic <id> <new_description>  -- modify the description of a generic wormhole
!bountybot destroy [generic/jcode]  -- CAUTION! removes all [generic/jcode] bounty systems
!bountybot echo <channel> <message>  -- send a message as Bounty Bot
!bountybot announce <message>  -- make an announcement as Bounty Bot
```

Example of commands as a normal user:
```no-highlight
# check if @bountybot is online
!bb hello

# listing the current bounty wormholes - systems with symbol * have kill reports enabled:
!bb list  # display all orders
!bb list generic  # display only generic orders
!bb list jcode  # display only specific orders
!bb list jcode+ # will output a more detailed list of specific wormhole orders
!bb list generic 33  # display information on generic order 33 (if existing)
!bb list jcode J123450  # display information on specific order J123450 (if existing)

# check if a wormhole is in specific order or generic order list
!bb check J123450
!bb check J123450 J101145  # multiple systems check

# retrieves information about a wormhole: class, effect, statics, radius, planets, moons (also tells if wormhole has perfect P.I.):
!bb info J000313
!bb info J123450 J000313 J101145  # multiple systems info

# lists all J-codes which are associated with generic order ID 1 (!bb list generic -- to view generic order list):
!bb generic 1

# searches the database for wormholes which have the following characteristics
!bb search C3; HS static; perfect P.I.
!bb search C2 or C3 non-shattered; effect Pulsar; LS or NS static; lava-2

# displays information on a wormhole connection (destination, stable time, max jump, max mass)
!bb static D385
!bb static B735
```

Example of commands which can be executed in the configuration channel:
```no-highlight
!bb add J123450 true Contact Apollo Tyrannos when found
!bb add J000313 false Missing Tripnull  # add a Tripnull to database, but without kill reports
!bb add generic C3 non-shattered; LS static; no effect

!bb remove J123450
!bb remove generic 3    # the Id of the generic can be obtained by listing the generics with !bb list generic

!bb edit J123450 true New comments
!bb edit J123450 false System held by X  # disables kill reports for J123450 and update comments
!bb edit J123450 false   # disables kill reports for J123450 and preserve previous comments
!bb edit generic 7 C4 or C5; C4 static; Uninhabited

# careful when executing following commands, they clear the database:
!bb destroy
!bb destroy generic
!bb destroy jcode

# send messages as Bounty Bot
!bb echo general BountyBot will go to sleep
!bb announce New wormhole added. Start scanning!
```

## Generic Format

The description is split into groups by a delimiter character: `;`. The description must contain info about the class. The others are optional: effect, statics, moons, planets, radius, comments. The class group must come in first. The others don't have to be in order. Comments are usually in the last group (if any).

Every group except the class group and the comment group must have the specific keyword: "effect/effects", "static/statics", "moon/moons", "planet/planets/p.i.", "radius". Lower-case or upper-case does not matter.

Optional keywords in the first group (class): "shattered", "non-shattered", "all", "drifter", "tripnull", "sansha". The keyword `exclude` can be placed in the effects and statics groups to exclude certain characteristics. For the planets group you can specify: "perfect" for perfect P.I. Otherwise you have to name the planets:

    lava-1 storm-1 or lava-2 - means find a wormhole with at least (1 lava and 1 storm) or (2 lava planets).

Remember to place the keyword "or" if multiple subgroups are defined. You can also specify how many planets you want by using the range construction:

    C3; planets 9-12

Always specify "non-shattered" parameter in the class group if you want a non-shattered wormhole. The parameter "non-shattered" must NOT be specified if there is a moon group or a planet types group.

### Planet keywords:
```no-highlight
temperate-1 equivalent with t-1
ice-1 equivalent with i-1
gas-1 equivalent with g-1
oceanic-1 equivalent with o-1
lava-1 equivalent with l-1
barren-1 equivalent with b-1
storm-1 equivalent with s-1
plasma-1 equivalent with p-1
shattered-1 equivalent with sh-1
```

### Effect keywords:
```no-highlight
black hole
cataclysmic
magnetar
no effect
pulsar
red giant
wolf-rayet
```

### Examples:
```no-highlight
# The following 2 commands are equivalent:
!bb search C2 or C3; effects exclude Magnetar; statics LS or NS; radius 0-14 AU; lava-1 storm-1 barren-1; uninhabited
!bb search C3, C2; exclude Magnetar effect; statics NS or LS; radius 0-14; l-1 s-1 b-1; uninhabited

# C3 with high-sec or low-sec static, no effect and perfect P.I. (the following 2 commands have the same results):
!bb search C3; exclude NS static; no effect; perfect P.I.
!bb search C3; HS or LS static; no effect; perfect P.I.
# Note: Please be aware that saying "HS or LS static" is not the same as "HS, LS static".
# In the last case both HS and LS must be present

# Display all wormholes which have exactly 1 moon
!bb search class all; moons 1-1

# Display all wormholes which have exactly 1 planet
!bb search class all; planets 1-1

# display shattered C3's with HS static
!bb search C3 shattered; HS static

# display drifter wormholes and tripnulls
!bb search class drifter
!bb search class tripnull

# display non-shattered wormholes which have at least one shattered planet
!bb search class all non-shattered; planets shattered-1
```

## Contribute
BountyBot is written in python and is based on Slack's RTM Bot: <https://github.com/slackhq/python-rtmbot>. Code is open source and can be found here: <https://github.com/farshield/bountybot>

Please send any ideas/suggestions, bug reports to *Valtyr Farshield* (in-game or on Slack: *@farshield*)

Fly safe o7
