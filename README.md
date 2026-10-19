# Lab 05 — Misbehaving Balls

In class we wrote a `Ball` class: a blueprint with attributes (`x`, `y`, `color`, `r`) and methods (`move()`, `changeSize()`, `show()`). `change()` calls the other three. Then we made 5000 balls with a loop and put them in an array.

In this lab you **add two new methods** to `Ball` that make the balls do something fun. Then you put it on your website.

## Start from the in-class sketch

Use your own code from class (`sketchAgain.js`). If it isn't working, use the [`starter/`](starter/) folder:

```
starter/
└── sketch.js         the in-class Ball sketch, ready for your website
```

1. In your website repo, copy your `lab-01/` folder and rename the copy `lab-05`.
2. On this repo's GitHub page, click the green **Code** button, then **Download ZIP**. Unzip it.
3. Copy `sketch.js` from `starter/` into `lab-05/`. Let it replace the old `sketch.js`.
4. Open `lab-05/index.html` with Live Server. You should see a line of teal balls that jitters and slowly spreads into a cloud, before you change anything.

## What to add

Add **two new methods** to the `Ball` class. Each one makes the balls do something you think is fun. They should do different things.

Your methods have to:

- Live **inside the class**, next to `move()` and `changeSize()`. No `function` keyword.
- Have **at least one parameter**, like `byHowMuch`, so you can turn the effect up and down.
- Get called for every ball, every frame. Call them from `change()`, before `this.show()`.
- Have a name that says what they do: `followMouse()`, not `thing2()`.

Some ideas. Pick two, or invent your own:

- **Chase** the mouse. Or run away from it.
- **Pulse**: the balls breathe, bigger and smaller.
- **Change color** over time, or when they are close to the mouse.
- **Gravity**: the balls fall and bounce.
- **Swirl** around the middle of the canvas.
- **Wrap**: leave one side, come back on the other.

<details>
<summary><b>Hint 1:</b> where does a method go, and how do I call it?</summary>

A method is a function that lives inside the class. Put it next to the others, and call it with `this.` from `change()`:

```js
change(byHowMuch) {
  this.changeSize(byHowMuch);
  this.move(byHowMuch);
  this.stayOnCanvas();   // your new method gets called here
  this.show();
}

// keeps the ball inside the canvas
stayOnCanvas() {
  this.x = constrain(this.x, 0, width);
  this.y = constrain(this.y, 0, height);
}
```

`stayOnCanvas()` is only an example. It doesn't count as one of your two.

</details>

<details>
<summary><b>Hint 2:</b> chase the mouse</summary>

`lerp(a, b, t)` slides from `a` toward `b`. `t` is how far: `0` stays put, `1` jumps all the way. Small numbers are smooth:

```js
followMouse(amount) {
  this.x = lerp(this.x, mouseX, amount);
  this.y = lerp(this.y, mouseY, amount);
}
```

Call it with `this.followMouse(0.02)`. Try `0.005` and `0.2` to see the difference.

To run away instead, or to react only when the mouse is close, use `dist()`:

```js
let d = dist(this.x, this.y, mouseX, mouseY);
if (d < 100) {
  // do something
}
```

</details>

<details>
<summary><b>Hint 3:</b> pulse. They all breathe at the same time</summary>

`sin()` goes smoothly between `-1` and `1`. Feed it `frameCount` and it keeps going:

```js
pulse(amount) {
  this.r = 15 + amount * sin(frameCount * 0.05);
}
```

Every ball is in the same part of the wave, so they all breathe together. Give each ball its own starting point. A ball needs a new **attribute** for that. Add this line to the `constructor`:

```js
this.offset = random(TWO_PI);
```

Then use it: `sin(frameCount * 0.05 + this.offset)`.

Your method runs after `changeSize()`, so it wins. If the size still looks jittery, that's `changeSize()` fighting with you.

</details>

<details>
<summary><b>Hint 4:</b> change color</summary>

`lerpColor(c1, c2, t)` works like `lerp()`, but for colors:

```js
turnPink(amount) {
  this.color = lerpColor(this.color, color(255, 80, 120, 180), amount);
}
```

With `amount` at `0.02`, the balls slowly turn pink. What if the target color depended on `mouseX`?

</details>

<details>
<summary><b>Hint 5:</b> gravity and bounce</summary>

A falling ball needs to remember its speed. That's a new attribute. Add this to the `constructor`:

```js
this.vy = 0;
```

Then:

```js
fall(gravity) {
  this.vy += gravity;
  this.y += this.vy;
  if (this.y > height) {
    this.y = height;
    this.vy *= -0.8;
  }
}
```

Call it with `this.fall(0.2)`. The `-0.8` flips the direction and loses a little energy on every bounce. Try `-1`.

</details>

### Bonus

- Pass something from `draw()` into your method, like `mouseX`, instead of a fixed number.
- Only run a method **while the mouse is pressed** (`mouseIsPressed`).
- Write a **third** method.
- Make some balls different from others. `if (this.x < width / 2)` is a start.

Weird is good. Have fun with it.

Put a **comment** above each method you add that says what it does, in your own words.

## How to hand it in

1. Edit `lab-05/index.html`: change the title and the `<h1>` to Lab 05, and write a sentence or two about what your two methods do.
2. Add a link to it on `projects/iat-806/index.html`.
3. Commit and sync, then check that it works at:

```
https://your-username.github.io/projects/iat-806/lab-05/
```

In **Canvas**, submit:

1. The link to your live sketch.
2. The link to your `lab-05` folder in your GitHub repo.

Need a reminder on adding a folder to your site? See [section 4 of the personal-website README](https://github.com/IAT-806/personal-website#4-add-a-submission).

## If something doesn't work

Open the browser console (right-click the page → **Inspect** → **Console**) and read the red text.

- **`this.something is not a function`**: the name doesn't match, or the method is outside the class's `{ }`. Check spelling and capital letters.
- **Nothing changes**: you wrote the method, but nothing calls it. Add `this.yourMethod(...)` to `change()`.
- **The balls vanish**: probably `NaN`. You used an attribute (like `this.vy`) that you never created in the `constructor`. Add `console.log(this.vy)` and look.
- **It's slow and laggy**: lower `numBalls` at the top of the sketch.
- **Works with Live Server but not on GitHub Pages**: check capital letters in file names.
