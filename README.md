# Light Check

Your alarm is not off until the room is actually bright.

A single static page that uses the phone camera as a light meter. It will not
clear until it sees the room genuinely brighten, which means you have to stand
up and hit the switch. Live: https://rs280.github.io/light-check/

## Wiring it up on iOS

In the **Shortcuts** app:

1. **Automation** tab -> **+** -> scroll to **Alarm**
2. Pick your wake-up alarm, choose **Is Stopped** -> **Next**
3. **New Blank Automation** -> **Add Action** -> **Open URLs** -> paste the live URL
4. Tap **Run Immediately**, turn **Notify When Run** off -> **Done**
5. Repeat the whole thing with **Is Snoozed**, so snoozing costs you too

## Why it calibrates first

iOS auto-exposure fights you here. A pitch-dark bedroom does not read as black:
the camera cranks the gain until it reports something mid-grey, so a fixed
brightness threshold would either pass in the dark or never pass at all.

So the page spends ~2 seconds measuring your actual dark room, takes the median
as a baseline, and then requires a rise of 45 luma points above *that* (or an
absolute 200, whichever comes first), sustained for 1.4 seconds. Flipping a
light produces a spike far larger than that before auto-exposure settles back,
so the jump is easy to catch while a dark room sitting still never trips it.

## Fallbacks

- **No camera or permission denied** -> hold a button for 20 continuous seconds.
  Let go and it resets. Long enough that going back to bed is a decision.
- Screen Wake Lock keeps the display on while the check runs.
- A wake is logged to localStorage once per day to keep a streak.

Nothing leaves the phone. No frames are stored, uploaded, or shown; only the
average brightness of a 64x48 downsample is ever computed.

## If you own smart bulbs

There is a stronger version that needs no camera: in Shortcuts, follow the alarm
trigger with **Get State of Home accessory**, check whether the bulb is on, and
loop until it is. That is a real check on a real device rather than an inference
from a camera. It only works if the light is HomeKit-connected.
