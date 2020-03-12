# STEP 3 - Submitting an app

<span style="color:red">:warning: This tutorial is out of date for 2v05 and later Bangle.js firmwares. Check out https://www.espruino.com/Bangle.js#tutorials instead:warning:</span>

* Fork https://github.com/espruino/BangleApps
* You can either edit it using GitHub or can clone locally and edit
* To test, enable 'GitHub Pages' on the `master` branch in GitHub settings

* First, make sure the 7 (or less) character ID you chose in Step 2 isn't already in https://github.com/espruino/BangleApps/tree/master/apps - we were using `timer`
* Now add the JSON you had (without `.write`) to `apps/timer/my-timer.json`
* Now, add the JS you had (without `.write`) to `apps/timer/my-timer.js`
* For the UI, let's add an icon - most icons are 48px. A quick, easy
source of these is https://icons8.com. Most icons used are from the 'Color'
group, for example https://icons8.com/icon/19613/sand-timer
* Download the icon and save to `apps/timer/my-timer.png`
* And now, we need to describe it for the UI. Open `apps.json` and add:

```
{ "id": "timer",
  "name": "My Timer",
  "icon": "my-timer.png",
  "description": "An Analog Clock",
  "tags": "",
  "type":"app",
  "storage": [
    {"name":"+timer","url":"my-timer.json"},
    {"name":"-timer","url":"my-timer.js"},
  ]
},
```

More info on what's allowed here is at https://github.com/espruino/BangleApps#developing-your-own-app

But you're basically good to go. If you updare your repo and go to https://yourusername.github.io/BangleApps you can now try your own personal app loader and see if it uploads your app properly. If it does, you could issue a PR!

## Icons!

We added an icon for the UI, but not for the watch itself. The watch doesn't have a PNG decoder (and doesn't have much memory), so we need to convert the image to a low bits-per-pixel version first.

* Head to http://www.espruino.com/Image+Converter
* Upload the png icon you saved to `apps/timer/my-timer.png` - it should be 48px
* Choose `4 bit Mac palette` from the 'Colours' drop down
* If the preview looks bad, experiment with 'Diffusion' settings, or worst case you can
move to `8 bit Web Palette` but this will use twice as much memory
* Choose `Image String` from `Image Object`
* Click `Use Compression`

Now copy the `require("heatshrink").decompress(atob(...))` text (but not `var img =`)
and paste it into a file called `apps/timer/my-timer-icon.js`

Add this line to `apps.json` under `"storage":`

```
    {"name":"*timer","url":"my-timer-icon.js","evaluate":true}
```

And you're done! Your app now has an icon!
