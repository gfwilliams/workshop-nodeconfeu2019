# STEP 2 - Making an app

<span style="color:red">:warning: This tutorial is out of date for 2v05 and later Bangle.js firmwares. Check out https://www.espruino.com/Bangle.js#tutorials instead:warning:</span>

We're now connected and can write some simple code - let's have a go at making a timer app.

To do this, it's best to use the right-hand side of the IDE - once uploaded you can tweak values and call functions using the REPL on the left if you want to.

Copy the following code to the right of the IDE and click upload:

```
var counter = 30;

function countDown() {
  counter--;

  g.clear();
  // draw the current counter value
  g.drawString(counter, g.getWidth()/2, g.getHeight()/2);
  // optional - this keeps the watch LCD lit up
  g.flip();
}

var interval = setInterval(countDown, 1000);
```

You'll now have some tiny text in the middle of the screen, which counts down.

First, we'll want to make the text bigger, and properly centered. You have
two options here - a bitmap font, or a vector one.

Add:

```
g.setFontAlign(0,0); // center font
g.setFont("6x8",8); // bitmap font, 8x magnified
```

Just after `g.clear()` and re-upload. You'll now have a bigger,
but pixellated, number in the center of the screen.

If you'd prefer something smooth, use the Vector font, 80px high:
`g.setFont("Vector",80)`.

Finally, maybe we want to detect when the counter hits zero,
display a message, and beep:

```
var counter = 30;
var counterInterval;

function outOfTime() {
  E.showMessage("Out of Time");
  Bangle.buzz();
  Bangle.beep(200, 4000)
    .then(() => new Promise(resolve => setTimeout(resolve,200)))
    .then(() => Bangle.beep(200, 3000));
  // again, 10 secs later
  setTimeout(outOfTime, 10000);
}

function countDown() {
  counter--;
  // Out of time
  if (counter<=0) {
    clearInterval(counterInterval);
    counterInterval = undefined;
    outOfTime();
    return;
  }

  g.clear();
  g.setFontAlign(0,0); // center font
  g.setFont("Vector",80); // vector font, 80px  
  // draw the current counter value
  g.drawString(counter,120,120);
  // optiona; - this keeps the watch LCD lit up
  g.flip();
}

counterInterval = setInterval(countDown, 1000);
```

Now we have this, we need to turn it into an app for the watch. The
simplest way to load it in right now is to wrap it in a 'Storage.write'
command to write it into flash memory - the Web IDE will get extended
at some point in the future to make this easier...

First, come up with a unique 7 character (or less) ID for your app. Ideally
it shouldn't already be listed in https://github.com/espruino/BangleApps/tree/master/apps

I'll use `timer`. Now, use a templated string to wrap
your code up:

```
require("Storage").write("-timer",`
...your code...
`);
```

**NOTE:** You have to be careful with escape sequences in Strings here. If you
use templates strings (or escape characters) in your code you can hit issues
here and you'll need to add `\` to escape them. For example:

```
`print("Hello\nWorld")`
print("Hello
World")

`print("Hello\\nWorld")`
print("Hello\nWorld")

`print(`Hello World`)`
// syntax error

`print(\`Hello World\`)`
// ok
```

And then we need some JSON to tell Bangle.js's menu
what the app is called and what to run:

```
require("Storage").write("+timer",{
  "name":"My Timer",
  "src":"-timer"
});
```

Now just click upload. When the progress bar for upload
is complete, long-press the bottom button to force a
restart.

You can now press the middle button to enter the menu
and you should see an app called `My Timer`.

To exit the timer, just long-press the bottom button gain!


Proper documentation on what you can put in the JSON is at:
https://github.com/espruino/BangleApps#developing-your-own-app

And you can also look at the apps in that repo for examples.

Put this all together:

```
require("Storage").write("+timer",{
  "name":"My Timer",
  "src":"-timer"
});
require("Storage").write("-timer",`
var counter = 30;
var counterInterval;

function outOfTime() {
  E.showMessage("Out of Time");
  Bangle.buzz();
  Bangle.beep(200, 4000)
    .then(() => new Promise(resolve => setTimeout(resolve,200)))
    .then(() => Bangle.beep(200, 3000));
  // again, 10 secs later
  setTimeout(outOfTime, 10000);
}

function countDown() {
  counter--;
  // Out of time
  if (counter<=0) {
    clearInterval(counterInterval);
    counterInterval = undefined;
    outOfTime();
    return;
  }

  g.clear();
  g.setFontAlign(0,0); // center font
  g.setFont("Vector",80); // vector font, 80px  
  // draw the current counter value
  g.drawString(counter,120,120);
  // optional - this keeps the watch LCD lit up
  g.flip();
}

counterInterval = setInterval(countDown, 1000);
`);
```
