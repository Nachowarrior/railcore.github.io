---
title: Railcore Tool Offset Configuration
--- 

So right now the way most of us (who don’t use Piezo sensors >:) set up our Z probe is like this: We home Z, and look at the distance between the nozzle and the deck. We attempt to jog down to it, only to discover we can only go to -.2. We then guess it’s 1.2mm, and we put that in our G31 line in config.g.

```
G31 X0 Y30 Z1.2 P1
```

Then we home Z again, and jog down to Z=0 or until the nozzle touches the bed. We usually use a piece of paper to test with - we want it to JUST drag the paper noticeably. Usually our guess is wrong, and either our paper is pinned to the deck such that it would tear if we dragged on it any harder, or it’s clearly flapping in the breeze. We update our G31 again, and repeat, until we get to the point where it *just* drags the paper.  This becomes our “Z Offset”. 

But what if I told you there is a better way? Well, a “less fiddly” way? 

First things first, **BACK UP YOUR CONFIGURATION**. Next, we need to change a couple of config settings.  We need to change the minimum Z travel to 5mm below 0:

```
M208 S1 Z-5  :set minimum Z travel
```

We need to enable config-override, so we need to add at the end of our config.g file:

```
M501 ; load config-override
```

Make sure that your config.g includes tool selection:

```
T0 ; select first tool
```

And we need to set our G31 Z offset (and Z only) to 0:

```
G31 X0 Y30 Z0 P1
```
  
Once these changes are made, restart the controller (power down and back up is good).Now that it’s back up, home X and Y:

```
G28 X Y
```

Then home Z:

```
G28 Z
```

Now, put your piece of paper on the deck. Jog down until the nozzle JUST touches it and drags it a bit. Check Z - it should say something like -1.2. Now, you want to enter that (as a positive value) as the “offset” for tool 0. (where? specify where this next line goes. also specify what to do if the value is positive)

```
G10 L1 P0 Z1.2 ; set tool offset for tool 0
```

You need to insert this manually in your config-override file the first time, save that file, then reboot. Like this:
(please speficy where the values come from. why is z6.28 in this example?)
```
; Probed tool offsets
G10 P0 Z6.28
G10 L2 P1 X0.00 Y0.00 Z0.00
G10 L2 P2 X0.00 Y0.00 Z0.00
```

The reason is that RRF will only save the tool offset if 1) it's set by probing (which we aren't doing) or 2) it was originally read from the config-override.g file. So we have to put it in there manually and restart the duet. THEN we can make subsequent change permanent with: (in the console?)

```
M500
```

Restart the machine again. Home Z, then jog the nozzle down to the paper. You should find yourself at Z=0 - and you’re done. Each time you reboot your system, the config will load the tool offset saved in config-override by your M500. If you change nozzles (or hot ends) or otherwise do something that will change your tool offset, you can repeat the process, but with one important change:

```
G10 L1 P0 Z0 ; set tool offset to 0
G28 Z
```

THEN jog the nozzle to paper, and set your tool offset. This may seem complicated if you’re not used to manual gcode entry, but after a couple of tries you’ll find it MUCH simpler than adjusting Z offset in G31. (do you have to remove the old g-31 lines? none of this is very clear, please include screen shots for the steps or find a more clear way of explaining how this is done, be specific when it is supposed to go into the console, or which config file, be consistent with each step)
